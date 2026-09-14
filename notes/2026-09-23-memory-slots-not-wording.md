# Agent memory conflicts: compare slots, not wording

*2026-09-23*

I build AI voice agents for small businesses. Lately I've been working on long-term memory, so an agent remembers what people told it in earlier conversations.

This week a live run on a local model broke something my tests said was fine. A test user said "I live in Kazan" and later "I moved to Moscow". The old fact never got closed. I tried three test users and got three failures. Both cities stayed in memory as current.

## What I noticed

My conflict check compared fact sentences by shared words. Those two sentences share none. The model also words the same fact differently every time, so any match on wording breaks easily.

## The rule I took from it

Compare slots, not sentences. For each fact, pull out who it is about and which single-valued attribute it sets, like where they live or where they work. If a new value lands in the same slot, it replaces the old one by a simple equality check. Close the old fact instead of deleting it, so you keep the history. And never let facts with many values replace each other. "Likes coffee" and "likes beer" can both be true.

A popular option is an LLM judge that decides add, update or delete for each fact. But it still depends on wording, and it costs an extra model call for every fact.

How do you handle facts that contradict each other in your agent's memory?
