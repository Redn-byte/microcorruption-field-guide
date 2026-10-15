# 17 — Chernobyl

*the Account Manager is back, and it's angrier. heap exploitation, round two, except the allocator is meaner and the input protocol has opinions.*

## what's new

input here arrives as semicolon-separated commands, each naming a username and a PIN, each one triggering its own allocation. this is Algiers' heap-corruption idea again, but with a real little command grammar sitting in front of it and a heap that's going to need several rounds of grooming rather than one clean hit.

## hint ladder

1. map the command format first, completely separately from the heap question: what prefix a command needs, how arguments are separated inside it, and what gets ignored. get this part fully mechanical before you touch exploitation — guessing at both the parser and the heap bug simultaneously is how this level eats an evening for no reason.
2. once you can reliably get chunks allocated in a pattern you choose, reapply everything from Algiers: chunk layout, the previous/next/size-and-status metadata, and corrupting a neighbor's header so that freeing it does your writing for you.
3. expect to need several allocations in a row rather than one — most writeups land somewhere around a dozen commands for the straightforward path. that's normal for this level, not a sign you're missing something clever. there is a shorter, more optimized solution too, but it's worth getting the straightforward version working first.

## why it matters

mostly reinforcement of Algiers' lesson at higher difficulty, plus a genuinely transferable skill of its own: reverse-engineering an input *protocol* (not just a single buffer) before you go looking for the bug inside it. a lot of real vulnerability research starts exactly here — understanding the grammar a program expects, fully, before you start trying to break what it does with that input.
