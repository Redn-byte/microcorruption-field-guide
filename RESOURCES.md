# resources

## primary

- **the game:** `microcorruption.com` — free signup, see `docs/00-getting-started.md`.
- **the Lockitall manual (PDF):** linked from inside the game once you're logged in. worth reading in full once, purely for the in-universe deadpan.
- **TI MSP430x1xx Family User's Guide:** the real hardware reference the game's own manual cites. overkill for playing, genuinely interesting if you want to know how much of this chip is real (almost all of it).

## if the live site is down

- **msp430-emu-uctf** (`cemeyer` on GitHub) — a standalone MSP430 emulator built specifically to replicate this game's debugger behavior closely enough to solve the actual level images offline, including the callgate/interrupt mechanics. the closest thing to a drop-in backup if the website itself is unreachable.

## for after you've solved a level (not before)

there's a real, long-standing community norm against posting full solutions publicly while they'd spoil the game for others. so: solve it yourself first, using the hint ladders in `levels/`. once you have — or once you've truly exhausted the ladder and want to see how someone else's approach differed from yours — a number of people have published their full writeups and solution notes. search `microcorruption writeup <level name>` once you're past a level and you'll find several independent approaches most of the time; comparing your solution against two or three others is a genuinely good way to learn, after the fact, in a way that reading someone else's answer first never is.

## adjacent things worth knowing exist

- **Aleph One, "Smashing the Stack for Fun and Profit"** — the 1996 paper that is, more or less, the ancestor of every stack-overflow level in this repo. short, old, still correct.
- **return-oriented programming (ROP)** — the technique Lagos is making you reinvent in miniature. worth reading a proper explainer on once you've felt the need for it yourself rather than before.
- **the unlink/heap-metadata exploitation family** — the real-world ancestor of Algiers and Chernobyl. Phrack and various CTF-writeup archives have good explainers if the shape of it interested you.
