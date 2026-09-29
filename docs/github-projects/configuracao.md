# Configuração do Project

Estrutura padrão de campos, views e acessos. Todos os Projects da organização seguem esta configuração.

## Criação

- Crie o Project **na organização**, nunca na conta pessoal.
- Nome no padrão `Equipe - Produto` (ex.: `Dados - MDM`).
- Conceda acesso de escrita ao **time** da equipe, não a pessoas.
- Vincule todos os repositórios da equipe ao Project.

=== "Interface web"

    1. Organização → **Projects** → **New project**. Use o template da organização, se houver.
    2. **Settings** → **Manage access**: adicione o time com permissão **Write**.
    3. Em cada repositório: **Projects** → **Link a project**.

=== "gh CLI"

    ```bash title="Criação e vínculo"
    gh project create --owner minha-org --title "Dados - MDM"
    gh project link 7 --owner minha-org --repo minha-org/mdm-hub
    ```

!!! tip "Template da organização"
    Após configurar um Project completo, marque-o como **template** em **Settings** → **Templates**. Novos Projects partem da mesma estrutura de campos e views.

## Campos

Campos acrescentam informações às issues dentro do Project. Eles permitem filtrar, agrupar e ordenar o trabalho nas views.

| Campo | Tipo | Para que serve |
| :--- | :--- | :--- |
| **Status** | Seleção única | Etapa do item no fluxo. Cada valor vira uma coluna do quadro |
| **Prioridade** | Seleção única | Ordem em que os itens devem ser atendidos |
| **Tamanho** | Seleção única | Esforço estimado. Ajuda a planejar a entrega e a identificar tarefas grandes demais |
| **Área** | Seleção única | Parte do sistema afetada. Permite ver o trabalho por camada do MDM |
| **Início** | Data | Quando a entrega começa. Só nas issues de entrega |
| **Data alvo** | Data | Quando a entrega deve estar concluída. Só nas issues de entrega |
| **Parent issue** | Nativo | Liga a tarefa à entrega da qual faz parte |
| **Sub-issues progress** | Nativo | Mostra quantas tarefas da entrega já foram concluídas |

### Nomes dos valores

A equipe escolhe os nomes dos valores de Prioridade, Tamanho e Área. Três cuidados valem para qualquer escolha:

- **Um padrão por Project.** Todos usam os mesmos valores com o mesmo significado.
- **Ordem das opções.** A ordem cadastrada no campo define a ordenação nas views. Cadastre do maior para o menor.
- **Cores.** Cada opção aceita uma cor. Use cores quentes para os níveis mais altos, para leitura rápida no quadro.

!!! warning "Status é a exceção"
    As etapas do **Status** são as mesmas em todos os Projects, pois o guia inteiro se baseia nelas. Todo Project novo vem com Todo, In Progress e Done: substitua pelas seis etapas da [visão geral](index.md#etapas-do-quadro), na mesma ordem.

### Prioridade

A prioridade responde a uma pergunta: **o que deve ser feito primeiro?** Padrões comuns:

| Padrão | Valores | Indicado para |
| :--- | :--- | :--- |
| Níveis numerados | `P0`, `P1`, `P2`, `P3` | Equipes técnicas. Curto e sem ambiguidade de ordem. É o padrão dos templates do GitHub |
| Descritivo | `Urgente`, `Alta`, `Média`, `Baixa` | Projects acompanhados pela área de negócio |
| MoSCoW | `Must`, `Should`, `Could`, `Won't` | Negociação de escopo de uma entrega |

Seja qual for o padrão, defina o critério de cada nível. Exemplo com níveis numerados:

| Nível | Critério |
| :--- | :--- |
| **P0** | Incidente em produção. Interrompe o trabalho em andamento |
| **P1** | Essencial para a entrega atual |
| **P2** | Importante, sem urgência. Próximas entregas |
| **P3** | Desejável. Revisto periodicamente |

### Área

Os valores seguem as camadas do Hub MDM. Uma issue que afeta mais de uma camada recebe a área onde está a maior parte da mudança.

| Valor | Escopo |
| :--- | :--- |
| **Ingestão** | Views de ingestão dos sistemas de origem e carga na staging |
| **Higienização** | Padronização, validação e enriquecimento (ex.: validação de CPF, tradução de domínios) |
| **Matching** | Blocagem, passos de comparação e notas de corte |
| **Sobrevivência** | Regras que compõem o Golden Record |
| **Publicação** | Carga da base unificada e entrega aos consumidores |
| **Curadoria** | Tratamento de duvidosos e inválidos, split e merge |
| **Orquestração** | DAGs, agendamento e reprocessamento no Airflow |
| **Infra** | Imagens Docker e ambientes |

### Tamanho

| Valor | Referência |
| :--- | :--- |
| **XS** | Menos de meio dia |
| **S** | Até um dia |
| **M** | Dois a três dias |
| **L** | Até uma semana. Avalie a divisão. |
| **XL** | Mais de uma semana. **Deve ser dividido** antes de sair do Backlog. |

## Views

| View | Layout | Configuração | Finalidade |
| :--- | :--- | :--- | :--- |
| **Quadro** | Board | Colunas por Status, filtro `-label:entrega`, slice por Parent issue | Acompanhamento do trabalho |
| **Entregas** | Table | Filtro `label:entrega`, campos Data alvo e Sub-issues progress, ordenar por Data alvo | Visão das entregas em andamento |
| **Roadmap** | Roadmap | Filtro `label:entrega`, datas por Início e Data alvo | Planejamento de médio prazo |
| **Backlog** | Table | Filtro `status:Backlog`, agrupar por Parent issue, ordenar por Prioridade | Priorização |
| **Revisão de código** | Table | Filtro `status:Waiting`, campo Linked pull requests | Fila de revisores |
| **Homologação** | Table | Filtro `status:Homologate`, campo Linked pull requests | Fila de homologação |
| **Minhas tarefas** | Table | Filtro `assignee:@me -status:Done` | Foco individual |

!!! info "Limite de WIP"
    Na view **Quadro**, defina limites nas colunas **Doing**, **Waiting** e **Homologate** (menu da coluna → **Set limit**). Valores de referência em [Entregas](entregas.md#limite-de-trabalho-em-andamento).

## Rascunhos

- Use rascunhos (*draft issues*) apenas para ideias ainda não refinadas.
- Converta em issue (**Convert to issue**) antes de priorizar.
- Rascunhos não aceitam branch, pull request nem vínculo de parent issue.

## Checklist

- [ ] Project criado na organização, com nome no padrão.
- [ ] Acesso de escrita concedido ao time.
- [ ] Repositórios vinculados.
- [ ] Status com as seis etapas; campos Prioridade, Tamanho, Área, Início e Data alvo criados, com padrão de nomes definido.
- [ ] Label `entrega` criada em todos os repositórios vinculados.
- [ ] Sete views padrão criadas.
- [ ] README do Project com o objetivo da equipe e o link para este guia.
