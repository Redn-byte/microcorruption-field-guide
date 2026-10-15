# 03 — Hanoi

*rev b.01. Lockitall's changelog: "added a Hardware Security Module so the password never has to be compared in software again." bold move for a company whose software is the thing being attacked.*

## what's new

your first Hardware Security Module (HSM-1, `INT 0x7D`). read `03-the-manual-explained.md` again if you skipped it — the whole level hinges on the gap HSM-1 leaves open: it only *sets a flag*. it does not unlock anything itself. whatever decides to act on that flag is still regular, readable, attackable code.

## hint ladder

1. find where `0x7D` gets called, and find the flag address it's told to write to. then find where `login` actually makes its unlock decision — is it reading *that* flag? or is it reading something else nearby that you have more influence over?
2. your own input is written into memory somewhere before the HSM call happens. check whether the bytes you control extend further than the program intended — specifically, whether they reach all the way up to whatever address `login`'s real unlock decision depends on.
3. the real check, when you find it, is comparing one specific byte at one specific address against one specific ASCII value. you don't need the HSM to agree with you about the password at all if you can just... put the right byte in the right place yourself.

## why it matters

this is the Hanoi bug pattern, and it recurs constantly in real systems: **"was authenticated" and "is authorized to proceed" got stored in two different places, and only one of them is actually trustworthy.** an HSM, a third-party auth provider, a signed JWT — doesn't matter how strong the actual check is if the code consuming its result is checking the wrong variable, or a variable an attacker can reach by some other path.
