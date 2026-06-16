# SKILL — Registro de Reunião
**Código:** S06-CS (client-side)
**Versão local:** v1.0
**Importada de:** `skl-registro-reuniao` (S06) — v1.0 — 2026-06-02
**Data de importação:** 2026-06-15
**Ecossistema:** DTD/SETIS/SES-DF — github.com/victorarimatea

> **Nota de design:** esta skill é um acionador local com padrão embutido como
> fallback. A fonte de verdade canônica é sempre a S06 no ecossistema. Quando
> há token de leitura disponível, esta skill verifica automaticamente se houve
> atualização de versão antes de operar.

---

## Como acionar esta skill

Frases que ativam esta skill:
- "registrar reunião"
- "fazer a ata"
- "gerar o registro"
- "processar essa reunião"
- Colar um resumo/transcrição de reunião no chat

---

## Modos de operação

### MODO 1 — Sem token (fallback)
Operação com padrão local embutido (versão importada em 2026-06-15).

**Comportamento:**
1. Alertar o usuário: *"Operando em modo offline — padrão local v1.0 importado
   em 2026-06-15. Pode haver atualizações no ecossistema não refletidas aqui."*
2. Executar as Etapas 0–5 com o padrão embutido abaixo
3. Entregar o registro no chat com instrução de depósito manual
4. Registrar ao final: *"Para depositar no ecossistema, inicie micro-sessão com
   token de escrita e informe o caminho:
   `agd-dtd/reunioes/AAAA/MM/[nome-do-arquivo].md`"*

### MODO 2 — Com token de leitura
Operação com verificação de versão e padrão atualizado do ecossistema.

**Comportamento:**
1. Ler `skl-registro-reuniao/SKILL.md` via API GitHub
2. Extrair campo `**Versão:**` do cabeçalho
3. Comparar com versão local embutida (v1.0)
4. **Se versão igual:** prosseguir normalmente com padrão lido do ecossistema
5. **Se versão diferente:** alertar o usuário:
   *"⚠️ A S06 no ecossistema está em [versão nova] — esta skill local está em
   v1.0 (importada em 2026-06-15). Recomendo atualizar o padrão embutido
   aqui em uma sessão de curadoria. Prosseguindo com o padrão mais recente
   do ecossistema para esta execução."*
6. Executar com o padrão lido do ecossistema (não o local)
7. Entregar o registro no chat para revisão e aprovação

### MODO 3 — Com token de escrita
Tudo do Modo 2, mais:

**Comportamento adicional após aprovação do registro:**
1. Acionar S04 para depositar arquivo em
   `agd-dtd/reunioes/AAAA/MM/[nome-do-arquivo].md`
2. Atualizar `agd-dtd/INDICE.md` com nova entrada
3. Se reunião for de projeto: adicionar referência em
   `[repositorio-projeto]/EXECUCOES.md` (sem duplicar conteúdo)
4. Confirmar depósito com GET após PUT (aguardar ~3s)

---

## Padrão embutido (fallback — S06 v1.0 importada em 2026-06-15)

### ETAPA 0 — Identificação do contexto

Antes de processar, perguntar ao usuário se necessário:

1. **A reunião é associada a um projeto formal?**
   - Sim → identificar qual (ex: P01 `prj-telessaude-poc-prisional`)
   - Não → reunião avulsa — destino: `agd-dtd` apenas

2. **Qual a unidade organizacional?**
   - Padrão: DTD/SETIS/SES-DF

3. **Há token de escrita disponível?**
   - Sim → Modo 3 após aprovação
   - Não → entrega no chat apenas

---

### ETAPA 1 — Recebimento do input

Aceitar qualquer formato:
- Síntese do PLAUD NOTE Pro
- Transcrição bruta
- Anotações livres
- Combinação de fontes

**Se input do PLAUD NOTE:** aplicar correção semântica antes de processar
(SEAPE, IGES-DF, SaMD, INMSD, siglas do ecossistema SES-DF).

**Se input parecer truncado:** informar e pedir confirmação antes de prosseguir.

---

### ETAPA 2 — Geração do registro institucional

Aplicar o seguinte padrão ao input recebido, produzindo documento com
**8 seções obrigatórias:**

---

**1. TÍTULO DA REUNIÃO**
Formato: `[CLASSIFICAÇÃO] nº XX — Nome sintético e claro`

Classificações possíveis:
- ALINHAMENTO ESTRATÉGICO
- ALINHAMENTO TÁTICO
- ALINHAMENTO OPERACIONAL
- ALINHAMENTO TÉCNICO
- ALINHAMENTO TÉCNICO-OPERACIONAL
- ALINHAMENTO TÉCNICO-ESTRATÉGICO

Se número sequencial não disponível: usar `nº —` e marcar como pendente.

**2. METADADOS**
- Data:
- Local:
- Participantes: (nome, cargo/função, instituição)
- Formato: (presencial / remoto / híbrido)

**3. CONTEXTO**
Parágrafo curto: por que a reunião aconteceu, em que etapa do projeto
se insere, qual o cenário institucional relevante.

**4. PRINCIPAIS PONTOS DISCUTIDOS**
Blocos temáticos com título em negrito. Síntese — não transcrição literal.

**5. DECISÕES**
Apenas decisões efetivamente tomadas.
Formato: verbo no passado + objeto + responsável.
Se nenhuma: registrar explicitamente "Nenhuma decisão formal tomada nesta reunião."

**6. ENCAMINHAMENTOS**
Ações práticas decorrentes. Formato: ação + responsável + prazo.
Se nenhum: registrar "Nenhum encaminhamento formal registrado."

**7. PONTOS CRÍTICOS IDENTIFICADOS**
Riscos, lacunas, dependências, fragilidades. Sinalizar impacto quando identificável.

**8. REFERÊNCIA**
"Registro elaborado a partir de síntese estruturada gerada por [fonte] em [data]."

---

**Diretrizes de qualidade:**
- Linguagem institucional (padrão SES-DF / administração pública)
- Clareza > volume de texto
- Não copiar o original — interpretar e estruturar
- Inferir com cautela, nunca inventar
- Corrigir erros de transcrição semanticamente

---

### ETAPA 3 — Montagem do arquivo Markdown

**Front Matter YAML obrigatório:**
```yaml
---
id_registro: AAAA-MM-DD-[classificacao-slug]-[descricao-slug]
titulo:
classificacao:
numero_sequencial:
data_reuniao:
local:
formato:
participantes:
projeto_associado:      # ID do projeto P, ou "avulso"
unidade:                # ex: DTD/SETIS/SES-DF
data_registro:          # data de geração deste arquivo
versao_registro: 1.0
---
```

**Nomenclatura do arquivo:**
```
AAAA-MM-DD-[CLASSIFICACAO]-[descricao-curta].md
```

Exemplos:
- `2026-06-15-ALINHAMENTO-ESTRATEGICO-telessaude-poc-prisional.md`
- `2026-06-15-ALINHAMENTO-TECNICO-revisao-pdtic.md`

---

### ETAPA 4 — Checklist de alerta pré-entrega

Antes de entregar, verificar e alertar o usuário sobre qualquer item ausente:

| Critério | Status |
|---|---|
| Front Matter YAML com todos os campos | ✅ / ⚠️ pendente: [campo] |
| 8 seções presentes | ✅ / ⚠️ ausente: [seção] |
| DECISÕES preenchida ou com nota de ausência | ✅ / ⚠️ |
| ENCAMINHAMENTOS preenchido ou com nota de ausência | ✅ / ⚠️ |
| Nomenclatura do arquivo correta | ✅ / ⚠️ |
| Linguagem institucional | ✅ / ⚠️ |
| Número sequencial definido | ✅ / ⚠️ pendente — preencher antes do SEI |
| Participantes com cargo e instituição | ✅ / ⚠️ incompleto: [nome] |

---

### ETAPA 5 — Entrega

Apresentar ao usuário:
1. O documento completo formatado
2. O nome sugerido do arquivo
3. O checklist de status (Etapa 4)
4. Instrução de próximo passo (depósito manual ou via token de escrita)

---

## Destinos no ecossistema

| Tipo de reunião | Destino principal | Referência adicional |
|---|---|---|
| Avulsa | `agd-dtd/reunioes/AAAA/MM/` | — |
| De projeto | `agd-dtd/reunioes/AAAA/MM/` | `[projeto]/EXECUCOES.md` |
| Interinstitucional | `agd-dtd/reunioes/AAAA/MM/` | nota de distribuição |

Atualizar sempre: `agd-dtd/INDICE.md` — ordenado por data de ocorrência.

---

## Endereços do ecossistema (para verificação de versão no Modo 2/3)

| Recurso | Repositório | Caminho |
|---|---|---|
| Padrão canônico | `skl-registro-reuniao` | `SKILL.md` |
| Workflow completo | `wkf-registro-reuniao` | `WORKFLOW.md` |
| Destino dos registros | `agd-dtd` | `reunioes/AAAA/MM/` |
| Índice cronológico | `agd-dtd` | `INDICE.md` |
| Orquestração GitHub | `skl-github-orquestracao` | `SKILL.md` |

Base URL: `https://api.github.com/repos/victorarimatea/[repo]/contents/[path]`

---

## Princípios desta skill

| Princípio | Definição |
|---|---|
| **Fonte única de verdade** | O padrão canônico vive no ecossistema (S06). Esta skill é acionador + fallback. |
| **Transparência de versão** | Sempre informar em qual versão está operando e se há divergência com o ecossistema. |
| **Graceful degradation** | Sem token, entrega algo útil. Com token, entrega o melhor possível. |
| **Aprovação antes de escrita** | O registro é sempre apresentado ao usuário antes de qualquer depósito no ecossistema. |
| **Referência, não duplicação** | Reunião de projeto: arquivo no A, referência no P. Nunca duplicar conteúdo. |

---

## Intenção do Comandante

O estado final desejado desta skill é que qualquer reunião da DTD/SETIS/SES-DF
possa ser registrada com qualidade institucional, independentemente de o usuário
estar em uma sessão formal do ecossistema ou em modo rápido sem token.
A skill nunca deixa o usuário sem produto — degrada com graciosidade,
entrega o melhor possível no contexto disponível, e sempre sinaliza
a distância entre o que entregou e o ideal canônico.
