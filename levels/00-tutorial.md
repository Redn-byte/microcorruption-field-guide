# 00 — Tutorial

the game's own hand-holding level. the lock asks for an 8-character password and literally nothing is hidden from you about how it checks it.

## what's new

everything — this is your first look at the disassembly view, the memory view, and the debug console, and the game walks you through using all three. the "vulnerability," such as it is, is just that the check is a length check plus a loop comparing bytes you can watch happen live.

## hint ladder

1. find the function that looks like it's comparing your input byte by byte. set a breakpoint at the top of it.
2. step through one iteration of the loop and watch which register holds the current byte of *your* input, and what it's being compared against.
3. the loop only cares about length, not content, until a specific point — read the comparison right before the jump that decides pass/fail, and figure out what length actually satisfies it.

## why it matters

nothing exploit-y yet. the point of this level is purely: can you set a breakpoint, step one instruction at a time, and read what a register contains at a given moment. if you can do that comfortably, you're ready for level 1. if it felt clumsy, do it again before moving on — every later level assumes this is automatic.
