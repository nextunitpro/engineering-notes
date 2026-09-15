# Feed a check known-bad input before trusting it

**Broke:** a guardrail that required a translation next to every English quote passed an
untranslated quote. The only change was a longer note next to it.

**Why:** the check never read meaning. It compared how many letters of the second language sat
near the quote — a proxy that a longer, unrelated note also satisfies.

```python
def has_translation(paragraph: str) -> bool:
    other = sum(ch.isalpha() and not ch.isascii() for ch in paragraph)
    return other >= 10  # a proxy for "a translation is present"

has_translation('"Make work visible" (см. выше)')          # False — blocked, correct
has_translation('"Make work visible" (смотри выше в тексте)')  # True — passed, still untranslated
```

**Fix:** before counting a check as protection, run it on an input you know is wrong. If the bad
input passes, the check is decoration.

```python
KNOWN_BAD = ['"Make work visible" (смотри выше в тексте)']
KNOWN_GOOD = ['"Make work visible" (делай работу видимой)']

assert not any(has_translation(x) for x in KNOWN_BAD), "check passes known-bad input"
assert all(has_translation(x) for x in KNOWN_GOOD)
```

Keep the known-bad cases next to the check, so every later change has to keep failing them.

**When not to bother:** checks with no proxy in them — exact equality, schema validation, a type
check. The trap is specific to checks that count or pattern-match something *instead of* the
property you care about.
