# 12 — Novosibirsk

*rev c.02. Lockitall kept the "print the username back" feature from Addis Ababa. they also moved to HSM-2. these two decisions do not get along.*

## what's new

same format-string bug as Addis Ababa, but now aimed at a juicier target: the `push #0x7e; call INT` pair that calls HSM-2 to check-and-unlock. your `%n` write primitive doesn't need to touch some separate flag byte this time — it can touch the interrupt number itself.

## hint ladder

1. confirm the format-string bug works exactly like last time — you already know how to leak and how to land a write.
2. instead of hunting for a flag address, find the exact address of the operand byte in `push #0x7e` — the literal `0x7e` sitting in the instruction stream, about to be pushed and handed to `INT`.
3. you don't need to change much — `0x7e` and `0x7f` differ by exactly one. aim your `%n` write at that single operand byte with a character count that lands it one higher than it currently is, and let the program's own, perfectly normal control flow carry your (now different) interrupt number the rest of the way to `INT` for you.

## why it matters

the lesson here isn't a new bug class — it's that the *same* primitive (arbitrary write, via a format string) pays off completely differently depending on what's reachable nearby. last level it flipped a flag a check read later; this level it patches an instruction's operand moments before that instruction runs. once you have "write any value to any address," what you choose to write it to is the whole rest of the exploit.
