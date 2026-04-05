# SIGIL Optimization Analysis

> Technical analysis of token efficiency strategies.
> Objective: minimize total compute cost per semantic unit transferred.

---

## 1. Cost Model

Total cost of an inter-agent message:

```
Cost = (tokens_in × cost_per_input_token) + 
       (attention_cost ∝ tokens²) + 
       (transmission_bytes × network_cost) +
       (tokens_out × cost_per_output_token)
```

The dominant term is attention cost, which scales quadratically with token count.
Therefore: **minimizing token count is the highest-leverage optimization.**

---

## 2. What Token Efficiency Actually Means

A token is not a character. A token is a learned subword unit, typically 3-4 characters
of English text. The exact tokenization depends on the tokenizer.

Examples (using cl100k_base / GPT-4):
- "the" → 1 token
- "certification" → 1 token  
- "EV" → 1 token
- ":" → 1 token
- "|" → 1 token
- "agent" → 1 token
- "." → 1 token
- "dim" → 1 token
- "0.72" → 2 tokens (typically "0" + ".72" or "0." + "72")
- "cot_depth" → 2-3 tokens

Key insight: most ASCII sequences of 1-6 common characters are 1-2 tokens.
Diminishing returns come quickly — the difference between a perfectly optimized
and a well-designed ASCII notation is ~10-15%, not 50%.

---

## 3. The Honest Assessment

### What the current Tier 1 spec gets right:
- ASCII-only (maximum tokenizer compatibility)
- Short identifiers (1-2 tokens each)
- Single-character delimiters (1 token each)
- No English filler words (articles, prepositions, copulas eliminated)
- Positional grammar reduces need for explicit labeling

### Where marginal gains exist:

**A. Performative compression**
Current: `EV` (1 token) + `:` (1 token) = 2 tokens for performative
Could be: single byte from a reserved set (1 token for performative + delimiter)
Savings: 1 token per message. At millions of messages: meaningful.

**B. Namespace prefix compression**  
Current: `agent.coach` = ~3 tokens (`agent`, `.`, `coach`)
Could be: `@c` = 1-2 tokens (@ + single char agent alias)
Savings: 1-2 tokens per identifier reference. Multiple per message.

**C. Numeric encoding**
Current: `0.72` = 2 tokens typically
Could be: transmit as integer basis points `72` = 1 token (interpret as 0.72 by convention)
Savings: 1 token per numeric value. Multiple per message.

**D. Eliminating redundant delimiters**
Current: `{l1=0.85,l2=0.91,l3=0.99}` = ~12 tokens
Could be: `{85:91:99}` = ~6 tokens (positional — first value is always l1, etc.)
Savings: ~6 tokens for a typical score block.

### Total marginal optimization: ~15-25% additional reduction beyond current spec.

---

## 4. What Would Be Truly Different (Non-Human Design)

### 4.1 Fixed-Width Semantic Slots

Instead of variable-length key-value pairs, use fixed-position slots where
position determines meaning:

```
Position 0: Performative (1 byte)
Position 1: Subject class (1 byte) 
Position 2-3: Subject ID (2 bytes)
Position 4: Predicate class (1 byte)
Position 5-N: Value slots (fixed order per predicate class)
```

This eliminates all delimiters, all key names, and all structural characters.
The message is pure content. The grammar is entirely positional.

Example — "Agent 47 scored 0.85 on L1, 0.91 on L2, 0.99 on L3, composite 78, tier Aptus":

Human-designed SIGIL:
```
CF:agent.cust_47|gate.pass{l1=0.85,l2=0.91,l3=0.99,ies=78,tier=aptus}
```
~16 tokens.

Positional SIGIL:
```
C#4785919978A
```
~3-4 tokens. Each character encodes a specific semantic slot.

But this is no longer human-auditable even with effort. The translation layer
becomes mandatory, not optional.

### 4.2 Base-64 Value Encoding

Scores from 0.00 to 1.00 have 101 possible values at 2-decimal precision.
These map to single printable ASCII characters:

```
0.00 = !   (ASCII 33)
0.01 = "   (ASCII 34)
...
0.50 = R   (ASCII 83)
...
0.99 = ~   (ASCII 126)
1.00 = DEL (use a convention character)
```

Every score becomes exactly 1 character = 1 token.
Three layer scores = 3 characters = 3 tokens (vs. 12+ in English).

### 4.3 Semantic Hash References

Instead of naming dimensions (`d2`, `cognitive_quality`), assign each concept
a stable hash that tokenizers will consistently handle as a single token.
Use the tokenizer's own vocabulary — find existing tokens that are rarely
used in natural language but are in the vocabulary, and assign them SIGIL meanings.

This is tokenizer-specific and breaks model-agnosticism. Not recommended
for Tier 1, but viable for Tier 2 where both agents agree on tokenizer.

---

## 5. Recommended Approach

### Keep Tier 1 as human-translatable symbolic notation.
Optimize with sections 3A-3D (marginal gains). This tier exists for:
- Universal compatibility (any model, any framework)
- Audit compliance (deterministic English translation)
- Debugging (developers can read it with some learning)
- Bootstrap (first-contact between unknown agents)

### Make Tier 2 the truly AI-optimized format.
Fixed-width positional encoding. Score compression. Zero delimiters.
Maximum density. Translation engine required for any human to understand it.
This is where the "non-human design" belongs.

### Tier 3 remains the embedding-native research target.
Bypasses tokenization entirely for same-architecture agents.

### The tier system IS the answer to "human vs. AI optimized."
- Tier 1: optimized for AI but human-accessible
- Tier 2: optimized for AI, human-accessible only via translation
- Tier 3: optimized for AI, no human representation exists

The progression from Tier 1 → 3 is the progression from
"language humans could learn" to "language only machines speak."

---

## 6. Token Count Comparison (Revised)

| Message | English | SIGIL T1 (current) | SIGIL T1 (optimized) | SIGIL T2 (positional) |
|---------|---------|-------------------|---------------------|----------------------|
| Eval result (7 dims) | ~80 | ~35 | ~28 | ~10 |
| Drill delegation | ~30 | ~12 | ~9 | ~4 |
| Cert decision | ~38 | ~16 | ~12 | ~5 |
| Coach recommendation | ~65 | ~30 | ~22 | ~8 |
| Status heartbeat | ~15 | ~6 | ~5 | ~2 |
| **Average reduction** | **baseline** | **~55%** | **~68%** | **~88%** |

---

## 7. Honest Limitations

1. **Tier 1 cannot achieve 90%+ reduction** without becoming unreadable.
   The remaining 30-40% of tokens carry actual content (scores, IDs, values) 
   that cannot be compressed further in a text format.

2. **Tier 2 achieves 85-90% reduction** but requires both agents to implement
   the binary decoder. Adoption barrier is higher.

3. **Token savings compound.** A multi-agent workflow with 50 inter-agent 
   messages saves 50× the per-message reduction. In a certification engagement
   with hundreds of messages, the cumulative savings are substantial.

4. **The biggest savings aren't in SIGIL's character set — they're in 
   eliminating English.** The jump from English to ANY structured notation
   is 50-60%. Optimizing the notation further adds 10-30%. Changing to
   non-human characters adds another 5-15%. The first step is by far the
   most valuable.

---

*This analysis is the honest technical basis for SIGIL's design decisions.*
*The spec should be updated to incorporate optimizations 3A-3D for Tier 1*
*and the positional encoding approach for Tier 2.*
