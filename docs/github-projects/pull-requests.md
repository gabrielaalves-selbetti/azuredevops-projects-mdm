# Pull requests

O pull request (PR) é o ponto de revisão e o gatilho de entrega. Ele deve referenciar a issue que resolve, estar bem descrito e pronto para revisão.

## Título

Padrão: `[Tipo] Breve descrição da funcionalidade`

```text title="Exemplo"
[Feature] Validar dígito verificador de CPF na higienização
```

O título do PR normalmente repete o título da issue.

## Descrição

```markdown title=".github/pull_request_template.md"
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
- [ ] PR pequeno e focado em uma única issue

## Issues relacionadas

Closes #123
```

Salve o modelo em `.github/pull_request_template.md` para que todo PR já nasça com essa estrutura.

## Vínculo com a issue

A linha `Closes #123` na descrição do PR cria o vínculo entre PR e issue:

- o PR aparece no campo **Linked pull requests** do item;
- ao mesclar o PR, a issue é fechada.

| Palavra-chave | Efeito |
| :--- | :--- |
| `Closes #123`, `Fixes #123`, `Resolves #123` | Fecha a issue quando o PR é mesclado na branch padrão |
| `Refs #123` | Apenas referencia, sem fechar |
| `Closes minha-org/outro-repo#45` | Fecha issue de outro repositório |

!!! warning "Um `Closes` por issue"
    Repita a palavra-chave para cada issue: `Closes #123, closes #124`. Escrever `Closes #123, #124` fecha apenas a primeira.

!!! info "Entregas"
    Cada PR fecha **a tarefa** (sub-issue), nunca a issue `[Entrega]`. Veja [Entregas](entregas.md#estrutura).

## PR e status no Project

| Ação no PR | Quem move o item | Novo status |
| :--- | :--- | :--- |
| Abre o PR como **Draft** | Autor | **Doing** |
| Marca **Ready for review** | Autor | **Waiting** |
| Recebe **Request changes** | Autor | **Doing** |
| Recebe **Approve** | Revisor | **Homologate** |
| É homologado e mesclado | Quem faz o merge | **Done** |

- Abra o PR como **Draft** cedo, para dar visibilidade ao trabalho.
- Marque **Ready for review** somente quando o PR estiver completo e os testes passarem localmente.
- **Não faça o merge** antes da homologação.

=== "Interface web"

    Após o push da branch, clique em **Compare & pull request**, preencha o modelo e escolha **Create draft pull request**.

=== "gh CLI"

    ```bash title="PR em rascunho e revisão"
    gh pr create --draft --fill --base main   # (1)!
    gh pr ready                              # (2)!
    ```

    1. `--fill` usa os commits para preencher título e corpo. Revise e ajuste ao modelo.
    2. Marca o PR como pronto para revisão.

## Revisão

### Responsabilidades do autor

- Faça PRs pequenos e específicos. Como referência, até 400 linhas alteradas.
- Nunca envie código não testado.
- Use a aba **Files changed** para revisar seu próprio código antes de solicitar revisão.
- Indique o revisor adequado. Com `CODEOWNERS`, a indicação é automática.
- Responda a todos os comentários. Marque como resolvido apenas o que foi tratado.

### Responsabilidades do revisor

- Revise itens em **Waiting** em até **um dia útil** após a solicitação.
- Valide contra os **critérios de aceitação** da issue, não só contra o código.
- Diferencie o que bloqueia do que é sugestão. Use o prefixo `sugestão:` para comentários não bloqueantes.
- Aprove (**Approve**) ou solicite ajustes (**Request changes**). Evite deixar só comentários soltos.

### Pontos de atenção em MDM

| Mudança | Pergunta do revisor |
| :--- | :--- |
| Nota de corte ou passo de matching | Por que mudou? Qual o efeito esperado em falsos positivos e na fila de duvidosos? |
| Regra de sobrevivência | A ordem das regras foi considerada? Há teste para o grupo afetado? |
| Regra de higienização | Há teste de regressão? Registros válidos podem ser rejeitados? |
| DAG do Airflow | As tasks são idempotentes? A janela de dados está correta? |
| Dependências e imagem | Versões fixadas? Alguma credencial fora dos secrets? |

!!! info "PRs com apoio de IA"
    Código gerado com apoio de agentes de IA segue as mesmas regras: PR pequeno, testes passando e revisão humana obrigatória. Quem abre o PR responde pelo conteúdo.

### CODEOWNERS

```text title=".github/CODEOWNERS"
# Revisores padrão do repositório
*                   @minha-org/dados-mdm

# Regras de negócio do MDM
/src/matching/      @minha-org/mdm-regras
/src/sobrevivencia/ @minha-org/mdm-regras

# Orquestração e infraestrutura
/dags/              @minha-org/engenharia-dados
/Dockerfile         @minha-org/engenharia-dados
/.github/           @minha-org/lideres-tecnicos
```

## Homologação

A homologação é a validação funcional contra os **critérios de aceitação** da issue, feita por quem solicitou a tarefa ou pelo responsável pela homologação definido na entrega. Em MDM, ela confirma o efeito da mudança nos dados, com uma base representativa.

1. O desenvolvedor executa a versão do PR no ambiente de homologação e informa na issue o link da execução.
2. O responsável pela homologação valida cada critério de aceitação e compara as métricas com a execução anterior.
3. Resultado:
    - **Aprovado**: o responsável registra a homologação em comentário na issue. O desenvolvedor faz o merge, e o item vai para **Done**.
    - **Reprovado**: o responsável descreve o problema em comentário e move o item para **Doing**.

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

## Proteção da branch `main`

Configure em **Settings** → **Rules** → **Rulesets** de cada repositório:

- [ ] Exigir pull request antes do merge.
- [ ] Exigir ao menos **uma aprovação**.
- [ ] Exigir revisão de CODEOWNERS.
- [ ] Descartar aprovações quando novos commits forem enviados.
- [ ] Bloquear force push e exclusão.

## Merge

- Use **Squash and merge** como padrão. O histórico da `main` fica com um commit por PR.
- O título do commit de squash segue o padrão de [commits](branches-e-commits.md#padrao-de-commits).
- A branch é excluída automaticamente após o merge.
