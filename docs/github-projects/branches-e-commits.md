# Branches e commits

A branch conecta a issue ao código. Criada a partir da issue, ela aparece no campo **Development** da issue e sinaliza que o trabalho começou.

## Divisão das branches

Cada repositório tem **uma única branch permanente**, a `main`. Todo o trabalho acontece em **branches temporárias**, uma por issue, que nascem da `main` e voltam para ela por pull request.

| Branch | Tipo | Origem | Destino | Vida útil | Conteúdo |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `main` | Permanente e protegida | — | — | Permanente | Somente código revisado e homologado |
| `feature/<numero>-...` | Temporária | `main` | `main`, por PR | Até o merge | Issue `[Feature]` |
| `bug/<numero>-...` | Temporária | `main` | `main`, por PR | Até o merge | Issue `[Bug]` |
| `refactor/<numero>-...` | Temporária | `main` | `main`, por PR | Até o merge | Issue `[Refactor]` |
| `docs/<numero>-...` | Temporária | `main` | `main`, por PR | Até o merge | Issue `[Docs]` |
| `chore/<numero>-...` | Temporária | `main` | `main`, por PR | Até o merge | Issue `[Chore]` |

Não há `develop`, `release/`, `hotfix/` nem branch de homologação. O motivo está em [Estratégia de branches](../fundamentos.md#estrategia-de-branches).

### Regras

| Regra | Motivo |
| :--- | :--- |
| Uma issue, uma branch, um pull request | Cada mudança de regra é revisada, homologada e revertida isoladamente |
| Origem sempre na `main` atualizada | Reduz conflitos e garante que a mudança parte do código homologado |
| Nunca criar branch a partir de outra branch de trabalho | Evita que código não homologado entre de carona. Se a tarefa depende de outra, aguarde o merge ou divida a issue |
| Vida curta | O tamanho máximo da issue (**L**, até uma semana) limita a vida da branch |
| Nenhum commit direto na `main` | Todo código passa por code review e homologação. O ruleset bloqueia o push direto |
| Branch excluída após o merge | Mantém a lista de branches igual à lista de trabalho em andamento |

### Ambientes

| Ambiente | Versão em execução |
| :--- | :--- |
| Local | Branch de trabalho |
| Homologação | Branch do pull request em homologação |
| Produção | Última **release** (tag) gerada a partir da `main`. Veja [Versionamento](entregas.md#versionamento) |

A `main` acumula itens homologados. Eles chegam à produção quando a entrega gera uma release.

### Correção urgente

Um incidente em produção segue o mesmo fluxo, com prioridade máxima:

1. Issue `[Bug]` com a prioridade mais alta (ex.: **P0**).
2. Branch `bug/<numero>-...` a partir da `main`.
3. Code review e homologação imediatos, antes de qualquer outro item.
4. Após o merge, release com versão PATCH.

!!! warning "Main com itens que ainda não podem ir para produção"
    Se a `main` contiver itens homologados que ainda não podem ser publicados, crie a branch de correção a partir da tag em produção (`git switch -c bug/130-corrigir-dv-cnpj v1.4.0`). Publique a release PATCH a partir dessa branch e abra o pull request dela para a `main`, para que a correção não se perca.

## Padrão de nomes

Use nomes curtos, descritivos, com prefixo de tipo e o número da issue.

Padrão: `tipo/numero-nome-curto`

```text title="Exemplos"
feature/123-validar-dv-cpf
bug/130-corrigir-dv-cnpj
refactor/141-extrair-comparadores
docs/125-regras-sobrevivencia
chore/155-atualizar-airflow
```

| Regra | Motivo |
| :--- | :--- |
| Prefixo igual ao tipo da issue | Identifica a natureza da mudança |
| Número da issue | Rastreabilidade direta entre código e tarefa |
| Letras minúsculas e hífens | Compatibilidade com qualquer sistema e ferramenta |
| Sem acentos nem espaços | Evita erros em scripts e ferramentas |

## Criando a branch pela issue

Criar a branch pela issue garante o vínculo automático com o Project.

=== "Interface web"

    1. Na issue, na lateral **Development**, clique em **Create a branch**.
    2. Ajuste o nome para o padrão (ex.: `feature/123-validar-dv-cpf`).
    3. Escolha **Checkout locally** e execute o comando sugerido.

=== "gh CLI"

    ```bash title="Branch vinculada à issue"
    gh issue develop 123 \
      --repo minha-org/mdm-hub \
      --name feature/123-validar-dv-cpf \
      --base main \
      --checkout
    ```

Em seguida:

1. Atribua a issue a você.
2. Mova o item para **Doing** no Project.

## Padrão de commits

Os commits seguem o [Conventional Commits](https://www.conventionalcommits.org/pt-br/v1.0.0/). O padrão torna o histórico legível e permite calcular a versão da release a partir dos commits.

Padrão: `tipo(escopo): descrição no imperativo`

```text title="Exemplos"
feat(higienizacao): valida dígito verificador de CPF
fix(higienizacao): corrige DV de CNPJ com zeros à esquerda
refactor(matching): extrai comparadores para módulo próprio
docs(sobrevivencia): descreve prioridade de fontes do e-mail
test(matching): cobre caso de gêmeos com mesmo CPF
chore(deps): atualiza Airflow para 3.0.6
ci(infra): publica imagem com tag do commit
feat(ingestao)!: altera layout da view de pessoa
```

| Tipo | Uso |
| :--- | :--- |
| `feat` | Nova funcionalidade ou nova regra |
| `fix` | Correção de erro |
| `refactor` | Mudança interna sem alterar comportamento nem resultado nos dados |
| `docs` | Documentação |
| `test` | Testes |
| `chore` | Build, dependências, configuração |
| `ci` | Pipelines |

O **escopo** é o valor do campo [Área](configuracao.md#area) em minúsculas e sem acentos: `ingestao`, `higienizacao`, `matching`, `sobrevivencia`, `publicacao`, `curadoria`, `orquestracao`, `infra`. Use `deps` para dependências.

!!! warning "Mudanças incompatíveis"
    Adicione `!` após o escopo quando a mudança quebrar um contrato, como o layout das views de ingestão ou o formato publicado aos consumidores. O `!` gera uma versão MAJOR e sinaliza que outras equipes precisam se adaptar.

!!! tip "Boas práticas"
    - Um commit, uma mudança lógica.
    - Descrição curta (até 72 caracteres), no imperativo e em minúsculas.
    - Use o corpo do commit para explicar o **porquê**, não o **como**. Em regras de dados, registre o efeito esperado.
    - Referencie a issue no corpo quando útil: `Refs #123`.

!!! warning "Não feche issues pelo commit"
    Palavras como `Closes #123` em commits fecham a issue assim que o commit chega à `main`, sem passar pelo vínculo do pull request. Use-as **somente na descrição do pull request**. Veja [Pull requests](pull-requests.md#vinculo-com-a-issue).

### Verificações antes do commit

Os repositórios usam [pre-commit](https://pre-commit.com/) para rodar formatação e lint (Ruff) antes de cada commit. Instale os hooks ao clonar o repositório:

```bash title="Instalação dos hooks"
uv run pre-commit install
```

!!! danger "Nunca pule os hooks"
    Não use `git commit --no-verify`, nem aceite essa sugestão de um agente de IA. Se o hook falhar, corrija o que foi apontado.

## Mantendo a branch atualizada

```bash title="Atualização com a main"
git fetch origin
git rebase origin/main
git push --force-with-lease   # (1)!
```

1. `--force-with-lease` recusa o push se alguém tiver enviado commits à branch que você ainda não tem.

Branches mescladas são excluídas automaticamente. Habilite **Automatically delete head branches** em **Settings** → **General** de cada repositório.
