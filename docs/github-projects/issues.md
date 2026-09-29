# Issues

A issue registra uma tarefa. Cada issue descreve **uma única mudança**, com critérios que permitem verificar se ela foi concluída.

## Título

Padrão: `[Tipo] O que será feito`

```text title="Exemplos"
[Feature] Validar dígito verificador de CPF na higienização
[Bug] Corrigir DV de CNPJ com zeros à esquerda
[Refactor] Extrair comparadores de matching para módulo próprio
```

| Tipo | Uso |
| :--- | :--- |
| `[Entrega]` | Issue-mãe de uma entrega. Veja [Entregas](entregas.md) |
| `[Feature]` | Nova funcionalidade ou nova regra |
| `[Bug]` | Correção de erro |
| `[Refactor]` | Melhoria interna, sem mudar o resultado |
| `[Docs]` | Documentação |
| `[Chore]` | Dependências, configuração e ambiente |

## Descrição

```markdown title="Modelo de descrição"
## Descrição

O que será feito e por quê.

## Fora de escopo

O que não será feito nesta issue.

## Impacto nos dados

Regras, tabelas ou consumidores afetados. Use "Nenhum" se não houver.

## Critérios de aceitação

- [ ] CPF com dígito verificador inválido vai para a fila de inválidos
- [ ] Sequências repetidas (ex.: 111.111.111-11) são rejeitadas
- [ ] Testes cobrem CPFs válidos, inválidos e repetidos
```

- **Critérios de aceitação** são o que o revisor e o homologador vão conferir. Escreva cada um de forma verificável.
- **Impacto nos dados** é obrigatório quando a issue altera uma regra do MDM.
- Dependências entre issues ficam em **Relationships** → **Mark as blocked by**.

!!! danger "Dados pessoais"
    Nunca copie registros reais para a issue. Use dados sintéticos ou mascarados.

## Campos no Project

| Campo | Quando preencher |
| :--- | :--- |
| Status | **Backlog**, ao criar a issue |
| Parent issue | Ao criar: a entrega à qual a tarefa pertence |
| Área | Ao criar |
| Prioridade e Tamanho | Antes de sair do Backlog |
| Responsável (*assignee*) | Ao assumir a tarefa |

## Vínculo com a entrega

Toda tarefa é **sub-issue** de uma issue `[Entrega]`. Tarefas maiores que **L** devem ser divididas.

=== "Interface web"

    Na issue de entrega, seção **Sub-issues**: **Create sub-issue** ou **Add existing issue**.

=== "gh CLI"

    ```bash title="Criação de tarefa no Project"
    gh issue create \
      --repo minha-org/mdm-hub \
      --title "[Feature] Validar dígito verificador de CPF na higienização" \
      --body-file descricao.md \
      --label feature \
      --project "Dados - MDM"
    ```

    Em seguida, vincule-a à entrega `#120` pela seção **Sub-issues** da issue-mãe.

## Labels

- Labels padrão: `entrega`, `feature`, `bug`, `refactor`, `docs`, `chore`, `needs-info`.
- Labels só classificam. Status, prioridade e tamanho ficam nos campos do Project.
- Não use milestones: a entrega é a issue `[Entrega]`.

## Definição de pronto

Uma issue pode ir para **To-do** quando:

- [ ] O título segue o padrão.
- [ ] A descrição, o fora de escopo e o impacto nos dados estão preenchidos.
- [ ] Os critérios de aceitação são verificáveis.
- [ ] Está vinculada a uma entrega.
- [ ] Prioridade, Tamanho e Área estão preenchidos.
- [ ] O tamanho é **L** ou menor.
- [ ] Não há bloqueios em aberto.
