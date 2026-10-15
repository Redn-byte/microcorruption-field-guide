# 16 — Lagos

*everything you've fought separately so far, stacked on top of each other at once. this is where the game stops being gentle.*

## what's new

DEP and ASLR, both active, together. you can't hardcode an address (ASLR), and you can't just jump to freshly written bytes even once you find one (DEP). most accounts of this level agree it also squeezes what kinds of bytes or instructions you can get away with injecting directly, which pushes you toward reusing code that's already sitting in the binary rather than writing much of anything brand new.

## hint ladder

1. don't try to solve this in one shot. the throughline across pretty much every writeup of this level is a **two-stage** approach: a short first-stage chain, built almost entirely out of instructions that already exist in the program (classic return-oriented-programming — borrowing small fragments of existing code, each one ending in a `ret`, and chaining them with a sequence of return addresses on the stack instead of writing new instructions), whose entire job is to peel back one or both protections or otherwise win yourself room to work.
2. once stage one has bought you that room — a page you can now write *and* execute, or a known address instead of a random one — stage two can be almost embarrassingly simple: the same `push #0x7f; call INT` idiom you've been using since Whitehorse, just finally landing somewhere it's allowed to run.
3. go back through `levels/06`, `07`, `14`, and `15` before you start — Lagos isn't a new idea, it's all four of those ideas, composed. the hard part is sequencing, not invention.

## why it matters

real-world exploit chains against hardened modern targets look exactly like this: nobody gets a single clever payload past ASLR, DEP, and a canary in one move anymore. they chain primitives — an info leak to defeat ASLR, a ROP chain to defeat DEP, then finally the actual payload — the same way you're about to. if this level takes you a while, that's correct; it's meant to be the first time in this game that it does.
