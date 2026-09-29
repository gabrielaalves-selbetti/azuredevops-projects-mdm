<div class="hero" markdown>

# Guia GitHub Projects

Padrões corporativos para planejar, executar e entregar a aplicação de MDM com o GitHub Projects. Um único fluxo, do registro da demanda até a entrega.

</div>

## Propósito

Este guia padroniza **como a equipe organiza o trabalho no GitHub**. Ele não ensina a ferramenta: define convenções para que todos os Projects, issues e pull requests da organização sigam as mesmas regras.

- Toda demanda é rastreável, do registro à entrega.
- Cada status tem o mesmo significado em todos os quadros.
- Revisão, homologação e entrega seguem um padrão único.

O trabalho é organizado em **entregas**: conjuntos de issues com escopo e data alvo definidos.

## Contexto: aplicação de MDM

A equipe desenvolve o Hub de **Master Data Management (MDM)**: ingestão dos sistemas de origem, higienização, matching, sobrevivência e publicação do Golden Record. O código roda em Python, orquestrado pelo Airflow e empacotado em containers Docker.

Nesse contexto, cada mudança de regra altera os cadastros de toda a base. O guia define como registrar, revisar, homologar e publicar essas mudanças com rastreabilidade. Veja [Fundamentos](fundamentos.md).

## Seções

<div class="grid cards" markdown>

-   :material-lightbulb-outline: **Fundamentos**

    ---

    Propósito de cada recurso do GitHub e riscos específicos do MDM.

    [Acessar](fundamentos.md)

-   :material-view-dashboard-outline: **Visão geral**

    ---

    Etapas do quadro e princípios.

    [Acessar](github-projects/index.md)

-   :material-cog-outline: **Configuração do Project**

    ---

    Campos, views e checklist de configuração.

    [Acessar](github-projects/configuracao.md)

-   :material-package-variant-closed: **Entregas**

    ---

    Planejamento, acompanhamento e fechamento.

    [Acessar](github-projects/entregas.md)

</div>

!!! tip "Exemplo usado ao longo do guia"
    Todas as páginas usam a mesma entrega fictícia: **Unificação de pessoas físicas** (issue `#120`), cuja primeira tarefa é **Validação de CPF na higienização** (issue `#123`), no repositório `minha-org/mdm-hub` e no Project **Dados - MDM** (número `7`).
