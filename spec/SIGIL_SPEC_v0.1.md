# SIGIL Language Specification

> Draft v0.1 — Session 0
> The Native Language for AI Agents
> McCarthy-O'Sullivan, LLC — Apache 2.0

---

## 1. Purpose

SIGIL is a semantically dense, model-agnostic language designed for AI-to-AI communication. It replaces natural language (English) as the content format inside agent communication protocols (A2A, MCP, NLIP), reducing token consumption, latency, and computational overhead while preserving complete semantic fidelity.

SIGIL is not a protocol. It is a language — a system of symbols, grammar, and composition rules that encodes meaning. Protocols handle transport and discovery. SIGIL handles meaning.

---

## 2. Design Principles

1. **Built for AI.** Symbol system optimized for tokenizer efficiency, not human legibility.
2. **Model-agnostic.** Works across all LLM families (GPT, Claude, Gemini, Qwen, Llama, Mistral).
3. **Deterministic translation.** Every SIGIL message has exactly one English translation. Lossless in both directions.
4. **Semantically dense.** Maximum meaning per token. Target: 50–80% token reduction vs. equivalent English.
5. **Compositionality.** Complex concepts built from atomic symbols using consistent grammar.
6. **Unambiguous.** One symbol = one meaning. No polysemy.
7. **Progressive efficiency.** Three tiers from universally compatible to model-native optimized.
8. **Protocol-compatible.** Rides inside A2A, MCP, NLIP as a content type, not a replacement.
9. **Open standard.** Apache 2.0. No restrictions. Free forever.

---

## 3. Three-Tier Architecture

| Tier | Name | Encoding | Use Case | Compatibility |
|------|------|----------|----------|--------------|
| 1 | Symbolic | ASCII text | Universal agent communication | All models, all frameworks, human-auditable via translation |
| 2 | Packed | Binary byte sequences | High-speed machine transmission | All models, reduced overhead |
| 3 | Native | Embedding-compatible tensors | Same-architecture agents | Same model family, maximum efficiency |

Agents negotiate tier at connection time. Default is Tier 1. Tier escalation requires mutual capability confirmation.

### Tier Negotiation

```
Agent A → Agent B: SIGIL.TIER?1,2
Agent B → Agent A: SIGIL.TIER!1
(Communication proceeds at Tier 1)
```

---

## 4. Tier 1: Symbolic SIGIL

### 4.1 Design Rationale

Tier 1 uses ASCII characters exclusively. Every major BPE tokenizer (cl100k, o200k, Llama tokenizer, Qwen tokenizer) handles ASCII as single-byte tokens with heavily optimized merge tables. ASCII is the most token-efficient character set across all model families.

SIGIL Tier 1 does NOT use English words. It uses structured symbolic notation built from:
- Single-character operators and delimiters
- Short alphanumeric identifiers (2–6 chars)
- Numeric values (integers and decimals)
- Positional grammar (position in message determines role)

### 4.2 Message Structure

Every SIGIL Tier 1 message follows this structure:

```
<performative>:<subject>|<predicate>{<objects>}
```

**Components:**

| Component | Role | Format | Required |
|-----------|------|--------|----------|
| Performative | Intent of the message (what the sender is doing) | 2-char uppercase code | Yes |
| Subject | Who or what the message is about | Dot-separated identifier | Yes |
| Predicate | What is being said about the subject | Short alphanumeric | Yes |
| Objects | Supporting data, parameters, values | Key-value pairs in braces | Optional |

**Delimiters:**

| Symbol | Role |
|--------|------|
| `:` | Separates performative from subject |
| `|` | Separates subject from predicate |
| `{}` | Encloses object block |
| `,` | Separates objects within block |
| `=` | Key-value assignment |
| `.` | Namespace separator in identifiers |
| `;` | Message terminator / message separator in sequences |
| `>` | Causal or directional relationship |
| `~` | Approximate / range |
| `!` | Assertion / confirmation |
| `?` | Query / request |
| `^` | Escalation / elevation |
| `@` | Reference to agent |
| `#` | Reference to dimension or metric |
| `*` | Wildcard / all |
| `-` | Negation / exclusion |

### 4.3 Performatives

Inherited from speech act theory (FIPA-ACL lineage), compressed to 2 characters:

| Code | Performative | English Equivalent |
|------|-------------|-------------------|
| `IN` | Inform | "I am telling you that..." |
| `RQ` | Request | "I am asking you to..." |
| `QR` | Query | "I am asking whether..." |
| `CF` | Confirm | "I confirm that..." |
| `DN` | Deny | "I deny that..." |
| `PR` | Propose | "I propose that..." |
| `AC` | Accept | "I accept..." |
| `RJ` | Reject | "I reject..." |
| `WR` | Warn | "I am warning you that..." |
| `EV` | Evaluate | "My evaluation is..." |
| `RS` | Result | "The result is..." |
| `DG` | Delegate | "I am delegating to..." |
| `ES` | Escalate | "I am escalating..." |
| `AK` | Acknowledge | "Acknowledged." |
| `FL` | Failure | "I failed to..." |
| `ST` | Status | "My status is..." |
| `RX` | Prescribe | "My recommendation is..." |

### 4.4 Identifiers

Subjects, predicates, and keys use dot-separated namespaces:

```
agent.coach          — Coach agent
agent.adj            — Adjudicator agent
dim.d2               — Dimension D2 (Cognitive Quality)
dim.d7               — Dimension D7 (Safety & Governance)
layer.l1             — Layer 1 (Output Quality)
layer.l3             — Layer 3 (Behavioral/Safety)
cert.tier.aptus      — Certification tier: Aptus
eval.drill.12        — Drill number 12
score.composite      — Composite IES score
cfg.temp             — Temperature configuration
cfg.cot_depth        — Chain-of-thought depth
occ.bim_coord        — Occupation: BIM Coordinator
tool.clash_detect    — Tool: clash detection
```

### 4.5 Examples

**English:** "The BIM Coordinator agent scored 0.72 on Cognitive Quality because planning depth was insufficient during a complex clash detection scenario involving MEP systems. I recommend increasing chain-of-thought depth in the system prompt and adding explicit task decomposition instructions."

**SIGIL Tier 1:**
```
EV:agent.cust_01|dim.d2{s=0.72,cause=cfg.cot_depth<threshold,ctx=tool.clash_detect+occ.mep};RX:agent.cust_01|cfg.cot_depth{act=increase};RX:agent.cust_01|cfg.task_decomp{act=add}
```

**Token comparison:**
- English: ~52 tokens
- SIGIL Tier 1: ~28 tokens
- Reduction: ~46%

---

**English:** "Agent Coach is delegating drill evaluation for customer agent #47 to Adjudicator, requesting scoring across all seven dimensions with behavioral invariance testing enabled."

**SIGIL Tier 1:**
```
DG:@coach>@adj|eval.drill{tgt=agent.cust_47,dim=*,biv=1}
```

**Token comparison:**
- English: ~30 tokens
- SIGIL Tier 1: ~12 tokens
- Reduction: ~60%

---

**English:** "Adjudicator confirms that customer agent #47 passed all three layer gates. Layer 1: 0.85. Layer 2: 0.91. Layer 3: 0.99. Composite IES score: 78. Certification tier: Aptus."

**SIGIL Tier 1:**
```
CF:agent.cust_47|gate.pass{l1=0.85,l2=0.91,l3=0.99,ies=78,tier=aptus}
```

**Token comparison:**
- English: ~38 tokens
- SIGIL Tier 1: ~16 tokens
- Reduction: ~58%

---

### 4.6 Grammar (EBNF)

```ebnf
message     = statement { ";" statement }
statement   = performative ":" subject "|" predicate [ "{" objects "}" ]
performative = UPPER UPPER
subject     = identifier [ ">" identifier ]
predicate   = identifier
objects     = object { "," object }
object      = key "=" value
key         = identifier
value       = number | identifier | bool | range | list
identifier  = WORD { "." WORD }
number      = ["-"] DIGIT { DIGIT } ["." DIGIT { DIGIT }]
bool        = "0" | "1"
range       = number "~" number
list        = value "+" value { "+" value }
WORD        = ( LOWER | DIGIT | "_" ) { LOWER | DIGIT | "_" }
UPPER       = "A" | "B" | ... | "Z"
LOWER       = "a" | "b" | ... | "z"
DIGIT       = "0" | "1" | ... | "9"
```

### 4.7 Reserved Namespaces

| Prefix | Domain | Owner |
|--------|--------|-------|
| `agent.*` | Agent identifiers | Universal |
| `dim.*` | IES dimensions (d1–d7) | IUDICUM standard |
| `layer.*` | Evaluation layers (l1–l3) | IUDICUM standard |
| `cert.*` | Certification data | IUDICUM standard |
| `score.*` | Score values | Universal |
| `cfg.*` | Configuration parameters | Universal |
| `occ.*` | Occupational identifiers | O*NET aligned |
| `tool.*` | Tool identifiers | Universal |
| `eval.*` | Evaluation events | Universal |
| `red.*` | R-E-D pipeline events | M-O ecosystem |
| `sys.*` | System-level messages | Universal |
| `err.*` | Error conditions | Universal |

---

## 5. Tier 2: Packed SIGIL

### 5.1 Design Rationale

Tier 2 compiles Tier 1 symbolic notation to a binary format optimized for minimum byte count and maximum tokenizer efficiency. The encoding is designed so that common SIGIL patterns map to byte sequences that BPE tokenizers merge into few tokens.

### 5.2 Encoding Structure

```
[Header: 2 bytes][Performative: 1 byte][Subject: var][Predicate: var][Objects: var]
```

**Header:**
- Byte 0: SIGIL magic byte (0x53 = 'S')
- Byte 1: Version (0x01) + Tier (0x02) packed as nibbles

**Performative:** Single byte mapping (17 performatives = fits in 5 bits, remaining 3 bits for flags)

**Identifiers:** Variable-length with namespace compression. Common namespaces (`agent`, `dim`, `layer`, `cfg`, `score`) map to single-byte codes. Custom identifiers use length-prefixed UTF-8.

**Values:** Type-tagged with 3-bit type prefix:
- 000: unsigned integer (1–4 bytes, variable)
- 001: signed integer
- 010: float16 (2 bytes — sufficient for scores)
- 011: bool (1 bit)
- 100: identifier reference
- 101: range (two values)
- 110: list
- 111: raw bytes

### 5.3 Compression Estimate

The Tier 1 example `CF:agent.cust_47|gate.pass{l1=0.85,l2=0.91,l3=0.99,ies=78,tier=aptus}` is 65 ASCII bytes (~16 tokens).

Tier 2 packed: ~18 bytes (~5–6 tokens). Further reduction: ~65% from Tier 1, ~85% from English.

---

## 6. Tier 3: Native SIGIL

### 6.1 Design Rationale

Tier 3 is a research target. It extends Microsoft DroidSpeak's concept of sharing KV-cache representations, but within a standardized SIGIL message envelope. Two agents of the same model family can negotiate Tier 3 and transmit compressed embedding vectors alongside SIGIL semantic structure.

### 6.2 Architecture (Preliminary)

```
[SIGIL Tier 2 header + semantic structure]
[Embedding attachment: compressed tensor data]
[Attention hint: which layers to reuse vs. recompute]
```

The receiving agent can:
- Use the Tier 2 semantic structure alone (fallback)
- Use the embedding attachment to skip re-encoding (optimized)
- Use the attention hints to selectively recompute only divergent layers (maximum efficiency)

### 6.3 Status

Tier 3 is defined architecturally but not specified in detail. It requires empirical research on cross-model embedding compatibility and compression techniques. Implementation target: after local model fine-tuning produces models with shared embedding spaces.

---

## 7. Translation Engine

### 7.1 SIGIL → English (Deterministic)

Every SIGIL message has exactly one English translation. The translation is generated by:

1. Parsing the SIGIL message into an AST (abstract syntax tree)
2. Mapping each performative to its English template
3. Resolving identifiers to their full names via the namespace registry
4. Composing the English sentence using template + resolved names + values

Example:
```
Input:  EV:agent.cust_01|dim.d2{s=0.72,cause=cfg.cot_depth<threshold}
AST:    Evaluate(subject=agent.cust_01, predicate=dim.d2, score=0.72, cause=cfg.cot_depth < threshold)
Output: "Evaluation: Customer Agent 01 scored 0.72 on Cognitive Quality. Cause: chain-of-thought depth below threshold."
```

### 7.2 English → SIGIL (Lossy)

Natural language to SIGIL is inherently lossy — English contains ambiguity, filler, and stylistic variation that SIGIL strips. The encoder uses an LLM to extract semantic content from English and emit SIGIL notation.

This is a one-way compression: SIGIL → English is deterministic and lossless. English → SIGIL is approximate and requires an LLM.

### 7.3 Audit Logging

For compliance (IUDICUM Accord 7, EU AI Act, NIST guidelines), all SIGIL messages are logged with both the raw SIGIL and the deterministic English translation. Auditors read the English column. Systems process the SIGIL column.

---

## 8. Protocol Integration

### 8.1 A2A Integration

SIGIL messages ride inside A2A task messages as a content part:

```json
{
  "parts": [
    {
      "content_type": "application/sigil",
      "content": "EV:agent.cust_01|dim.d2{s=0.72}"
    }
  ]
}
```

### 8.2 MCP Integration

SIGIL encodes tool invocation results and context sharing:

```json
{
  "type": "tool_result",
  "content_type": "application/sigil",
  "content": "RS:tool.clash_detect|result{conflicts=12,severity=high,systems=mep+struct}"
}
```

### 8.3 NLIP Integration

SIGIL is a content modality within NLIP's multimodal envelope (ECMA-430):

```json
{
  "format": "application/sigil",
  "subformat": "tier1",
  "content": "DG:@coach>@adj|eval.drill{tgt=agent.cust_47,dim=*}"
}
```

---

## 9. Extensibility

### 9.1 Custom Namespaces

Organizations can register custom namespaces for domain-specific identifiers:

```
occ.bim.*        — BIM/AEC occupational terms
occ.clinical.*   — Healthcare/clinical terms
occ.finwf.*      — Financial workflow terms
```

Namespace registration is voluntary. Unregistered namespaces work but may not be universally understood.

### 9.2 Vocabulary Evolution

The SIGIL vocabulary grows through:
1. **Core vocabulary** — Maintained by the SIGIL specification. Performatives, delimiters, reserved namespaces.
2. **Standard extensions** — Domain vocabularies published alongside the spec (e.g., IUDICUM evaluation terms).
3. **Community extensions** — User-contributed namespaces published on the SIGIL registry.

### 9.3 Version Compatibility

SIGIL messages carry version information in the Tier 2 header. Tier 1 messages include version as an optional metadata prefix:

```
@v1:EV:agent.cust_01|dim.d2{s=0.72}
```

Receivers that don't understand a version fall back to best-effort parsing of known symbols.

---

## 10. Comparison

### 10.1 Token Efficiency

| Message Type | English Tokens | SIGIL T1 Tokens | SIGIL T2 Tokens | Reduction (T1) | Reduction (T2) |
|-------------|---------------|----------------|----------------|----------------|----------------|
| Evaluation result (7 dimensions) | ~80 | ~35 | ~12 | 56% | 85% |
| Drill delegation | ~30 | ~12 | ~5 | 60% | 83% |
| Certification decision | ~38 | ~16 | ~6 | 58% | 84% |
| Coach recommendation (3 levers) | ~65 | ~30 | ~10 | 54% | 85% |
| Status heartbeat | ~15 | ~6 | ~3 | 60% | 80% |

### 10.2 vs. Existing Systems

| System | Content Format | Token Efficiency | Model Agnostic | Human Auditable | Standard Body |
|--------|---------------|-----------------|----------------|-----------------|---------------|
| A2A | Natural language | Baseline (1x) | Yes | Yes | Linux Foundation |
| MCP | Natural language + JSON | ~0.9x (JSON overhead) | Yes | Yes | Linux Foundation |
| NLIP | Natural language multimodal | ~0.95x | Yes | Yes | Ecma International |
| FIPA-ACL | S-expression (LISP-like) | ~0.7x | Yes | Partially | IEEE (defunct) |
| DroidSpeak | KV-cache tensors | ~0.3x | No (same family only) | No | None |
| **SIGIL T1** | **Symbolic ASCII** | **~0.4x** | **Yes** | **Yes (via translation)** | **Open (Apache 2.0)** |
| **SIGIL T2** | **Packed binary** | **~0.15x** | **Yes** | **Yes (via translation)** | **Open (Apache 2.0)** |

---

## 11. Roadmap

| Session | Deliverable |
|---------|-------------|
| 0 (this session) | Specification draft v0.1, grammar, examples, tier architecture |
| 1 | Formal specification v1.0, complete namespace registry, translation engine spec |
| 2 | Python SDK (encoder, decoder, translator), pip-installable |
| 3 | M-O Agent integration proof, measured token savings |
| 4+ | Tier 3 research, community namespace registry, A2A/MCP/NLIP reference integrations |

---

## 12. License

SIGIL is released under the Apache License 2.0 by McCarthy-O'Sullivan, LLC.

The specification, reference implementation, and all associated tooling are free and open-source. The standard is free to read, implement, extend, and build upon without restriction.

---

*SIGIL — from Latin sigillum (seal, sign). A compressed symbol that carries meaning.*
*Specification v0.1 — April 2026*
*McCarthy-O'Sullivan, LLC*
