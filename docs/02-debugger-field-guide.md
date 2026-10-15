# driving the debugger

the debug console is a real, if small, debugger — closer in spirit to GDB attached to a remote target than to a toy. the mental model is simple even where exact syntax might drift a little from what's written here (more on that below): you set breakpoints, you run until you hit one, you step instruction by instruction, and you can read or write memory and registers directly.

## the core verbs

- **breakpoint** — stop execution when the program counter reaches a given address. you'll do this constantly: set a breakpoint at the start of whatever function looks interesting (`check_password`, `login`, `test_password_valid`, whatever the disassembly calls it), then run, so you land right at the start of the logic you actually care about instead of single-stepping through setup code to get there.
- **delete a breakpoint** — remove one you've set, usually once you're past the part you needed to slow down for.
- **step** — execute exactly one instruction and stop again. this is how you watch a loop iterate, watch a comparison happen right before the jump that depends on it, watch a buffer fill byte by byte.
- **continue** — run freely until the next breakpoint (or until the program needs more input from you).
- **reset** — restart the simulated CPU from the beginning, keeping your breakpoints. you'll use this a lot: form a theory, reset, set up input, run it, observe, adjust, reset again.
- **set a register or memory value directly** — the debugger lets you reach in and change a register or a memory location by hand, which is enormously useful for testing a theory fast without re-sending input through the I/O console. for example: convincing yourself that a given stack address really is what gets jumped to, by just writing a known value there and watching what happens, rather than constructing the overflow first.

that's the whole mental model: **breakpoint → run → step/continue → observe → reset → adjust.** everything else is detail.

## on exact command syntax

I'm deliberately not giving you an exact command cheat-sheet of literal strings to type (`break 0x4484` vs `b 0x4484` and so on) — the interface has had a decade to drift in small ways, and I'd rather you get the authoritative, current syntax from the tool itself than from my memory of it. **type `help` into the debug console** the first time you're in a level; it'll print the real, current command list. the verbs above are what each command *does* — matching them to whatever the console actually calls them takes about ten seconds.

## reading the memory dump

the live memory view is a hex dump: an address, then sixteen bytes, then (usually) an ASCII rendering of those same bytes off to the side. two things to keep straight while reading it:

- it's **little-endian**, so a 2-byte value you're hunting for (an address, a length) appears byte-reversed from how you'd write it on paper. if you're looking for `0x4452`, you're scanning for the byte pair `52 44`.
- it updates live as the program runs. the single most useful debugging habit in this whole game is: set a breakpoint right before a copy or a comparison you don't trust, step through it one instruction at a time, and just *watch* the dump change. most bugs in these levels are completely obvious once you watch them happen in real time instead of trying to reason about them purely from static disassembly.

## a worked loop, no spoilers

say you're looking at a function that copies your input into a fixed-size buffer and you suspect it doesn't check the length. you'd:

1. breakpoint at the start of the copy function.
2. run, and send an input that's deliberately way too long — something like forty `A`s in a row is the traditional move, because `A` is `0x41`, which is easy to recognize anywhere it shows up in memory or in a register later.
3. step through the copy and watch the memory dump fill with `41 41 41 41...` past where the buffer should have ended.
4. keep stepping (or continue) to where the function returns, and watch whether the return address on the stack, or the program counter after the `ret`, now contains bytes from *your* input.
5. if it does — and in a lot of early levels, it will — you've confirmed the bug is real, and the rest of the level is just figuring out *what* to put there instead of junk.

that loop — confirm the bug is real with a lazy payload before building a precise one — will save you more time than any other single habit in this game.
