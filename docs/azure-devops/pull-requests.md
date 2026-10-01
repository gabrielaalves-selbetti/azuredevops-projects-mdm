# Pull requests

O pull request (PR) é o pedido para integrar uma branch a outra. É nele que o código é revisado antes de entrar na `dev` e, depois de homologado, na `main`. Ele deve estar vinculado ao work item que resolve, bem descrito e pronto para revisão.

## Título

Padrão: `[Tipo] Breve descrição da funcionalidade`

```text title="Exemplo"
[Feature] Validar dígito verificador de CPF na higienização
```

O título do PR normalmente repete o título do work item.

## Descrição

```markdown title=".azuredevops/pull_request_template.md"
## O que foi feito?

Descreva de forma clara e objetiva o que foi desenvolvido ou alterado.

## Impacto nos dados

Regras, tabelas ou consumidores afetados. Exige reprocessamento?
Resultado esperado nas métricas (ex.: inválidos, duvidosos, Golden Records).

## Como testar?

Passos para reproduzir o funcionamento do PR localmente.

## Evidências

Resultados de testes e métricas, com dados sintéticos ou mascarados.

## Checklist

- [ ] Lint e testes passam localmente (`uv run task lint` e `uv run task test`)
- [ ] Todo bug corrigido tem teste de regressão
- [ ] Funções públicas novas têm docstring e type hints
- [ ] Documentação atualizada, se aplicável
- [ ] Nenhum dado pessoal real ou credencial no código, testes ou descrição
- [ ] PR pequeno e focado em um único work item
- [ ] Work item vinculado na seção **Work items**
```

Salve o modelo em `.azuredevops/pull_request_template.md` na `dev` e na `main`. Todo PR criado pela interface web já nasce com essa estrutura.

## Tipos de pull request

| PR | Quando | Revisão | Tipo de merge |
| :--- | :--- | :--- | :--- |
| `task/<id>` → `dev` | A tarefa está pronta para code review | Code review do work item | **Squash commit** |
| `dev` → `main` | Os itens da `dev` estão homologados | Conferência dos itens incluídos | **Merge (no fast-forward)** |

- **Squash commit**: todos os commits da tarefa viram um único commit na `dev`. O histórico da `dev` fica com um commit por tarefa, fácil de ler e de reverter.
- **Merge (no fast-forward)**: mantém os commits da `dev` como estão e acrescenta um commit de merge, que marca a promoção na `main`.

O PR `dev` → `main` não usa squash. O squash criaria na `main` um commit que não existe na `dev`, e as duas branches entrariam em conflito na promoção seguinte.

As opções de conclusão de cada PR estão em [Merge](#merge).

## Vínculo com o work item

O work item é vinculado ao PR na seção **Work items** do PR. O vínculo é automático quando a branch foi criada pelo work item ou quando os commits mencionam `#123`.

- o PR aparece na seção **Development** do work item;
- a branch policy **Check for linked work items** impede a conclusão de PRs sem vínculo;
- o estado do work item é movido por quem executa a ação, não pelo PR.

!!! warning "Não use palavras de transição"
    Termos como `Fixes #123`, `Resolves #123` ou `Closes #123` na descrição do PR ou nos commits mudam o estado do work item de forma automática. No PR `task/<id>` → `dev`, deixe desmarcada a opção **Complete linked work items after merging**: o item ainda precisa ser homologado.

!!! info "Entregas"
    Cada PR resolve **a tarefa** (work item filho), nunca o work item **Entrega**. Não vincule a entrega ao PR.

## PR e status no board

O PR não move o work item: quem executa a ação atualiza o **State**. O significado de cada estado está em [Etapas do board](index.md#etapas-do-board).

| Ação | Quem move o item | Novo status |
| :--- | :--- | :--- |
| Publica o PR `task/<id>` → `dev` (**Publish**) | Autor | **Waiting** |
| Recebe **Wait for author** ou **Reject** | Autor | **Doing** |
| PR aprovado e concluído na `dev` | Quem conclui o PR | **Homologate** |
| Reprovado na homologação | Responsável pela homologação | **Doing** |
| PR `dev` → `main` concluído | Quem conclui o PR | **Done** |

- Crie o PR como rascunho (**Create as draft**) cedo, para dar visibilidade ao trabalho. O rascunho mostra o código à equipe, mas ainda não pede revisão.
- Publique o PR somente quando ele estiver completo e os testes passarem localmente.

=== "Interface web"

    Após o push da branch, **Repos** → **Pull requests** → **New pull request**, com destino `dev`. Preencha o modelo, vincule o work item e escolha **Create as draft** no menu do botão **Create**. Para enviar à revisão, clique em **Publish**.

=== "az CLI"

    ```bash title="PR em rascunho e revisão"
    az repos pr create --draft \
      --repository mdm-hub \
      --source-branch task/123 \
      --target-branch dev \
      --title "[Feature] Validar dígito verificador de CPF na higienização" \
      --description "$(cat descricao-pr.md)" \
      --work-items 123                       # (1)!
    az repos pr update --id 456 --draft false   # (2)!
    ```

    1. Vincula o work item `#123` ao PR.
    2. Publica o PR para revisão. O ID do PR é retornado pelo comando anterior.

## Revisão

### Responsabilidades do autor

- Faça PRs pequenos e específicos. Como referência, até 400 linhas alteradas.
- Nunca envie código não testado.
- Use a aba **Files** para revisar seu próprio código antes de publicar o PR.
- Confira os revisores. Os revisores obrigatórios por caminho são incluídos automaticamente. Veja [Revisores por caminho](#revisores-por-caminho).
- Responda a todos os comentários. Marque como **Resolved** apenas o que foi tratado.

### Responsabilidades do revisor

- Revise itens em **Waiting** em até **um dia útil** após a publicação.
- Valide contra os **critérios de aceitação** do work item, não só contra o código.
- Diferencie o que bloqueia do que é sugestão. Use o prefixo `sugestão:` para comentários não bloqueantes.
- Registre um voto. Evite deixar só comentários soltos.

| Voto | Uso |
| :--- | :--- |
| **Approve** | Pronto para homologação |
| **Approve with suggestions** | Pronto para homologação, com sugestões não bloqueantes |
| **Wait for author** | Ajustes necessários antes de nova revisão |
| **Reject** | A abordagem precisa ser refeita. Explique o motivo |

### Pontos de atenção em MDM

| Mudança | Pergunta do revisor |
| :--- | :--- |
| Nota de corte ou passo de matching | Por que mudou? Qual o efeito esperado em falsos positivos e na fila de duvidosos? |
| Regra de sobrevivência | A ordem das regras foi considerada? Há teste para o grupo afetado? |
| Regra de higienização | Há teste de regressão? Registros válidos podem ser rejeitados? |
| DAG do Airflow | As tasks são idempotentes? A janela de dados está correta? |
| Dependências e imagem | Versões fixadas? Alguma credencial fora do cofre de segredos? |

!!! info "PRs com apoio de IA"
    Código gerado com apoio de agentes de IA segue as mesmas regras: PR pequeno, testes passando e revisão humana obrigatória. Quem abre o PR responde pelo conteúdo.

### Revisores por caminho

Os revisores obrigatórios por pasta são definidos na branch policy **Automatically included reviewers** da `dev`, com filtro de caminho. Quem altera uma pasta recebe automaticamente os revisores responsáveis por ela. A configuração é feita por quem administra o repositório:

| Caminho | Revisores | Obrigatório |
| :--- | :--- | :--- |
| `/*` | Time do projeto | Sim |
| `/src/matching/*; /src/sobrevivencia/*` | Grupo responsável pelas regras do MDM | Sim |
| `/dags/*; /Dockerfile` | Grupo de engenharia de dados | Sim |
| `/.azuredevops/*` | Grupo de líderes técnicos | Sim |

## Homologação

A homologação é a validação funcional contra os **critérios de aceitação** do work item, feita por quem solicitou a tarefa ou pelo responsável pela homologação definido na entrega. Em MDM, ela confirma o efeito da mudança nos dados, com uma base representativa.

1. Após o merge na `dev`, o desenvolvedor executa a `dev` no ambiente de homologação e informa na **Discussion** do work item o link da execução.
2. O responsável pela homologação valida cada critério de aceitação e compara as métricas com a execução anterior.
3. Resultado:
    - **Aprovado**: o responsável registra a homologação na **Discussion** do work item. O item segue em **Homologate** até a promoção para a `main`.
    - **Reprovado**: o responsável descreve o problema na **Discussion** e move o item para **Doing**. A correção é feita em uma nova `task/<id>`, recriada a partir da `dev`, pois a anterior foi excluída no merge. Se não for imediata, reverta o PR na `dev` (**Revert**) para não bloquear a promoção.

| Métrica | Uso |
| :--- | :--- |
| Registros inválidos | Mudanças de higienização |
| Registros duvidosos (faixa de curadoria) | Mudanças de matching |
| Quantidade de Golden Records | Mudanças de matching e sobrevivência |
| Amostra de pares unificados | Verificação de falsos positivos |

```markdown title="Comentário de homologação"
Homologado em ambiente de homologação (carga de 2026-09-28).

- [x] CPF com dígito verificador inválido vai para a fila de inválidos
- [x] Sequências repetidas são rejeitadas
- [x] Testes cobrem CPFs válidos, inválidos e repetidos

Inválidos: 1,8% → 2,3% (esperado, CPFs com DV incorreto).
Golden Records: sem variação.
```

!!! danger "Evidências sem dados pessoais"
    Registre apenas métricas agregadas e exemplos mascarados. Não anexe extrações da base de homologação.

## Branch policies

As branch policies são regras que o Azure Repos aplica a uma branch: sem atendê-las, o PR não pode ser concluído. Elas garantem que nenhum código entre na `dev` ou na `main` sem revisão e sem work item. A configuração é feita uma vez, por quem administra o repositório.

Configure em **Project settings** → **Repositories** → `mdm-hub` → **Policies**, nas branches `dev` e `main`:

| Policy | `dev` | `main` |
| :--- | :--- | :--- |
| **Require a minimum number of reviewers** | **1**, sem o autor aprovar o próprio PR | **1**, sem o autor aprovar o próprio PR |
| **When new changes are pushed** | **Reset all approval votes** | **Reset all approval votes** |
| **Check for linked work items** | **Required** | **Required** |
| **Check for comment resolution** | **Required** | **Required** |
| **Limit merge types** | Somente **Squash merge** | Somente **Basic merge (no fast-forward)** |
| **Automatically included reviewers** | Conforme [Revisores por caminho](#revisores-por-caminho) | Líderes técnicos |

Em **Security** das branches `dev` e `main`, negue (**Deny**) a permissão **Force push** para todos os grupos. O force push sobrescreve o histórico da branch no servidor e só é aceito em `task/<id>`.

!!! info "Configurações do repositório"
    Na aba **Settings** do repositório, mantenha **Commit mention linking** ativo, para vincular commits que mencionam `#123`.

## Merge

O tipo de merge de cada PR está em [Tipos de pull request](#tipos-de-pull-request). Ao concluir, ajuste as opções:

| PR | Opções de conclusão |
| :--- | :--- |
| `task/<id>` → `dev` | Mensagem do squash no padrão de [commits](branches-e-commits.md#padrao-de-commits). Marque **Delete `<branch>` after merging**. Deixe desmarcada **Complete linked work items after merging** |
| `dev` → `main` | Deixe desmarcada **Delete `<branch>` after merging**: a `dev` é permanente |

Promova a `dev` para a `main` somente quando todos os itens da `dev` estiverem homologados. Após a conclusão, quem promoveu move os itens incluídos para **Done**.

=== "Interface web"

    No PR, clique em **Complete**, escolha o tipo de merge e ajuste as opções de conclusão. No squash, edite a mensagem em **Customize merge commit message**.

=== "az CLI"

    ```bash title="Conclusão do PR task/123 → dev"
    az repos pr update --id 456 --status completed \
      --squash true \
      --delete-source-branch true \
      --transition-work-items false \
      --merge-commit-message "feat(higienizacao): valida dígito verificador de CPF"
    ```