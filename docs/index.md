<div class="hero" markdown>

# Guia Azure DevOps

Padrões corporativos para planejar, executar e entregar a aplicação de MDM com o Azure DevOps (Azure Boards e Azure Repos). Um único fluxo, do registro da demanda até a entrega.

</div>

## Propósito

Este guia padroniza **como a equipe organiza o trabalho no Azure DevOps**. Ele não ensina a ferramenta: define convenções para que todos os projetos, work items e pull requests da organização sigam as mesmas regras.

- Toda demanda é rastreável, do registro à entrega.
- Cada estado tem o mesmo significado em todos os boards.
- Revisão, homologação e entrega seguem um padrão único.

O trabalho é organizado em **entregas**: conjuntos de work items com escopo e data alvo definidos.

## Contexto: aplicação de MDM

A equipe desenvolve o Hub de **Master Data Management (MDM)**: ingestão dos sistemas de origem, higienização, matching, sobrevivência e publicação do Golden Record. O código roda em Python, orquestrado pelo Airflow e empacotado em containers Docker.

Nesse contexto, cada mudança de regra altera os cadastros de toda a base. O guia define como registrar, revisar, homologar e publicar essas mudanças com rastreabilidade. Veja [Fundamentos](fundamentos.md).

## Seções

<div class="grid cards" markdown>

-   :material-lightbulb-outline: **Fundamentos**

    ---

    Propósito de cada recurso do Azure DevOps e riscos específicos do MDM.

    [Acessar](fundamentos.md)

-   :material-view-dashboard-outline: **Visão geral**

    ---

    Etapas do board e princípios.

    [Acessar](azure-devops/index.md)

-   :material-cog-outline: **Configuração do projeto**

    ---

    Processo, campos, board, queries e checklist.

    [Acessar](azure-devops/configuracao.md)

-   :material-package-variant-closed: **Entregas**

    ---

    Planejamento, acompanhamento e fechamento.

    [Acessar](azure-devops/entregas.md)

</div>

!!! tip "Exemplo usado ao longo do guia"
    Todas as páginas usam a mesma entrega fictícia: **Unificação de pessoas físicas** (work item `#120`), cuja primeira tarefa é **Validação de CPF na higienização** (work item `#123`), no repositório `mdm-hub` do projeto **Dados - MDM**, na organização `minha-org`.
