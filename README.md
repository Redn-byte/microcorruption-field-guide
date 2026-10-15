# microcorruption, a field guide

a beginner's route through [microcorruption](https://microcorruption.com), the embedded reverse-engineering CTF where you break into a fictional smart lock, one firmware "security update" at a time. written while working through it myself, for anyone else starting out.

## the whole game on one page

every level is a new revision of the same lock, shipped because the last one got broken. each revision patches the previous bug and leaves a new one behind, so the level list doubles as a curriculum. it climbs from "the secret is just sitting there" to "build your own exploit chain past DEP and ASLR":

```
  the secret isn't secret       New Orleans · Sydney
  checked ≠ acted on            Hanoi
  overflow → existing code      Cusco · Johannesburg · Santa Cruz
  overflow → your own code      Whitehorse · Montevideo (no null bytes allowed)
  arithmetic lies               Jakarta (integer overflow beats a length check)
  format strings                Addis Ababa · Novosibirsk (read with %x, write with %n)
  heap metadata                 Algiers · Chernobyl
  moving targets                Vladivostok (ASLR)
  non-executable memory         Bangalore (DEP)
  everything at once            Lagos (DEP + ASLR + ROP)
  code that rewrites itself     Hollywood
  ─────────────────────────────────────────────────────────
  newer NCC Group levels        Vancouver · Cold Lake · Churchill · Baku
```

if one row interests you more than the rest, that's fine. the levels still build on each other, so play them in order anyway.

## what even is this

in January 2014, the security firm Matasano teamed up with Square and launched a game built around a company called **Lockitall** and its flagship product, the **LockIT Pro**. in-universe, the LockIT Pro is a smart door lock running on a real TI MSP430 microcontroller, which the manual proudly notes "can be powered off a lemon." every level is a new firmware revision of the lock, usually released because the last revision got broken into. you are the reason the changelog keeps growing.

the cute theme isn't the reason to play. the reason is that the assembly is real. MSP430 is a genuine, widely used low-power chip family. the debugger behaves like an actual hardware debugger, with breakpoints, single-stepping and live memory and register views. and the bugs you'll find (buffer overflows, format strings, integer overflows, heap corruption, DEP and ASLR) belong to the same classes that show up in real CVEs. you just fight them at a scale small enough to hold the whole program in your head at once, which is the entire point.

NCC Group (which acquired Matasano) [added new levels in October 2022](https://www.nccgroup.com/research/check-out-our-new-microcorruption-challenges/). see [`levels/19-bonus-rounds.md`](levels/19-bonus-rounds.md). websites occasionally go down, so if `microcorruption.com` is unreachable, [`RESOURCES.md`](RESOURCES.md) has an offline MSP430 emulator that runs the same level images.

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

start with `docs/00-getting-started.md`. skim the primer and the manual doc before you touch level 1, because they're short and level 1 assumes you've read them. then work through `levels/` in order. the levels get meaner fast around the back half.

## house rules on the hints

each level file is a ladder, not an answer key. you get a light nudge, then a stronger one, then the actual shape of the bug and why it exists, but never a payload you can paste in and move on with. that's partly pedagogy and partly etiquette, because this community has a long-standing norm against dumping full solutions publicly. mostly, though, the "aha" is the whole reason to play a game like this instead of reading a paper. if you're still stuck past the last rung, the level file points you toward where other people's full writeups live. go look *after* you've earned the right to be annoyed at yourself.

one test for whether you've actually got a level: **can you say the bug in one sentence?** if you can't compress it that far yet, you probably haven't found it yet. [`notes-template.md`](notes-template.md) is built around that question.

## contributing

found something wrong, outdated, or confusing? open an issue or a PR. corrections to the history, the MSP430 details, or the debugger notes are especially welcome. the one thing that won't be merged is a full solution or a paste-ready payload. see the house rules above.

## license

[CC BY-SA 4.0](LICENSE). share it, fork it, translate it, fix it. keep the credit, and keep derivatives under the same license.

have fun. go be a nuisance to a fictional lock company.
