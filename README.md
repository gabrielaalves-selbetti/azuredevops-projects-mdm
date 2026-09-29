# Guia GitHub Projects

Guia de padronização do uso do **GitHub Projects** para a equipe que desenvolve a aplicação de MDM. Ele define convenções para registrar demandas, organizar o trabalho por entregas, desenvolver, revisar, homologar e entregar. O objetivo é previsibilidade e integração entre pessoas e tarefas.

O conteúdo é publicado como site estático com [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) no GitHub Pages.

## O que o guia cobre

| Seção | Conteúdo |
| :--- | :--- |
| Início | Propósito do guia e contexto MDM |
| Fundamentos | Propósito de cada recurso do GitHub, riscos do MDM, estratégia de branches e dados pessoais |
| GitHub Projects | Etapas do quadro, configuração do Project, entregas, issues, branches e commits e pull requests |

## Estrutura do repositório

```text
.
├── docs/                      # Conteúdo do site (Markdown)
│   ├── assets/                # CSS da identidade visual e favicon
│   ├── github-projects/       # Seção principal do guia
│   └── index.md
├── resources/                 # Material de referência interno (não publicado)
├── .agents/skills/            # Instruções para agentes de IA
├── .github/workflows/ci.yml   # Build e deploy no GitHub Pages
├── AGENTS.md                  # Orientações para agentes de IA
├── mkdocs.yml                 # Configuração do site e navegação
└── requirements.txt
```

## Executar localmente

Pré-requisito: Python 3.9 ou superior.

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

Acesse `http://127.0.0.1:8000`. As alterações em `docs/` recarregam a página automaticamente.

Para validar o build como no CI:

```bash
mkdocs build --strict
```

## Publicação

Cada push na `main` executa o workflow `.github/workflows/ci.yml`, que valida o build (`--strict`) e publica o site na branch `gh-pages` com `mkdocs gh-deploy`.

## Como contribuir

Este repositório segue o próprio guia:

1. Abra uma issue `[Docs]` descrevendo a mudança.
2. Crie a branch a partir da issue (`docs/<numero>-descricao`).
3. Faça commits no padrão Conventional Commits (`docs(escopo): ...`).
4. Abra um pull request com `Closes #<numero>` e solicite revisão.

Novas páginas devem ser incluídas na seção `nav` do `mkdocs.yml`.

## Identidade visual

O site aplica a identidade visual corporativa, centralizada em `docs/assets/stylesheets/brand.css`:

| Uso | Cor |
| :--- | :--- |
| Primária | `#00754a` |
| Primária escura | `#006241` |
| Fundo escuro / cabeçalho no tema escuro | `#17392f` |
| Destaque | `#ef8943` |

Tipografia: Gotham, com Segoe UI como alternativa digital. O texto segue tom sério, objetivo e confiável, com frases curtas e uso de tabelas e listas.
