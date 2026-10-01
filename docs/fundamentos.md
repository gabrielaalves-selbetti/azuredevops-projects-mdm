# Fundamentos

Esta página explica **por que** a equipe usa o Azure DevOps da forma definida neste guia. As regras das demais páginas partem daqui.

## Ciclo completo

Toda mudança no código percorre o mesmo caminho, do registro da demanda até a versão publicada:

```mermaid
flowchart LR
    I[Work item] --> B["task/id"] --> C[Commits] --> PR[PR para a dev]
    PR --> R[Code review]
    R -->|ajustes| C
    R -->|aprovado| D[Merge na dev]
    D --> H[Homologação]
    H -->|reprovado| C
    H -->|aprovado| M[PR dev → main]
    M --> V[Tag e imagem]
```

O passo a passo, com o estado do board em cada etapa, está em [Uma tarefa do início ao fim](azure-devops/index.md#uma-tarefa-do-inicio-ao-fim).

## DevOps e DataOps

DevOps é um conjunto de práticas para publicar mudanças de software com segurança: mudanças pequenas, registradas, revisadas e validadas antes de chegar à produção. **DataOps** aplica as mesmas práticas a dados, com uma exigência adicional: saber qual versão do código gerou cada dado.

Neste guia, essas práticas aparecem como:

- **Trabalho visível.** Toda demanda é um work item, e todo código passa por pull request.
- **Histórico completo.** Cada alteração fica registrada com autor, data e motivo.
- **Revisão por outra pessoa.** Nenhuma mudança é integrada sem code review.
- **Validação antes da produção.** A mudança é homologada com dados representativos.
- **Segurança desde o início.** Credenciais e dados pessoais ficam fora do repositório.

## Propósito de cada recurso

No MDM, o código define como os cadastros são padronizados, unificados e publicados. Por isso, cada mudança precisa ser rastreável até a decisão que a originou.

| Recurso | O que é | No MDM |
| :--- | :--- | :--- |
| **Repositório (Azure Repos)** | Pasta do código com todo o histórico de alterações. Permite comparar e desfazer versões | Regras de higienização, passos de matching, regras de sobrevivência e DAGs do Airflow ficam versionados |
| **Work item** | Registro de uma demanda, com contexto e critérios de aceitação | Documenta o motivo de uma mudança de regra: qual problema de cadastro ela resolve |
| **Azure Boards** | Quadro com o andamento de todos os work items | Visão das entregas do Hub MDM e do que está em revisão ou homologação |
| **Branch** | Cópia isolada do código para trabalhar em uma mudança | Uma nova regra é testada sem alterar a carga que está em produção |
| **Commit** | Ponto de salvamento de uma alteração, com mensagem padronizada | O histórico mostra quando e por que uma nota de corte mudou |
| **Pull request** | Pedido para integrar uma branch a outra, com revisão antes do merge | Espaço para discutir o impacto da regra nos Golden Records |
| **Tag** | Etiqueta que marca uma versão publicada | Identifica qual versão das regras gerou cada carga |

!!! info "Referência"
    Os conceitos de DevOps, colaboração e integração contínua seguem o material [Fundamentos de DevOps](https://eduardo-da-silva.github.io/fundamentos-devops/).

## O que muda em um projeto de MDM

Em uma aplicação comum, um erro costuma afetar uma tela ou uma funcionalidade. No MDM, uma regra errada altera os **Golden Records de toda a base** e se propaga para todos os sistemas que os consomem. Corrigir exige reprocessar a carga e, em alguns casos, desfazer unificações.

| Tipo de mudança | Risco | Cuidado exigido |
| :--- | :--- | :--- |
| Nota de corte ou passo de **matching** | Falso positivo (pessoas diferentes unificadas) ou falso negativo (mesma pessoa separada) | Testes com casos conhecidos e comparação de métricas na homologação |
| Regra de **sobrevivência** | A ordem das regras muda o valor que sobrevive | Testes por grupo de duplicados e aprovação do responsável pelo domínio |
| Regra de **higienização** | Registros válidos enviados para a fila de inválidos, ou o contrário | Teste de regressão para cada correção |
| Layout das **views de ingestão** | Quebra o contrato com os sistemas de origem | Mudança incompatível: commit com `!` e comunicação às origens |
| **DAG** do Airflow | Janela de dados errada ou reprocessamento que duplica registros | Tasks idempotentes e revisão do agendamento |
| **Imagem** Docker | Versão incerta em produção | Tag fixa (hash do commit ou versão), nunca `latest` |

Esses riscos explicam três regras centrais do guia:

- **Homologação antes da main.** A tarefa passa pela `dev`, onde é homologada com dados representativos. A `main` recebe apenas regras validadas. Veja [Homologação](azure-devops/pull-requests.md#homologacao).
- **Pull requests pequenos.** Uma regra por pull request. Assim é possível medir o efeito de cada mudança isoladamente.
- **Teste para cada bug.** Todo erro de regra corrigido ganha um teste de regressão: um teste que reproduz o erro e impede que ele volte.

## Estratégia de branches

A equipe adota um modelo simples, com duas branches permanentes e uma branch curta por tarefa:

| Branch | Papel |
| :--- | :--- |
| `main` | Código homologado. Origem das versões em produção |
| `dev` | Integração e homologação. Recebe as tarefas revisadas |
| `task/<id>` | Uma por work item. Nasce da `dev` e volta para ela por pull request |

O fluxo completo está em [Fluxo de uma tarefa](azure-devops/branches-e-commits.md#fluxo-de-uma-tarefa).

??? question "Por que não outras estratégias?"

    | Estratégia | Por que não é o padrão |
    | :--- | :--- |
    | **GitFlow** (`develop`, `release/`, `hotfix/`) | Mais branches para sincronizar. A `dev` já cumpre o papel de homologação |
    | **Trunk-based** | Integração direta na principal, sem uma etapa de homologação antes da produção |

## Dados pessoais

O MDM trata dados pessoais protegidos pela LGPD. O repositório e o Azure Boards são lidos por toda a equipe e guardam o histórico para sempre: o que foi registrado uma vez continua acessível, mesmo depois de apagado do arquivo.

!!! danger "Nunca registre dados reais no Azure DevOps"
    CPF, CNPJ, nomes, endereços, telefones e e-mails reais não entram em work items, comentários, anexos, pull requests, commits, testes ou logs anexados. Use dados sintéticos ou mascarados (ex.: `***.456.789-**`). Credenciais ficam em um cofre de segredos (ex.: Azure Key Vault) ou em `.env` fora do Git.
