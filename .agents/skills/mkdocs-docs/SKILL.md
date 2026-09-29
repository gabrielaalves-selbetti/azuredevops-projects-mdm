---
name: mkdocs-docs
description: Write or edit documentation in this repository under docs/. The site is the corporate GitHub Projects guide, built with mkdocs-material; navigation is declared in the `nav` section of mkdocs.yml. Use this skill whenever the user asks you to add, write, edit, restructure, or review any file under docs/ — including new pages, how-to guides, examples, and updates to the nav in mkdocs.yml. Trigger even when the user does not explicitly say "mkdocs" — any work that produces or modifies a Markdown file under docs/ should use this skill so the output uses admonitions (not `>` blockquotes for notes), omits manual tables of contents, follows the brand tone of voice, and takes advantage of mkdocs-material features (tabs, code annotations, mermaid, collapsible blocks, cards).
---

# Escrevendo páginas do Guia GitHub Projects (mkdocs-material)

O site é gerado pelo `mkdocs-material` a partir do `mkdocs.yml` na raiz. A navegação fica na chave `nav` do próprio `mkdocs.yml`. O leitor vê um site com tema e recursos em JavaScript, não o Markdown cru do GitHub: escreva para esse formato.

Leia também o `AGENTS.md` na raiz, que traz as regras de conteúdo, o tom de voz e o exemplo contínuo.

Referências:
- mkdocs-material: https://squidfunk.github.io/mkdocs-material/reference/

## Regras obrigatórias

1. **Nunca cite o nome da empresa**, unidades de negócio ou frases de reforço do brand book. Use "a organização", "a equipe", "corporativo".
2. **Nunca escreva um sumário manual.** O sumário lateral é gerado a partir dos títulos (`toc` + `toc.follow`).
3. **Nunca use blockquote `>` para notas, avisos ou dicas.** Use admonitions. Citações reais de pessoas ou especificações podem continuar com `>`.
4. **Não adicione listas de navegação** ("veja também página X / Y") no fim das páginas. Referências cruzadas dentro do texto são permitidas.
5. **Não mova páginas existentes sem necessidade.** A URL deriva do caminho do arquivo. Ao renomear, atualize todos os links internos.
6. **Páginas novas entram no `nav` do `mkdocs.yml`**, na seção adequada.
7. **Um `#` por página** (título). Seções usam `##` e abaixo.
8. **Português do Brasil.** Nomes de telas e botões do GitHub ficam em inglês e em negrito (**New project**).

## Tom de voz

- Sério, profissional, objetivo e confiável.
- Frases curtas e diretas, com formalidade moderada.
- Regras e comparações em tabelas e listas.
- Sem exageros, termos genéricos ou promessas vagas. Não use emojis no texto corrido.

## Admonitions

Habilitadas via `admonition` + `pymdownx.details`.

```markdown
!!! note
    Nota simples. A linha em branco e a indentação de 4 espaços são obrigatórias.

!!! warning "Título personalizado"
    Aviso com título.

??? note "Clique para expandir"
    Recolhido por padrão.

???+ note "Aberto, mas recolhível"
    Conteúdo longo opcional.
```

Tipos: `note`, `abstract`, `info`, `tip`, `success`, `question`, `warning`, `failure`, `danger`, `bug`, `example`, `quote`. Escolha o tipo que corresponde ao conteúdo, sem usar `note` para tudo.

## Blocos de código

Sempre com linguagem. Use `title=` para caminhos de arquivo ou para descrever o bloco:

````markdown
```yaml title=".github/ISSUE_TEMPLATE/feature.yml"
name: Nova funcionalidade
```
````

Atributos úteis: `linenums="1"` e `hl_lines="2 4-6"`.

**Anotações de código** (`content.code.annotate`):

````markdown
```bash
gh auth refresh -s project  # (1)!
```

1. Adiciona o escopo necessário para `gh project`.
````

## Abas

Use para "diferentes formas de fazer a mesma coisa". Mantenha os rótulos padrão do site, **"Interface web"** e **"gh CLI"**, para que a escolha persista entre páginas (`content.tabs.link`):

````markdown
=== "Interface web"

    1. Acesse **Projects** → **New project**.

=== "gh CLI"

    ```bash
    gh project create --owner minha-org --title "Dados - MDM"
    ```
````

## Diagramas

Mermaid está configurado no `pymdownx.superfences`. Prefira Mermaid a ASCII para fluxos, estados e sequências:

````markdown
```mermaid
flowchart LR
    Backlog --> ToDo[To-do] --> Doing --> Waiting --> Homologate --> Done
```
````

## Outros recursos habilitados

- **Listas de definição** (`def_list`): termo e definição.
- **Abreviações** (`abbr`): `*[WIP]: Work in progress`.
- **Teclas** (`pymdownx.keys`): `++ctrl+k++`.
- **Checklists** (`pymdownx.tasklist`): `- [ ] item`.
- **Ícones** (`pymdownx.emoji`): `:material-rocket:`.
- **Cards**: `<div class="grid cards" markdown>` com lista, como em `docs/index.md`.
- **md_in_html** e **attr_list**: Markdown dentro de `<div markdown>` e atributos `{ .classe }`.

## Navegação (`mkdocs.yml`)

```yaml
nav:
  - Início: index.md
  - GitHub Projects:
      - github-projects/index.md          # usa o H1 da página como rótulo
      - Issues: github-projects/issues.md
```

## Convenções

- Nomes de arquivo em kebab-case, em português, sem acentos (`branches-e-commits.md`).
- Links internos relativos: `[Entregas](entregas.md#planejamento)`. Âncoras são geradas sem acentos (`#definicao-de-pronto`).
- Imagens e ativos ficam em `docs/assets/`.
- Cores e fontes ficam apenas em `docs/assets/stylesheets/brand.css`. Não use estilos inline com cores.

## Ao concluir uma alteração

1. As notas usam admonitions? Não há sumário nem navegação manual?
2. A página nova está no `nav`?
3. Os links internos de arquivos renomeados foram atualizados?
4. Execute `mkdocs build --strict` na raiz. O mesmo comando roda no CI.
5. O nome da empresa não aparece no texto?
6. Para revisão visual, sugira `mkdocs serve` (`http://127.0.0.1:8000`).
