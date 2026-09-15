# Compare fact slots, not sentences, to resolve memory conflicts

**Broke:** a user told the agent "I live in Kazan", later "I moved to Moscow". Long-term memory kept
both as current. Three test users, three failures on a local model; unit tests were green.

**Why:** the conflict check matched facts by shared words. "I live in Kazan" and "I moved to Moscow"
share none. The model also phrases the same fact differently each time, so any wording match is
fragile.

**Fix:** extract a slot for each fact — who it is about and which attribute it sets — and compare
slots by equality. A new value in a single-valued slot closes the old fact instead of deleting it.

```python
SINGLE_VALUED = {"lives_in", "works_at", "spouse"}

def apply(memory: list[dict], new: dict) -> None:
    # new = {"subject": "user", "attribute": "lives_in", "value": "Moscow"}
    if new["attribute"] in SINGLE_VALUED:
        for fact in memory:
            if (fact["subject"], fact["attribute"]) == (new["subject"], new["attribute"]) \
                    and fact.get("valid_to") is None and fact["value"] != new["value"]:
                fact["valid_to"] = new["observed_at"]   # close, keep history
    memory.append({**new, "valid_to": None})
```

The model's job shrinks to filling `subject` / `attribute` / `value` from a closed list of
attributes. The decision "does this replace that" becomes plain code you can test.

**When not to use:** multi-valued attributes. "Likes coffee" and "likes beer" are both true — never
let them close each other. Keep those attributes out of `SINGLE_VALUED`.

**Alternative I didn't pick:** an LLM judge that decides add / update / delete for each new fact. It
still depends on wording, and costs an extra model call per fact.
