# microcorruption, a field guide

a beginner's route through [microcorruption](https://microcorruption.com), the embedded reverse-engineering CTF where you break into a fictional smart lock, one firmware "security update" at a time.

## what even is this

in 2013 the security firm Matasano teamed up with Square and built a game around a company called **Lockitall** and their flagship product, the **LockIT Pro**. the pitch, in-universe: a smart door lock running on a real TI MSP430 microcontroller, which the manual proudly notes "can be powered off a lemon." every level is a new firmware revision of the lock, usually released because the last revision got broken into. you are the reason the changelog keeps growing.

the bit that makes it worth your time instead of just a cute theme: the assembly is real. MSP430 is a genuine, widely-used low-power chip family, the debugger behaves like an actual hardware debugger (breakpoints, single-stepping, live memory and register views), and the bug classes you'll find — buffer overflows, format strings, integer overflows, heap corruption, DEP, ASLR — are the exact same bug classes that show up in real CVEs. you're just fighting them at a scale small enough to hold the whole program in your head at once, which is the entire point.

NCC Group owns it now and added a handful of new levels in 2022 (see [`levels/19-bonus-rounds.md`](levels/19-bonus-rounds.md)). as of the most recent notes I could find, people were still actively playing and writing about it. websites occasionally go down though — if `microcorruption.com` is unreachable when you get there, [`RESOURCES.md`](RESOURCES.md) has an offline MSP430 emulator that runs the same level images.

signup is free: email address, verify, log in. no payment, no application.

## how this repo is laid out

```
docs/
  00-getting-started.md       account, UI tour, the one checkbox that will save you an hour
  01-msp430-primer.md         the ISA — registers, instructions, addressing modes, calling convention
  02-debugger-field-guide.md  how to actually drive the debugger
  03-the-manual-explained.md  Lockitall's own manual, translated out of manual-speak

levels/
  00-tutorial.md  …  18-hollywood.md    one file per level, in order
  19-bonus-rounds.md                     the newer NCC Group levels

CHEATSHEET.md        everything above, flattened into tables, for when you just need to look something up
RESOURCES.md         manual PDF, offline emulator, further reading
notes-template.md    a blank template — copy it per level if you want to keep your own log
```

start with `docs/00-getting-started.md`, skim the primer and the manual doc before you touch level 1 (they're short, and level 1 assumes you've read them), then work through `levels/` in order. the levels get meaner fast around the back half.

## house rules on the hints

each level file is a ladder, not an answer key: a light nudge, then a stronger one, then the actual shape of the bug and why it exists — but never a payload you can paste in and move on. that's partly pedagogy and partly etiquette; there's a long-standing norm in this community against dumping full solutions publicly (the earliest writeups I found were pulled down specifically for that reason), and partly it's just that the "aha" is the whole reason to play a game like this instead of reading a paper. if you genuinely get stuck past the last rung, the level file points you toward where other people's full writeups live — go look *after* you've earned the right to be annoyed at yourself.

have fun. go be a nuisance to a fictional lock company.
