# hub-client-side

**Versão:** v0.1 — 2026-06-16
**Repositório:** hub-client-side
**Mantenedor:** victorarimatea
**Tipo:** agregador de pacotes instaláveis (client-side)

---

## O que é este repositório

`hub-client-side` é o repositório de **pacotes instaláveis** do ecossistema ATLAS.

Cada pacote é uma versão **client-side** de uma skill ou workflow canônico do ecossistema:
ela pode ser copiada diretamente para a ferramenta do usuário (ex: Claude Project Knowledge)
e opera com ou sem token de acesso ao ecossistema.

### Client-side vs Server-side

| Dimensão | Server-side (canônico) | Client-side (instalável) |
|---|---|---|
| **Onde vive** | Repositório do ecossistema (GitHub) | Ferramenta do usuário (ex: Claude) |
| **Fonte de verdade** | Sempre | Nunca — consulta o server-side |
| **Requer token** | Sim (para escrita) | Não (opera em modo fallback sem token) |
| **Papel no ecossistema** | Lê e escreve | Apenas lê — nunca escreve diretamente |
| **Atualização** | Mantida pelo mantenedor | Auto-verifica versão quando há token |

---

## Pacotes disponíveis

| Código | Arquivo | Origem canônica | Versão local | Importado em |
|---|---|---|---|---|
| S06-CS | `SKILL-registro-reuniao-clientside.md` | `skl-registro-reuniao` (S06) v1.0 | v1.0 | 2026-06-15 |
| S-CSC 🧪 | `SKILL-criador-skill-clientside.md` | *sem canônico ainda* — proto | v1.0 | 2026-06-15 |

> **S-CSC** é uma meta-skill em estado `🧪 em teste`. Quando o ecossistema tiver
> a versão server-side canônica, esta cópia passa a ser sua própria client-side.

---

## Como instalar

1. Abra o arquivo da skill desejada neste repositório
2. Copie o conteúdo completo
3. Cole em **Project Knowledge** do seu projeto Claude (ou equivalente na sua ferramenta)
4. A skill estará disponível automaticamente nas sessões daquele projeto

**Sem token:** a skill opera com o padrão embutido (versão da data de importação).
**Com token de leitura:** a skill verifica automaticamente se há versão mais recente no ecossistema.

---

## Disclosure de compatibilidade

- Pacotes testados com Claude Sonnet (versão vigente em junho/2026)
- Skills client-side **não têm papel de escrita** no ecossistema — nunca modificam repositórios diretamente
- Sem garantia de retrocompatibilidade entre versões — verificar `INDICE.md` antes de atualizar
- Em caso de divergência entre padrão local e ecossistema, o padrão do ecossistema prevalece

---

## Bloco para agentes de IA

> **Leitura obrigatória para qualquer agente que opere neste repositório.**

| Marcação | Significado | Comportamento esperado |
|---|---|---|
| 🔒 | Interno — relevância de rastreabilidade | Registrar normalmente |
| 📊 | Estratégico — valor narrativo para SETIS | Destacar em resumos executivos |
| 🌐 | Público — comunicável externamente | Incluir em materiais de divulgação |
| 🧪 | Em teste — experimentação ativa, não vinculante | Reportar ao final de cada sessão; não bloqueia operações |

**Sobre elementos 🧪:** não são artefatos — são calculados na leitura. Reportar comportamento
observado ao final de cada sessão. Validação formal ocorre em sessões W04.

**Regra de acesso a este repositório:**
- Leitura: token de leitura ou acesso público (repositório público)
- Escrita: token de escrita — apenas o mantenedor (victorarimatea)
- Skills client-side aqui depositadas **não escrevem** neste repositório durante execução

---

## Versionamento

Este repositório segue **versionamento source-only**: a versão vive na ficha técnica
de cada arquivo. O `INDICE.md` é a referência central de versões dos pacotes.

---

## Links do ecossistema

| Recurso | URL |
|---|---|
| Ecossistema ATLAS (porta de entrada) | https://github.com/victorarimatea/hub-entrada |
| Fonte de verdade canônica | https://github.com/victorarimatea/hub-fonte |
| Orquestração GitHub (S04) | https://github.com/victorarimatea/skl-github-orquestracao |
