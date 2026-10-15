# 15 — Bangalore

*DEP is on. remember INT 0x10 and 0x11 from the manual? this is the level that makes you actually care about them.*

## what's new

memory protection is active: every page is executable or writable, never both. your old move of "overflow the stack with shellcode, point the return address at your own bytes" now gets you a very fast, very unglamorous crash the instant execution tries to run from a page that's marked writable-not-executable.

## hint ladder

1. confirm DEP is really the wall you're hitting — find the call to `INT 0x10` (or evidence protection's already on by the time you get control) and watch your old-style shellcode injection fail specifically at the "jump to my own bytes" step rather than earlier.
2. go back to `03-the-manual-explained.md`'s description of `INT 0x11`: it flips a *specific page* to executable, given a page number as an argument. that interrupt exists somewhere in this program's normal operation, or can be reached through the control you already have over the stack. the question to chase: can you get `0x11` called with *your* page number, before you ever try to execute anything?
3. you likely still have a return-address overwrite available to you, same as Cusco — it just can't point directly at your own fresh shellcode anymore. use it instead to redirect through a `push #page; push #1 (or 0); call INT` sequence (built the same null-byte-aware way you practiced in Montevideo) that marks *your* buffer's page executable, and only then land on it.

## why it matters

this is the actual real-world DEP bypass pattern, shrunk down: you don't fight the mitigation head-on, you find the one legitimate way the system itself is allowed to flip a page's permissions, and you drive it with attacker-controlled arguments. on real operating systems that primitive is usually `VirtualProtect` or `mprotect`, reached via a short return-oriented-programming chain instead of a single interrupt call — which is exactly the shape of the next level.
