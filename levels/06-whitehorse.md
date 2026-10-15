# 06 — Whitehorse

*rev c.01. first appearance of HSM-2. Lockitall closed the Hanoi gap by having the HSM unlock the door directly instead of just raising a flag — which is a genuine improvement. it also means they deleted a function you were counting on.*

## what's new

this is the same stack overflow as Cusco, structurally — but go looking for `unlock_door` the way you did last time and you won't find it. HSM-2 (`INT 0x7E`) unlocks directly, so there's no separate "open the door" function anywhere in this binary for you to jump to.

## hint ladder

1. confirm the overflow still works exactly like Cusco — same mechanism, same overwritten return address.
2. since there's nothing pre-built to jump to, the return address needs to point somewhere *you* control the contents of. you're already overflowing a buffer with your own bytes — what if some of those bytes weren't junk, but were instructions?
3. recall the `push #N; call INT` idiom from `03-the-manual-explained.md`. you need five bytes of actual MSP430 machine code sitting in your overflow, and a return address that points at the start of those five bytes instead of past them. you have both the hex-encoded-input checkbox and the assembly reference in `01-msp430-primer.md` — you have everything required to hand-assemble this.

## why it matters

this is your first time writing and injecting your own shellcode rather than redirecting to existing code — the difference between a `ret2win` and an actual code-injection exploit. it's also the smallest possible shellcode you'll ever need to write (one push, one call), which makes it a good first one to hand-assemble yourself instead of reaching for a tool.
