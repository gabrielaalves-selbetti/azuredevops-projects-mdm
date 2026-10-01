# Visão geral

O projeto no Azure DevOps é **a fonte única da verdade** sobre o andamento do trabalho. Se não está no Azure Boards, não está planejado.

O trabalho é organizado em **entregas**, não em ciclos de tempo fixo. Cada entrega é um work item do tipo **Entrega** com escopo e data alvo, e seus work items filhos percorrem as etapas do board. Veja [Entregas](entregas.md).

## Etapas do board

<ol class="trilho" aria-label="Etapas do board">
  <li>Backlog</li>
  <li>To-do</li>
  <li>Doing</li>
  <li>Waiting</li>
  <li class="trilho__controle">Homologate</li>
  <li>Done</li>
</ol>

O campo **State** define as etapas. Os nomes e a ordem são os mesmos em todos os projetos da organização, porque vêm de um processo compartilhado. Veja [Processo](configuracao.md#processo).

| State | Significado | Entra quando | Sai quando |
| :--- | :--- | :--- | :--- |
| **Backlog** | Registrado, ainda não priorizado | O work item é criado | É priorizado para execução |
| **To-do** | Priorizado, pronto para desenvolvimento | Atende à [definição de pronto](work-items.md#definicao-de-pronto) e foi priorizado | Alguém assume e cria a branch |
| **Doing** | Em desenvolvimento | A branch `task/<id>` é criada e há responsável | O pull request para a `dev` é publicado |
| **Waiting** | Aguardando code review | Pull request publicado (**Publish**) | O pull request é aprovado, ou há ajustes solicitados (volta para **Doing**) |
| **Homologate** | Em homologação: validação funcional na `dev` contra os critérios de aceitação | Pull request aprovado e concluído na `dev` | A `dev` é promovida para a `main`, ou é reprovado (volta para **Doing**) |
| **Done** | Homologado e na `main` | Pull request `dev` → `main` concluído | — |

??? abstract "Diagrama de transições"

    ```mermaid
    stateDiagram-v2
        direction LR
        [*] --> Backlog: work item criado
        Backlog --> ToDo: priorização
        ToDo --> Doing: branch criada
        Doing --> Waiting: PR publicado
        Waiting --> Doing: ajustes solicitados
        Waiting --> Homologate: merge na dev
        Homologate --> Doing: reprovado na homologação
        Homologate --> Done: promovido para a main
        Done --> [*]
        ToDo: To-do
    ```

!!! note "Homologação antes da main"
    **Homologate** é o ponto de controle do fluxo: nenhuma regra chega à `main` sem ser validada na `dev`. O motivo está em [O que muda em um projeto de MDM](../fundamentos.md#o-que-muda-em-um-projeto-de-mdm).

## Uma tarefa do início ao fim

O caminho da tarefa `#123`, do registro até a `main`. O estado é movido manualmente, por quem executa a ação.

| Passo | Ação | Quem move o item | State | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Registra o work item como filho da entrega `#120` | Quem registra | **Backlog** | [Work items](work-items.md) |
| 2 | Completa a definição de pronto e prioriza | Quem prioriza | **To-do** | [Definição de pronto](work-items.md#definicao-de-pronto) |
| 3 | Assume o item e cria a branch `task/123` a partir da `dev` | Desenvolvedor | **Doing** | [Criando a branch](branches-e-commits.md#criando-a-branch) |
| 4 | Faz os commits e abre o PR em rascunho | — | **Doing** | [Padrão de commits](branches-e-commits.md#padrao-de-commits) |
| 5 | Publica o PR `task/123` → `dev` | Autor | **Waiting** | [Pull requests](pull-requests.md#pr-e-status-no-board) |
| 6 | O revisor aprova e o PR é concluído na `dev` | Quem conclui o PR | **Homologate** | [Revisão](pull-requests.md#revisao) |
| 7 | A mudança é validada na `dev` contra os critérios de aceitação | — | **Homologate** | [Homologação](pull-requests.md#homologacao) |
| 8 | O PR `dev` → `main` é concluído | Quem conclui o PR | **Done** | [Merge](pull-requests.md#merge) |

Se o revisor pedir ajustes (passo 6) ou a homologação reprovar (passo 7), o item volta para **Doing**.

## Princípios

- **Um work item, uma entrega de valor.** Cada work item gera ao menos um pull request e é concluído por ele.
- **Nada fora do board.** Trabalho não registrado não é planejado, medido nem revisado.
- **Status sempre atualizado.** Quem executa a ação move o item.
- **Pequeno e frequente.** Work items e pull requests pequenos reduzem o tempo de revisão, de homologação e o risco de cada entrega. Uma regra de dados por pull request.
- **Fluxo contínuo.** Concluir tem prioridade sobre iniciar. Veja [limites de WIP](entregas.md#limite-de-trabalho-em-andamento).
