# Entregas

O trabalho é organizado por **entregas**, sem ciclos de tempo fixo. Uma entrega é um incremento de valor com escopo fechado e data alvo, composto por tarefas que avançam no board em fluxo contínuo.

Cada entrega é um work item do tipo **Entrega**, com **Start Date** e **Target Date**. As tarefas são work items filhos dela (link **Parent/Child**) e levam o código; a entrega não tem branch nem pull request próprios.

## Planejamento

1. Crie o work item **Entrega** e descreva objetivo, escopo, fora de escopo e critérios de aceitação. Em MDM, inclua ao menos um critério mensurável de qualidade de dados.
2. Quebre o escopo em work items filhos que atendam à [definição de pronto](work-items.md#definicao-de-pronto).
3. Estime o **Tamanho** de cada filho.
4. Defina **Start Date** e **Target Date** com base no tamanho total e na capacidade disponível.
5. Priorize os filhos e mova para **To-do** os que podem ser iniciados.

!!! tip "Entregas pequenas"
    Prefira entregas de até quatro semanas. Entregas maiores devem ser divididas em entregas incrementais, cada uma com valor próprio.

## Acompanhamento

O backlog **Entregas** mostra o progresso de cada entrega pela coluna de rollup **Progress by all Work Items**. O **Delivery Plan** mostra as entregas no tempo. Revise o board regularmente, da direita para a esquerda:

1. **Homologate**: há itens aguardando homologação?
2. **Waiting**: há pull requests aguardando revisão há mais de um dia útil?
3. **Doing**: há itens parados ou com impedimento?
4. **To-do**: qual o próximo item prioritário?

!!! info "Risco na data alvo"
    Se o progresso indicar risco para a **Target Date**, reduza o escopo (mova filhos para uma próxima entrega) ou renegocie a data. Registre a decisão na **Discussion** do work item de entrega.

## Limite de trabalho em andamento

Os limites são configurados no board, em cada coluna (**WIP limit**). O board destaca a coluna quando o limite é ultrapassado.

| Coluna | Limite de referência |
| :--- | :--- |
| **Doing** | Um item por desenvolvedor |
| **Waiting** | Metade do número de desenvolvedores |
| **Homologate** | Definido conforme a disponibilidade para homologar |

Com um limite atingido, a prioridade é revisar, homologar e concluir, não iniciar novos itens.

| Sintoma no board | Ação |
| :--- | :--- |
| **Waiting** acumulando | Priorizar code review antes de novos itens |
| **Homologate** acumulando | Agendar homologação em bloco |
| Item em **Doing** há muitos dias | Dividir o work item ou pedir apoio |
| Retornos frequentes de **Homologate** para **Doing** | Revisar a qualidade dos critérios de aceitação e dos casos de teste |

## Fechamento

A entrega é concluída quando:

- [ ] Todos os filhos estão em **Done**.
- [ ] Os critérios de aceitação da entrega foram validados.
- [ ] A versão foi publicada (tag e imagem).

Mova o work item de entrega para o estado concluído. Itens de escopo não concluídos são movidos para uma nova entrega antes do fechamento.

### Versionamento

Cada entrega publicada gera uma **tag anotada** na `main` do Azure Repos, após a promoção da `dev`, no padrão [SemVer](https://semver.org/lang/pt-BR/) `vMAJOR.MINOR.PATCH`. O tipo dos commits indica qual número sobe:

| Commits na entrega | Versão | Exemplo |
| :--- | :--- | :--- |
| Só `fix` | PATCH | `v1.4.0` → `v1.4.1` |
| Ao menos um `feat` | MINOR | `v1.4.0` → `v1.5.0` |
| Mudança incompatível (`!`), como novo layout das views de ingestão | MAJOR | `v1.4.0` → `v2.0.0` |

```bash title="Tag da entrega"
git switch main && git pull
git tag -a v1.5.0 -m "Entrega #120: Unificação de pessoas físicas"
git push origin v1.5.0
```

- A mensagem da tag referencia o work item de entrega. Registre as notas da versão (pull requests incluídos) na **Discussion** da entrega.
- A imagem Docker publicada recebe a mesma versão e o hash do commit (ex.: `mdm-airflow:1.5.0` e `mdm-airflow:a1b2c3d`).
- Produção sempre usa uma tag fixa. `latest` não identifica qual código está em execução.

!!! tip "Rastreabilidade da carga"
    Registre a versão da aplicação nos metadados de execução da carga. Assim é possível saber qual versão das regras gerou cada Golden Record.

## Dashboard

Mantenha um **Dashboard** do time com os seguintes widgets:

| Widget | Configuração | Pergunta que responde |
| :--- | :--- | :--- |
| **Chart for work items** | Query de User Stories e Bugs com `State <> Done`, gráfico de barras por State | Em qual etapa o trabalho está parado? |
| **Chart for work items** | Query de árvore (Parent/Child) das entregas abertas, barras empilhadas por State | Qual entrega está atrasada? |
| **Chart for work items** | Query com `State <> Done`, barras por Assigned To | A carga está equilibrada? |
| **Cumulative Flow Diagram** | Backlog **Stories** | Onde o fluxo acumula? |
| **Cycle Time** | Backlog **Stories** | O ritmo de conclusão é estável? |

!!! info "Métricas são da equipe"
    Os gráficos avaliam o fluxo de trabalho, não o desempenho individual.
