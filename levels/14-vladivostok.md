# 14 — Vladivostok

*no more changelog excuse — this is a different series entirely. the whole program relocates itself to a random address every single run. everything you've been doing by hardcoding addresses stops working, on purpose.*

## what's new

ASLR. at startup the code copies itself to a randomly chosen location in memory before continuing to run from there, and the original location gets wiped behind it. every hardcoded-address trick from the last thirteen levels is now unusable as-is — not because the underlying bugs are gone, but because you no longer know, in advance, where anything you'd want to jump to actually lives on a given run.

## hint ladder

1. watch the relocation happen, once, slowly, step by step, before trying to beat it. find the routine that's doing the copying (it'll look like a loop calling something copy-shaped, with a randomized destination) and step through it in the debugger until you can see, concretely, where your program ended up this particular run.
2. there are two fundamentally different families of answer here, and both have been used by real players: either find a way to **leak an address** at runtime (is there anything printed back to you, or readable through some other bug, that reveals where something landed this run?) — or find a way to **interfere with the relocation itself**, before it finishes deciding where things go, since the code doing the relocating has to exist somewhere fixed and known in order to run at all.
3. if you're chasing the leak route: look for anything resembling the format-string or print-based bugs from a few levels back reappearing here in a new shape — a leaked stack value is often a return address, and a return address tells you exactly how far from a known location you're currently executing.

## why it matters

Address Space Layout Randomization is one of the handful of mitigations that actually shipped broadly on real operating systems and genuinely changed how exploits get built — "hardcode the address of your shellcode or your gadget" simply stops working, full stop, and every ASLR bypass in the real world reduces to some version of the two families above: leak an address at runtime, or find a target that wasn't randomized in the first place. you're doing the exact same reasoning here, just on a lock small enough to actually finish reasoning about in one sitting.
