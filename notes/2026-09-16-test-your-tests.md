# How do you test your tests?

*2026-09-16*

I ran my own automated check twice on the same untranslated English sentence.

With a short note next to it, the check blocked it. Correct.
With a slightly longer note that said exactly the same thing, the check passed it.

Nothing about the meaning changed. Only the character count did.

That check was a guardrail: a rule that is supposed to stop a mistake before it ships.
It did not read meaning. It counted letters — a proxy for the property I actually cared about.

Every automated check measures a proxy. A green result proves the proxy is satisfied,
not that the real thing is true.

## What I do now, for every new check

1. Before trusting the first clean run, feed it a known-bad example.
2. If the bad example passes, the check is decoration, not protection.
3. Only after it fails on bad input and passes on good input do I count it.

I build AI voice agents for small businesses, and most of my week is not writing the agent.
It is proving that the things watching the agent actually watch.

How do you test your tests?
