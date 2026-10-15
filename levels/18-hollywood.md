# 18 — Hollywood

*the last of the original nineteen. the program is polymorphic — it doesn't sit still long enough for you to read it the normal way. budget real time for this one.*

## what's new

rather than running as one static block of code, this program continuously unpacks and copies fragments of itself into different, shifting locations as it runs, cleaning up behind itself as it goes. trying to read a full, stable disassembly of "the program" from a cold static view mostly fails, because there isn't one single stable version of it sitting still in memory at any given moment.

## hint ladder

1. stop trying to statically disassemble the whole thing up front. instead, run it, let it reach the point where it's actually asking you for a password, and capture a **live memory dump** right there — that partially-unpacked snapshot, at that one moment, is readable in a way the program as a whole isn't.
2. treat what you've captured as its own small reverse-engineering problem: pull that dump out, load it into whatever disassembler you're comfortable with outside the browser (this is a very reasonable level to step outside the in-browser view for), and figure out what it's actually checking at that specific point, rather than trying to trace the entire unpacking machinery that got you there.
3. once you understand the check being performed at that moment, the rest of the level tends to resolve into familiar shapes from everything before it — the novelty here is almost entirely in the reconnaissance, not in some brand-new exploitation primitive waiting at the end.

## why it matters

this is your first brush with self-modifying / packed code, which is exactly the obfuscation style real malware analysts deal with constantly — code that's deliberately hard to read statically because it's actively rewriting or relocating itself. the core technique that gets you through it (stop fighting the packer, run it to the interesting moment, and dump memory right there) is a completely real, completely standard move in that line of work, not a trick specific to this game.

finishing this one means you've done all nineteen of the original levels. go look at other people's writeups now if you want to — you've more than earned it. `RESOURCES.md` has a few.
