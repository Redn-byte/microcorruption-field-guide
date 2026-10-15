# getting started

## account

go to `microcorruption.com/login`, sign up with an email that works, click the activation link it sends you, log back in. that's the whole flow. no payment, no application, no team requirement — it's a single-player game against a fictional company's firmware.

once you're in, start the **Tutorial** level. do the actual guided tutorial even if you're impatient — it walks you through the five panes below with a program small enough that nothing can go wrong, and it's the only time the game holds your hand.

## the five panes

every level opens into the same layout:

- **disassembly** — the program, as instructions, with addresses on the left. this is the ground truth. everything else in the UI is just a different view onto the same memory.
- **live memory** — a hex dump of RAM that updates as the program runs. this is where you'll watch your input land, watch the stack get built and torn down, and eventually watch your overflow happen in real time.
- **registers** — the current value of all 16 registers (`r0`–`r15`). watch `r0` (the program counter) to know where execution is, and `r1` (the stack pointer) to know where the stack currently sits.
- **debug console** — where you type commands: breakpoints, stepping, continuing, peeking and poking memory. covered properly in `02-debugger-field-guide.md`.
- **I/O console** — what the simulated lock prints at you, and where you type your actual input (the password, the username, whatever the level wants).

## the one checkbox that matters

near the input box there's a toggle usually labeled something like **"check here if entering hex encoded input."** this is the single most important UI detail in the entire game and the thing that trips up almost everyone on their first non-trivial level.

- **unchecked**, whatever you type goes in as literal ASCII text. fine for "the password is just a word."
- **checked**, whatever you type is read as a string of hex byte pairs and sent in *exactly* as those raw bytes. this is what you need the moment you're trying to send a crafted address, a null byte, or actual shellcode — none of which you can type on a keyboard as plain text.

if you ever find yourself trying to overflow a buffer with a return address and the game is doing something incomprehensible to your input, check this box first. it is very often the entire bug in your approach.

## the basic loop

1. read the disassembly. figure out what the program *wants* from you.
2. form an opinion about where the gap is — usually "this check can be satisfied without actually knowing the real password" or "this copy has no length limit."
3. set a breakpoint somewhere useful (the start of the suspicious function is a good default) and run.
4. step through, watching memory and registers, to confirm your theory against what's actually happening — not what you assumed was happening.
5. adjust your input, toggle hex encoding if you need raw bytes, and try again.

don't worry about the CPU-cycle count the game shows you at the end of a level. it's a leaderboard thing for people optimizing for style points later. as a beginner your only job is to get the door open.

## if the site is down

see `RESOURCES.md` for an offline MSP430 emulator that can run the level images without the live site. it's not a perfect substitute for the real UI but it'll keep you moving.
