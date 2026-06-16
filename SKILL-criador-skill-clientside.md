# SKILL — Criador de Skill Client-Side
**Código:** S-CSC 🧪 (meta-skill proto)
**Versão local:** v1.0
**Data de criação:** 2026-06-15
**ID reservado:** S08
**Ecossistema:** DTD/SETIS/SES-DF — github.com/victorarimatea
**Tipo:** meta-skill client-side — cria outras skills client-side

> **Natureza desta skill:** esta é uma meta-skill em estado `🧪 em teste`.
> Seu produto não é um documento ou registro — é outra skill. Ela converte
> skills e workflows server-side (canônicos no ecossistema) em versões
> client-side (instaláveis na ferramenta do usuário).
>
> **Sobre o estado 🧪:** este proto não bloqueia operações. É reportado ao
> final de cada sessão W06 e validado formalmente em sessões W04.
> Veredito possível: validar (promove a definitivo) · ajustar · descartar.
>
> **Migração futura:** quando o ecossistema tiver a versão server-side
> canônica desta meta-skill (ID S08), esta cópia passa a ser sua própria
> versão client-side.

---

## Como acionar esta skill

Frases que ativam esta skill:
- "criar skill client-side de [nome]"
- "converter [skill/workflow] para client-side"
- "quero instalar [skill] no Claude"
- "gerar versão instalável de [workflow]"
- "fazer client-side do [código]"

---

## Pré-requisito: token de leitura

Esta skill **sempre requer token de leitura** para funcionar corretamente.
Sem token, não é possível ler a skill/workflow alvo no ecossistema, mapear
dependências nem verificar versões.

Se acionada sem token disponível:
> "⚠️ Esta skill requer token de leitura para acessar o ecossistema e
> analisar a skill/workflow alvo. Por favor, forneça o token de leitura
> para prosseguir. Sem ele, não consigo garantir que a versão client-side
> gerada reflita o estado atual do ecossistema."

Não prosseguir sem token. Esta é a única skill do ecossistema client-side
sem modo fallback — criar uma skill client-side a partir de memória local
seria gerar um produto potencialmente desatualizado e não confiável.

---

## Etapas de execução

### ETAPA 0 — Identificação do alvo

Solicitar ao usuário, se não informado:

1. **Qual skill ou workflow será convertido?**
   - Código (ex: S06, W02) ou nome descritivo
   - Se nome descritivo: buscar no `hub-fonte/sumario.md` para identificar
     o repositório correto

2. **Token de leitura disponível?**
   - Sim → prosseguir
   - Não → emitir aviso acima e aguardar

---

### ETAPA 1 — Leitura do alvo no ecossistema

Com o token de leitura:

1. Ler `sumario.md` em `hub-fonte` para confirmar repositório e versão atual
2. Ler o arquivo principal da skill/workflow alvo:
   - Skills: `[repo]/SKILL.md`
   - Workflows: `[repo]/WORKFLOW.md`
3. Ler `[repo]/INDICE.md` se existir — pode conter dependências declaradas
4. Registrar: **versão atual**, **data da última atualização**, **intenção
   do comandante** declarada na skill

---

### ETAPA 2 — Mapeamento de dependências

Varrer o conteúdo lido e identificar todas as referências externas.
Classificar cada uma em três categorias:

#### Categoria A — Embutível
Dependência que pode ser resumida ou incorporada diretamente na versão
client-side sem perda significativa de função.

Exemplos:
- Padrões de nomenclatura de arquivo
- Estruturas de documento (seções obrigatórias, front matter YAML)
- Classificações e taxonomias estáticas
- Diretrizes de qualidade e linguagem

#### Categoria B — Referenciável
Dependência que não precisa estar embutida — a skill client-side sabe o
endereço e busca no ecossistema via API quando tem token de leitura.
Em modo fallback (sem token), opera com versão local desatualizada
possível ou sinaliza limitação.

Exemplos:
- Outras skills canônicas consultadas (não executadas)
- Índices e sumários
- Glossários e terminologia
- Hub-aprendizagem para contexto

#### Categoria C — Bloqueante por modo
Dependência cuja ausência impede a operação **em determinado modo**.
Deve ser declarada explicitamente na skill client-side gerada com
indicação de qual modo é afetado.

Sub-classificação obrigatória:

| Tipo | Definição |
|---|---|
| **C1 — Bloqueia Modo 1** | Sem token algum, a skill não consegue operar esta etapa de forma confiável |
| **C2 — Bloqueia Modo 2** | Requer token de leitura para esta etapa funcionar |
| **C3 — Bloqueia Modo 3** | Requer token de escrita para esta etapa funcionar |

Exemplos:
- Depósito em repositório de destino → C3 (requer token de escrita)
- Leitura de padrão canônico atualizado → C2 (requer token de leitura)
- Verificação de versão no ecossistema → C2 (requer token de leitura)
- Padrão que muda frequentemente e não pode ser embutido com confiança → C1

---

### ETAPA 3 — Diagnóstico de viabilidade

Antes de gerar qualquer skill, apresentar ao usuário o relatório de
dependências no seguinte formato:

```
## Diagnóstico — [Nome da Skill] → Client-Side

**Skill alvo:** [código] — [nome] — v[versão]
**Lida em:** [data]

### Dependências mapeadas

| Dependência | Tipo | Categoria | Modo afetado | Tratamento proposto |
|---|---|---|---|---|
| [nome] | skill/workflow/padrão/repo | A/B/C1/C2/C3 | 1/2/3/todos | embutir/referenciar/declarar bloqueio |

### Resumo de viabilidade por modo

| Modo | Viabilidade | Limitações |
|---|---|---|
| Modo 1 (sem token) | ✅ plena / ⚠️ parcial / 🚫 inviável | [listar bloqueios C1] |
| Modo 2 (token leitura) | ✅ plena / ⚠️ parcial / 🚫 inviável | [listar bloqueios C2] |
| Modo 3 (token escrita) | ✅ plena / ⚠️ parcial / 🚫 inviável | [listar bloqueios C3] |

### Recomendação

[Uma das três:]
✅ VIÁVEL — todos os modos funcionam com as dependências tratadas conforme proposto.
⚠️ VIÁVEL COM RESTRIÇÕES — [modos viáveis] funcionam plenamente;
   [modos restritos] têm as seguintes limitações: [listar].
   Recomendo prosseguir declarando as restrições explicitamente na skill gerada.
🚫 BLOQUEADO — [razão]. Não é possível gerar versão client-side útil sem
   resolver: [listar bloqueantes críticos]. Sugestão: [caminho para desbloquear].
```

**Aguardar aprovação explícita do usuário antes de prosseguir.**

Se o usuário aprovar com restrições: registrar quais restrições foram
aceitas conscientemente — elas serão declaradas na skill gerada.

---

### ETAPA 4 — Geração da skill client-side

Após aprovação do diagnóstico, gerar a skill client-side com a seguinte
estrutura obrigatória:

```markdown
# SKILL — [Nome]
**Código:** [código original]-CS (sufixo CS = client-side)
**Versão local:** v1.0
**Importada de:** [repositório]/[arquivo] — v[versão] — [data de criação original]
**Data de importação:** [data de hoje]
**Ecossistema:** DTD/SETIS/SES-DF — github.com/victorarimatea

> Nota de design: [explicar brevemente o que esta skill faz,
> que é client-side, e onde vive a versão canônica]

---

## Declaração de dependências e modos

| Modo | Requer | Viabilidade | Limitações |
|---|---|---|---|
| Modo 1 — Sem token | nada | [✅/⚠️/🚫] | [ou "nenhuma"] |
| Modo 2 — Token leitura | token leitura | [✅/⚠️/🚫] | [ou "nenhuma"] |
| Modo 3 — Token escrita | token leitura + escrita | [✅/⚠️/🚫] | [ou "nenhuma"] |

[Se houver bloqueios C1/C2/C3, detalhar aqui com linguagem clara para o usuário]

---

## Como acionar esta skill
[frases de ativação]

---

## Verificação de versão (Modos 2 e 3)
[instruções para comparar versão local com ecossistema e alertar o usuário]

---

## Padrão embutido (fallback — [skill original] v[versão] importada em [data])
[conteúdo da skill adaptado para uso client-side, com dependências
 tratadas conforme classificação da Etapa 2]

---

## Modos de operação

### MODO 1 — Sem token
[o que funciona, o que não funciona, como alertar o usuário]

### MODO 2 — Com token de leitura
[fluxo completo com verificação de versão]

### MODO 3 — Com token de escrita
[fluxo completo com depósito no ecossistema]

---

## Endereços do ecossistema
[tabela com repositórios, caminhos e URLs relevantes para esta skill]

---

## Princípios desta skill
[princípios específicos desta skill client-side]
```

---

### ETAPA 5 — Entrega e depósito

**5a — Entrega no chat:**
Apresentar a skill client-side gerada completa para revisão e aprovação
do usuário antes de qualquer depósito.

**5b — Nomenclatura do arquivo:**
```
SKILL-[nome-da-skill-original]-clientside.md
```
Exemplo: `SKILL-registro-reuniao-clientside.md`

**5c — Depósito (Modo 3 — requer token de escrita):**
Quando o repositório dedicado de skills client-side estiver criado:
- Depositar em `hub-client-side/SKILL-[nome]-clientside.md`
- Atualizar `hub-client-side/INDICE.md`

Enquanto o repositório não existir:
- Entregar no chat para instalação manual pelo usuário
- Registrar no staging como pendência de depósito formal

**5d — Registro na staging (se skill nova):**
Se a conversão for de uma skill que ainda não tem versão client-side,
registrar na Seção A do staging como entregável novo:
```
| [data] | SKILL-[nome]-clientside.md | skill client-side |
  hub-client-side | criador-skill-clientside |
```

---

## Checklist de qualidade — skill gerada

Antes de entregar, verificar:

| Critério | Status |
|---|---|
| Cabeçalho completo (versão, data importação, origem) | ✅ / ⚠️ |
| Tabela de dependências e modos declarada | ✅ / ⚠️ |
| Bloqueios C1/C2/C3 explicitados com clareza para o usuário | ✅ / ⚠️ |
| Verificação de versão implementada (Modos 2 e 3) | ✅ / ⚠️ |
| Padrão embutido completo e fiel à versão lida | ✅ / ⚠️ |
| Três modos de operação definidos | ✅ / ⚠️ |
| Endereços do ecossistema corretos | ✅ / ⚠️ |
| Nomenclatura do arquivo correta | ✅ / ⚠️ |
| Frases de ativação definidas | ✅ / ⚠️ |

---

## Endereços desta meta-skill no ecossistema

| Recurso | Repositório | Caminho |
|---|---|---|
| Sumário canônico | `hub-fonte` | `sumario.md` |
| Orquestração GitHub | `skl-github-orquestracao` | `SKILL.md` |
| Repositório de skills client-side | `hub-client-side` | `INDICE.md` |
| Staging area | `hub-entrada` | `staging.md` |

Base URL: `https://api.github.com/repos/victorarimatea/[repo]/contents/[path]`

---

## Intenção do Comandante

O estado final desejado desta skill é que qualquer workflow ou skill
canônica do ecossistema possa ser convertida em uma versão client-side
confiável, transparente sobre suas limitações, e automaticamente
consciente de quando ficou desatualizada — para que o usuário opere
sempre com o melhor padrão disponível, independentemente do contexto
de acesso no momento.

Uma skill client-side bem gerada nunca mente sobre o que pode fazer.
Ela declara seus limites, opera dentro deles, e guia o usuário para
o modo mais capaz disponível.
