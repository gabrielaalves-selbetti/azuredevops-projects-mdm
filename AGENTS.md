# AGENTS.md

Orientações para agentes de IA que trabalham neste repositório.

## O que estamos desenvolvendo

Um **guia de padronização do uso do GitHub Projects**, publicado como site MkDocs Material. O público é experiente: o guia define convenções, não ensina a ferramenta. Ele cobre o ciclo completo de um novo recurso:

entrega → issues → Project → branch → commits → pull request → code review → homologação → merge.

O trabalho é organizado **por entregas**, sem sprints nem ciclos de tempo fixo. As etapas do campo **Status** no Project são, nesta ordem e com estes nomes exatos: **Backlog → To-do → Doing → Waiting → Homologate → Done**.

- **Waiting** = aguardando code review.
- **Homologate** = validação funcional **antes** do merge.
- **Entrega** = issue-mãe `[Entrega]` com label `entrega`, campos **Início** e **Data alvo**, e as tarefas como sub-issues.

Fora de escopo (removidos por decisão do responsável): onboarding, papéis da equipe, sprints e eventos do Scrum, padrão de documentação de projetos, automações do Project (workflows e GitHub Actions) e CI. Não reintroduza esses temas.

O foco é integração da equipe e rastreabilidade das tarefas. O status no Project é movido manualmente, por quem executa a ação.

**Contexto:** a equipe desenvolve uma aplicação de **MDM** (Master Data Management) em Python, orquestrada pelo Airflow e empacotada em Docker. Exemplos, valores do campo **Área** e pontos de atenção usam as camadas do MDM: ingestão, higienização, matching, sobrevivência, publicação, curadoria, orquestração e infra. A página `docs/fundamentos.md` explica o propósito de cada recurso do GitHub e os riscos específicos do MDM.

## Stack

- MkDocs com tema `material` (`requirements.txt`).
- Navegação declarada em `nav` no `mkdocs.yml`. Não há `.nav.yml` nem plugin awesome-nav.
- Deploy via `.github/workflows/ci.yml` (`mkdocs build --strict` + `mkdocs gh-deploy`).

## Mapa do repositório

| Caminho | Conteúdo |
| :--- | :--- |
| `docs/index.md` | Página inicial: propósito do guia e contexto MDM |
| `docs/fundamentos.md` | DevOps/DataOps, propósito de cada recurso do GitHub, riscos do MDM, estratégia de branches, dados pessoais |
| `docs/github-projects/` | Visão geral (etapas do quadro), configuração, entregas, issues, branches e commits, pull requests |
| `docs/assets/stylesheets/brand.css` | **Única** fonte de cores e tipografia |
| `resources/` | Brand book de referência. Material interno, **não publicado** |
| `.agents/skills/mkdocs-docs/` | Skill com as regras de escrita de páginas |

## Regras de conteúdo

1. **Nunca cite o nome da empresa**, nomes de unidades de negócio nem as frases de reforço do brand book, em nenhum arquivo publicado ou versionado (páginas, README, commits, PRs). Use termos neutros: "a organização", "a equipe", "corporativo".
2. **Idioma:** português do Brasil. Nomes de telas e botões do GitHub ficam em inglês e em negrito (**New project**, **Ready for review**).
3. **Tom de voz** (do brand book):
    - sério, profissional, objetivo e confiável;
    - frases curtas e diretas, formalidade moderada;
    - tabelas e listas para regras e dados;
    - evitar exageros, termos genéricos e promessas vagas.
4. **Exemplo contínuo:** use a entrega fictícia "Unificação de pessoas físicas" (issue `#120`) e a tarefa "Validação de CPF na higienização" (issue `#123`, branch `feature/123-validar-dv-cpf`), repositório `minha-org/mdm-hub` e Project número `7` ("Dados - MDM"). Não invente outros identificadores sem necessidade.
5. **Dados pessoais:** nenhum exemplo usa CPF, nome ou contato real. Use dados sintéticos ou mascarados.
6. **Consistência:** os valores de campos (Status Backlog/To-do/Doing/Waiting/Homologate/Done, Início, Data alvo), os nomes das views e os padrões de título, branch e commit estão definidos em `docs/github-projects/`. Qualquer mudança deve ser propagada a todas as páginas que os citam. Os nomes dos valores de Prioridade, Tamanho e Área são escolha da equipe: o guia apresenta padrões sugeridos e usa P0–P3 e XS–XL apenas como exemplo.
7. **Identidade visual:** cores e fontes somente via variáveis em `brand.css`:
    - verde `#00754a` e `#006241`;
    - verde escuro `#17392f`;
    - laranja `#ef8943`;
    - fonte Gotham, com Segoe UI como fallback.
   
   Não há logotipo. O cabeçalho usa um ícone Material.

## Como escrever páginas

Siga a skill `.agents/skills/mkdocs-docs/SKILL.md`. Em resumo:

- admonitions em vez de `>`;
- sem sumário manual;
- um `#` por página;
- blocos de código com linguagem e `title=`;
- abas "Interface web" / "gh CLI" para procedimentos;
- Mermaid para fluxos.

Toda página nova entra no `nav` do `mkdocs.yml`.

## Verificação

Antes de concluir qualquer alteração:

```bash
pip install -r requirements.txt
mkdocs build --strict
```

Confira também que o nome da empresa não aparece em nenhum arquivo fora de `resources/`. Por exemplo, busque o nome com `grep -ri` em `docs`, `README.md`, `AGENTS.md`, `mkdocs.yml` e `.agents`.

## Convenções do repositório

Este repositório segue o próprio guia:

- issues `[Docs]`;
- branches `docs/<numero>-descricao`;
- commits `docs(escopo): descrição`;
- PRs com `Closes #<numero>`.
