<div class="hero" markdown>

# Guia Azure DevOps

Padrões corporativos para planejar, executar e entregar a aplicação de MDM com o Azure DevOps (Azure Boards e Azure Repos). Um único fluxo, do registro da demanda até a entrega.

<ol class="trilho" aria-label="Etapas do board">
  <li>Backlog</li>
  <li>To-do</li>
  <li>Doing</li>
  <li>Waiting</li>
  <li class="trilho__controle">Homologate</li>
  <li>Done</li>
</ol>

</div>

## Propósito

Este guia padroniza **como a equipe organiza o trabalho no Azure DevOps**. Ele define convenções para que todos os projetos, work items e pull requests da organização sigam as mesmas regras.

- Toda demanda é rastreável, do registro à entrega.
- Cada estado tem o mesmo significado em todos os boards.
- Revisão, homologação e entrega seguem um padrão único.

O trabalho é organizado em **entregas**: conjuntos de work items com escopo e data alvo definidos.

## Contexto: aplicação de MDM

A equipe desenvolve o Hub de **Master Data Management (MDM)**: ingestão dos sistemas de origem, higienização, matching, sobrevivência e publicação do Golden Record. O código roda em Python, orquestrado pelo Airflow e empacotado em containers Docker.

Nesse contexto, cada mudança de regra altera os cadastros de toda a base. O guia define como registrar, revisar, homologar e publicar essas mudanças com rastreabilidade.

## Por onde começar

As páginas seguem a ordem do trabalho. Para ver o caminho completo de uma tarefa em uma única tabela, comece por [Uma tarefa do início ao fim](azure-devops/index.md#uma-tarefa-do-inicio-ao-fim).

<div class="grid cards" markdown>

-   :material-lightbulb-outline: **Fundamentos**

    ---

    Por que cada recurso é usado e quais são os riscos específicos do MDM.

    [Entender os fundamentos](fundamentos.md)

-   :material-view-dashboard-outline: **Visão geral**

    ---

    As seis etapas do board e o caminho de uma tarefa.

    [Ver as etapas do board](azure-devops/index.md)

-   :material-package-variant-closed: **Entregas**

    ---

    Planejamento, acompanhamento, fechamento e versão.

    [Planejar uma entrega](azure-devops/entregas.md)

-   :material-card-text-outline: **Work items**

    ---

    Título, descrição, critérios de aceitação e campos.

    [Registrar uma tarefa](azure-devops/work-items.md)

-   :material-source-branch: **Branches e commits**

    ---

    A branch `task/<id>` e o padrão das mensagens de commit.

    [Criar a branch e commitar](azure-devops/branches-e-commits.md)

-   :material-source-pull: **Pull requests**

    ---

    Revisão, homologação e promoção para a `main`.

    [Abrir e revisar um PR](azure-devops/pull-requests.md)

</div>

Quem administra o projeto encontra o processo, os campos e as visões em [Configuração do projeto](azure-devops/configuracao.md).

!!! tip "Exemplo usado ao longo do guia"
    Todas as páginas usam a mesma entrega fictícia: **Unificação de pessoas físicas** (work item `#120`), cuja primeira tarefa é **Validação de CPF na higienização** (work item `#123`), no repositório `mdm-hub` do projeto **Dados - MDM**, na organização `minha-org`.
