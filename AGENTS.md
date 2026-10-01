# AGENTS.md

Orientações para agentes de IA que trabalham neste repositório.

## O que estamos desenvolvendo

Um **guia de padronização do uso do Azure DevOps** (Azure Boards e Azure Repos), publicado como site MkDocs Material. O público é experiente em dados, mas tem pouca vivência com DevOps e DataOps: o guia define convenções e explica cada termo de Git ou de fluxo em uma frase na primeira ocorrência, sem virar tutorial da ferramenta. Não mencione o nível de experiência do leitor nos textos publicados. Ele cobre o ciclo completo de um novo recurso:

entrega → work items → board → branch → commits → pull request → code review → homologação → merge.

O trabalho é organizado **por entregas**, sem sprints nem ciclos de tempo fixo. As etapas do campo **State** no Azure Boards são estados customizados de um processo herdado do Agile, nesta ordem e com estes nomes exatos: **Backlog → To-do → Doing → Waiting → Homologate → Done**.

- **Waiting** = aguardando code review.
- **Homologate** = validação funcional na `dev`, **antes** da promoção para a `main`.
- **Entrega** = work item do tipo customizado **Entrega** (nível de portfólio "Entregas"), com **Start Date** e **Target Date**; as tarefas (User Story ou Bug) são filhas dela pelo link **Parent/Child**.
- **Branches** = `main` (homologado), `dev` (integração e homologação) e uma `task/<id>` por work item, criada da `dev`. Fluxo: `task/<id>` → PR para `dev` (squash) → homologação → PR `dev` → `main` (merge sem squash) → **Done**.
- **Commits** = Conventional Commits, semânticos (`tipo(escopo): descrição`).

Fora de escopo (removidos por decisão do responsável): onboarding, papéis da equipe, sprints e eventos do Scrum, padrão de documentação de projetos, automações (regras de processo, service hooks, Azure Pipelines, GitHub Actions) e CI. Sprints incluem **Iteration Path** e o tipo **Task**. Não reintroduza esses temas.

O foco é integração da equipe e rastreabilidade das tarefas. O estado no board é movido manualmente, por quem executa a ação.

**Contexto:** a equipe desenvolve uma aplicação de **MDM** (Master Data Management) em Python, orquestrada pelo Airflow e empacotada em Docker. Exemplos, valores do campo **Área** e pontos de atenção usam as camadas do MDM: ingestão, higienização, matching, sobrevivência, publicação, curadoria, orquestração e infra. A página `docs/fundamentos.md` explica o propósito de cada recurso do Azure DevOps e os riscos específicos do MDM.

## Stack

- MkDocs com tema `material` (`requirements.txt`).
- Navegação declarada em `nav` no `mkdocs.yml`. Não há `.nav.yml` nem plugin awesome-nav.
- O site do guia continua no GitHub Pages: deploy via `.github/workflows/ci.yml` (`mkdocs build --strict` + `mkdocs gh-deploy`). Só o conteúdo trata de Azure DevOps.

## Mapa do repositório

| Caminho | Conteúdo |
| :--- | :--- |
| `docs/index.md` | Página inicial: propósito do guia e contexto MDM |
| `docs/fundamentos.md` | DevOps/DataOps, propósito de cada recurso do Azure DevOps, riscos do MDM, estratégia de branches, dados pessoais |
| `docs/azure-devops/` | Visão geral (etapas do board), configuração do projeto e do processo, entregas, work items, branches e commits, pull requests |
| `docs/assets/stylesheets/brand.css` | **Única** fonte de cores e tipografia. Define o componente `.trilho` (etapas do board) |
| `includes/abreviacoes.md` | Glossário: termos exibidos como tooltip em todas as páginas (`abbr` + `pymdownx.snippets`) |
| `resources/` | Brand book de referência. Material interno, **não publicado** |
| `.claude/skills/mkdocs-docs/` | Skill com as regras de escrita de páginas |

## Regras de conteúdo

1. **Nunca cite o nome da empresa**, nomes de unidades de negócio nem as frases de reforço do brand book, em nenhum arquivo publicado ou versionado (páginas, README, commits, PRs). Use termos neutros: "a organização", "a equipe", "corporativo".
2. **Idioma:** português do Brasil. Nomes de telas, campos e botões do Azure DevOps ficam em inglês e em negrito (**New project**, **Publish**, **Wait for author**).
3. **Tom de voz** (do brand book):
    - sério, profissional, objetivo e confiável;
    - frases curtas e diretas, formalidade moderada;
    - tabelas e listas para regras e dados;
    - evitar exageros, termos genéricos e promessas vagas.
4. **Exemplo contínuo:** use a entrega fictícia "Unificação de pessoas físicas" (work item **Entrega** `#120`) e a tarefa "Validação de CPF na higienização" (User Story `#123`, branch `task/123`), organização `minha-org`, projeto "Dados - MDM", repositório `mdm-hub`, processo "Agile MDM". Não invente outros identificadores sem necessidade.
5. **Dados pessoais:** nenhum exemplo usa CPF, nome ou contato real. Use dados sintéticos ou mascarados.
6. **Consistência:** os valores de campos (State Backlog/To-do/Doing/Waiting/Homologate/Done, Start Date, Target Date, Area Path), os tipos de work item, as visões (board, backlog, Delivery Plan, queries) e os padrões de título, branch (`task/<id>`) e commit estão definidos em `docs/azure-devops/`. Qualquer mudança deve ser propagada a todas as páginas que os citam. Os nomes dos valores de Prioridade, Tamanho e Área são escolha da equipe: o guia apresenta padrões sugeridos e usa o campo nativo Priority (1 a 4, lidos como P0–P3) e XS–XL apenas como exemplo.
7. **Identidade visual:** cores e fontes somente via variáveis em `brand.css`:
    - verde `#00754a` e `#006241`;
    - verde escuro `#17392f`;
    - laranja `#ef8943`;
    - fonte Gotham, com Segoe UI como fallback.
   
   Não há logotipo. O cabeçalho usa um ícone Material.

## Como escrever páginas

Siga a skill `.claude/skills/mkdocs-docs/SKILL.md`. As skills `azure-boards` e `azure-repos` apontam para a documentação oficial: confira nomes de botões, campos e flags do `az` antes de escrevê-los. Em resumo:

- admonitions em vez de `>`;
- sem sumário manual;
- um `#` por página;
- blocos de código com linguagem e `title=`;
- abas "Interface web" / "az CLI" para procedimentos (`az` com a extensão `azure-devops`);
- Mermaid para fluxos.

Toda página nova entra no `nav` do `mkdocs.yml`.

## Verificação

Antes de concluir qualquer alteração:

```bash
pip install -r requirements.txt
mkdocs build --strict
```

Confira também que o nome da empresa não aparece em nenhum arquivo fora de `resources/`. Por exemplo, busque o nome com `grep -ri` em `docs`, `README.md`, `AGENTS.md`, `mkdocs.yml` e `.claude/skills/mkdocs-docs`.

## Convenções do repositório

O repositório deste guia fica no GitHub e segue os padrões de nome do guia:

- issues `[Docs]`;
- branches `docs/<numero>-descricao`;
- commits `docs(escopo): descrição`;
- PRs com `Closes #<numero>`.
