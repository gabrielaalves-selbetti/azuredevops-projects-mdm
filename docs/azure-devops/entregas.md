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

O limite de WIP é o número máximo de itens que uma coluna pode ter ao mesmo tempo. Ele evita que a equipe inicie mais trabalho do que consegue revisar e homologar: um item parado em revisão não entrega valor e se distancia da `dev` a cada dia.

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

Cada entrega publicada gera uma **tag anotada** na `main` do Azure Repos, após a promoção da `dev`. A tag é uma etiqueta fixa em um ponto do histórico. A tag anotada guarda também autor, data e mensagem.

O nome da tag segue o padrão [SemVer](https://semver.org/lang/pt-BR/) `vMAJOR.MINOR.PATCH`. Cada número indica o tamanho da mudança para quem consome o MDM:

- **PATCH**: correção. Nada muda para os consumidores.
- **MINOR**: regra ou funcionalidade nova, compatível com o que já existia.
- **MAJOR**: mudança incompatível. Origens ou consumidores precisam se adaptar.

O tipo dos [commits](branches-e-commits.md#padrao-de-commits) indica qual número sobe:

| Commits na entrega | Versão | Exemplo |
| :--- | :--- | :--- |
| Só `fix` | PATCH | `v1.4.0` → `v1.4.1` |
| Ao menos um `feat` | MINOR | `v1.4.0` → `v1.5.0` |
| Mudança incompatível (`!`), como novo layout das views de ingestão | MAJOR | `v1.4.0` → `v2.0.0` |

```bash title="Tag da entrega"
git switch main && git pull   # (1)!
git tag -a v1.5.0 -m "Entrega #120: Unificação de pessoas físicas"   # (2)!
git push origin v1.5.0   # (3)!
```

1. Vai para a `main` e baixa a versão mais recente, já com a promoção da `dev`.
2. Cria a tag anotada (`-a`) no commit atual, com a mensagem informada em `-m`.
3. Envia a tag ao Azure Repos. O `git push` comum não envia tags.

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
| **Cumulative Flow Diagram** | Backlog **Stories** | Onde o fluxo acumula? Cada faixa é um estado: a faixa que engrossa indica a etapa que acumula itens |
| **Cycle Time** | Backlog **Stories** | O ritmo de conclusão é estável? Cada ponto é um item e mostra quantos dias ele levou do início à conclusão |

!!! info "Métricas são da equipe"
    Os gráficos avaliam o fluxo de trabalho, não o desempenho individual.
