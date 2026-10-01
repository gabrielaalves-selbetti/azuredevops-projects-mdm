# Work items

O work item registra uma tarefa. Cada work item descreve **uma única mudança**, com critérios que permitem verificar se ela foi concluída.

## Tipo e título

O tipo do work item segue a natureza da mudança. O título usa o padrão `[Tipo] O que será feito`.

```text title="Exemplos"
[Feature] Validar dígito verificador de CPF na higienização
[Bug] Corrigir DV de CNPJ com zeros à esquerda
[Refactor] Extrair comparadores de matching para módulo próprio
```

| Prefixo | Tipo de work item | Uso |
| :--- | :--- | :--- |
| — | **Entrega** | Work item pai de uma entrega. Veja [Entregas](entregas.md) |
| `[Feature]` | **User Story** | Nova funcionalidade ou nova regra |
| `[Bug]` | **Bug** | Correção de erro |
| `[Refactor]` | **User Story** | Melhoria interna, sem mudar o resultado |
| `[Docs]` | **User Story** | Documentação |
| `[Chore]` | **User Story** | Dependências, configuração e ambiente |

Todo work item de tarefa é desenvolvido na branch `task/<id>` (ex.: `task/123`). Veja [Branches e commits](branches-e-commits.md#branches).

## Descrição

```markdown title="Modelo de descrição"
## Descrição

O que será feito e por quê.

## Fora de escopo

O que não será feito neste work item.

## Impacto nos dados

Regras, tabelas ou consumidores afetados. Use "Nenhum" se não houver.

## Critérios de aceitação

- [ ] CPF com dígito verificador inválido vai para a fila de inválidos
- [ ] Sequências repetidas (ex.: 111.111.111-11) são rejeitadas
- [ ] Testes cobrem CPFs válidos, inválidos e repetidos
```

- **Critérios de aceitação** são o que o revisor e o homologador vão conferir. Escreva cada um de forma verificável. Na User Story, use o campo **Acceptance Criteria**; no Bug, a seção de critérios dentro de **Repro Steps**.
- **Impacto nos dados** é obrigatório quando o work item altera uma regra do MDM.
- Dependências entre work items usam o link **Predecessor/Successor**: o item bloqueado recebe o link **Predecessor** para o item que o bloqueia.

!!! danger "Dados pessoais"
    Nunca copie registros reais para o work item, para a **Discussion** ou para anexos. Use dados sintéticos ou mascarados.

## Campos

| Campo | Quando preencher |
| :--- | :--- |
| State | **Backlog**, ao criar o work item |
| Parent | Ao criar: a entrega à qual a tarefa pertence |
| Area Path | Ao criar |
| Priority e Tamanho | Antes de sair do Backlog |
| Assigned To | Ao assumir a tarefa |

## Vínculo com a entrega

Todo work item de tarefa é **filho** de uma **Entrega**. Tarefas maiores que **L** devem ser divididas.

=== "Interface web"

    No backlog **Entregas**, use **+** na linha da entrega para criar um filho. Para um item existente, abra-o e use **Links** → **Add link** → **Existing item**, com o tipo de link **Parent**.

=== "az CLI"

    ```bash title="Criação de tarefa vinculada à entrega"
    az boards work-item create \
      --type "User Story" \
      --title "[Feature] Validar dígito verificador de CPF na higienização" \
      --area "Dados - MDM\Higienização" \
      --description "$(cat descricao.md)" \
      --fields "Microsoft.VSTS.Common.Priority=2" "Custom.Tamanho=S"   # (1)!

    az boards work-item relation add \
      --id 123 --relation-type parent --target-id 120
    ```

    1. O nome de referência do campo customizado aparece em **Organization settings** → **Process** → **Fields**.

## Tags

- Tag padrão: `needs-info`, quando o item aguarda informação do solicitante.
- Tags só classificam. Tipo, estado, prioridade e tamanho ficam nos campos.
- Não use **Iteration Path** nem sprints: a entrega é o work item **Entrega**.

## Definição de pronto

Um work item pode ir para **To-do** quando:

- [ ] O título segue o padrão.
- [ ] A descrição, o fora de escopo e o impacto nos dados estão preenchidos.
- [ ] Os critérios de aceitação são verificáveis.
- [ ] É filho de uma entrega.
- [ ] Priority, Tamanho e Area Path estão preenchidos.
- [ ] O tamanho é **L** ou menor.
- [ ] Não há link **Predecessor** para item em aberto.
