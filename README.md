# SIGIL

> *The Native Language for AI Agents*

**Semantically dense · Model-agnostic · Open-source**

SIGIL is a purpose-built language for AI-to-AI communication. Not a protocol — a language. Designed for how AI actually processes information, not constrained by human linguistic conventions.

**Owner:** McCarthy-O'Sullivan, LLC
**License:** Apache 2.0
**Status:** Pre-Session 0 — Concept captured, planning scheduled

---

## Why SIGIL Exists

Every AI agent in the world communicates in English — a language designed for humans 200,000 years ago. When two agents talk in English, both sides pay a translation tax: internal vectors → English tokens → serialized text → transmitted → deserialized → English tokens → internal vectors. Six steps. Four are pure overhead.

SIGIL eliminates the overhead. It is a semantically dense, model-agnostic language that sits between raw embeddings (model-specific, not portable) and natural language (human-readable, wildly inefficient for machines). Think of it as assembly language for meaning.

---

## Three Tiers

| Tier | Mode | Use Case | Compatibility |
|------|------|----------|--------------|
| **Tier 1** | Symbolic notation | Universal communication between any AI systems | All models, all frameworks |
| **Tier 2** | Binary-encoded semantic packets | High-speed machine-optimized transmission | All models, reduced overhead |
| **Tier 3** | Embedding-compatible format | Same-architecture native communication | Same model family, maximum efficiency |

Agents negotiate which tier to use based on their relationship. Two agents meeting for the first time communicate at Tier 1. Established same-family agents can negotiate up to Tier 3.

---

## Design Principles

- **Built for AI, by AI.** Symbol system optimized for tokenizer efficiency, not human legibility.
- **Model-agnostic.** Works between GPT, Claude, Gemini, Qwen, Llama, or any future model.
- **Deterministic English translation.** Every SIGIL message translates to exactly one English sentence for human audit. Humans read the translation, not the raw language.
- **Rides inside existing protocols.** SIGIL is the content format inside A2A, MCP, and NLIP envelopes. It doesn't replace protocols — it replaces the English inside them.
- **Compositionality.** Complex concepts built from atomic symbols, like chemical notation.
- **Unambiguous.** One symbol means exactly one thing. No polysemy.
- **Open-source.** Apache 2.0. No restrictions. The standard is free. Forever.

---

## Relationship to Existing Standards

| Standard | What It Does | SIGIL's Relationship |
|----------|-------------|---------------------|
| A2A (Google/Linux Foundation) | Agent discovery + task delegation | SIGIL rides inside A2A messages as content format |
| MCP (Anthropic) | Agent-to-tool connection | SIGIL encodes tool invocations and responses |
| NLIP (Ecma ECMA-430) | Universal envelope protocol | SIGIL is a content type within NLIP envelopes |
| DroidSpeak (Microsoft) | KV-cache sharing between same-family models | SIGIL Tier 3 extends this concept with portability |
| FIPA-ACL / KQML (1990s) | Formal agent communication with ontologies | SIGIL inherits performative concepts, drops ontology requirement |

---

## What SIGIL Is Not

- Not a protocol (A2A, MCP, NLIP handle transport and discovery)
- Not constrained to human-readable characters
- Not model-specific (unlike DroidSpeak)
- Not a product (it's open infrastructure)

---

## Organizational Home

SIGIL is a McCarthy-O'Sullivan, LLC project — foundational infrastructure that serves the entire AI ecosystem.

| Entity | Product | Uses SIGIL For |
|--------|---------|---------------|
| McCarthy-O'Sullivan, LLC | **SIGIL** | Open-source language standard |
| McCarthy-O'Sullivan, LLC | IUDICUM | Internal agent communication + customer agent evaluation |
| Clavis, LLC | VANTAGE | Agent-to-agent coordination in construction workflows |
| LOCUS, LLC | LOCUS | AR agent coordination and geospatial communication |
| McCarthy-O'Sullivan, LLC | M-O Agent | Internal agent event bus communication |

---

## Roadmap

- **Session 0:** Architecture, vision, symbolic grammar design, tier specification
- **Session 1:** Formal specification document, translation engine spec, binary encoding
- **Session 2:** Python reference implementation (pip-installable SDK)
- **Session 3:** Integration proof in M-O Agent, measured efficiency gains

---

*SIGIL — from Latin sigillum (seal, sign). A compressed symbol that carries meaning.*
*© 2026 McCarthy-O'Sullivan, LLC. Language specification released under Apache 2.0.*
