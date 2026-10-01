# Configuração do projeto

Padrão sugerido para os projetos da equipe no Azure DevOps. Parta dele e ajuste apenas o necessário.

!!! info "Feita uma vez, por quem administra"
    Esta página é usada na criação do projeto. Para o trabalho do dia a dia, siga para [Entregas](entregas.md) e [Work items](work-items.md).

## Padrão sugerido

| Item | Padrão |
| :--- | :--- |
| Projeto | `Equipe - Produto` (ex.: `Dados - MDM`), na organização `minha-org` |
| Processo | Herdado do **Agile** (ex.: `Agile MDM`), compartilhado por todos os projetos |
| Repositório | Git no Azure Repos (ex.: `mdm-hub`), com as branches `main` e `dev` |
| Acesso | Por grupo (time do projeto e **Contributors**), não por pessoa |
| Sprints | Não usados. O trabalho é organizado por [entregas](entregas.md) |

=== "Interface web"

    1. Página inicial da organização → **New project**. Em **Advanced**, escolha **Git** e o processo herdado.
    2. **Repos** → **New repository**. Depois, crie a `dev` a partir da `main` (**Branches** → **New branch**).

=== "az CLI"

    ```bash title="Projeto, repositório e branch dev"
    az devops configure --defaults \
      organization=https://dev.azure.com/minha-org project="Dados - MDM"   # (1)!
    az devops project create --name "Dados - MDM" --process "Agile MDM"
    az repos create --name mdm-hub
    git push origin main:dev   # (2)!
    ```

    1. Define a organização e o projeto padrão. Os próximos comandos `az` não precisam repeti-los.
    2. Cria a `dev` no servidor como cópia da `main`. Execute no repositório local, depois do primeiro push da `main`.

## Processo

O processo define os tipos de work item, os campos e os estados disponíveis no projeto. O **processo herdado** é uma cópia customizável do processo Agile padrão. Ele é criado uma vez, em **Organization settings** → **Process**, por um administrador da organização, e vale para todos os projetos que o usam.

| Tipo de work item | Uso |
| :--- | :--- |
| **Entrega** | Tipo novo, no nível de portfólio (renomeado para "Entregas"), com **Start Date** e **Target Date** |
| **User Story** | Tarefas `[Feature]`, `[Refactor]`, `[Docs]` e `[Chore]` |
| **Bug** | Tarefas `[Bug]`, gerenciadas junto com as User Stories |

Feature, Epic e Task não são usados.

Em **User Story** e **Bug**, crie os estados abaixo e oculte os herdados (**New**, **Active**, **Resolved**, **Closed**):

| State | Categoria |
| :--- | :--- |
| **Backlog** | Proposed |
| **To-do** | Proposed |
| **Doing** | In Progress |
| **Waiting** | In Progress |
| **Homologate** | In Progress |
| **Done** | Completed |

A categoria informa ao Azure Boards como tratar cada estado: **Proposed** é trabalho não iniciado, **In Progress** é trabalho em andamento e **Completed** é trabalho concluído. Os gráficos e o progresso das entregas usam a categoria, não o nome do estado.

!!! warning "Nomes definitivos"
    Nomes de estados customizados não podem ser alterados depois de criados. Confira a grafia antes de salvar.

## Campos

Valores sugeridos. A equipe pode ajustá-los, desde que use o mesmo padrão em todo o processo.

| Campo | Valores sugeridos |
| :--- | :--- |
| **Priority** (nativo) | `1` urgente · `2` essencial para a entrega · `3` importante · `4` desejável |
| **Tamanho** (picklist) | `XS` menos de meio dia · `S` até um dia · `M` dois a três dias · `L` até uma semana · `XL` deve ser dividido |
| **Area Path** (nativo) | Uma área por camada do MDM: `Ingestão`, `Higienização`, `Matching`, `Sobrevivência`, `Publicação`, `Curadoria`, `Orquestração`, `Infra` |
| **Start Date** e **Target Date** | Só no tipo **Entrega** |

## Board e visões

| Visão | Configuração |
| :--- | :--- |
| **Board** (nível Stories) | Uma coluna por estado. **WIP limit** (máximo de itens na coluna) em **Doing**, **Waiting** e **Homologate** |
| **Backlog de Entregas** | Colunas Target Date e **Progress by all Work Items** (barra com o percentual de filhos concluídos) |
| **Delivery Plan** | Entregas no tempo, por Start Date e Target Date |
| **Queries compartilhadas** | `State = Waiting` (revisão), `State = Homologate` (homologação), `Assigned To = @Me` (minhas tarefas) |

## Checklist

- [ ] Processo herdado com o tipo **Entrega**, os seis estados e o campo **Tamanho**.
- [ ] Projeto criado com o processo herdado e acesso por grupo.
- [ ] Repositório com `main`, `dev` e [branch policies](pull-requests.md#branch-policies).
- [ ] Area Paths das camadas do MDM.
- [ ] Board, Delivery Plan e queries configurados.
