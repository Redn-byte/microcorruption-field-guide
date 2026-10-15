# 08 — Johannesburg

*rev b.04. changelog: "a firmware update rejects passwords which are too long." they added a stack canary. they also left the canary's value sitting right there in the code you're reading.*

## what's new

back to HSM-1 (so `unlock_door` exists again — good news), but now there's a single guard byte sitting on the stack between your buffer and the saved return address, and `login` checks it against a fixed value before it'll return normally. this is a stack canary: a real, genuine mitigation technique, implemented in the laziest possible way.

## hint ladder

1. do the usual overflow-and-crash check first. you'll likely find the program now detects something's wrong and refuses to continue cleanly, rather than happily jumping wherever your garbage return address points.
2. find the comparison that's catching you. it's checking one specific byte, at one specific offset from the start of your buffer, against one specific constant — and that constant is sitting right there in the `cmp.b` instruction's operand, in the disassembly you already have open.
3. a canary only works as a *secret*. this one is hardcoded in the binary you're reading. include the correct value at the correct position in your overflow, exactly as you built the rest of it, and continue straight on to the `unlock_door` redirect from Cusco.

## why it matters

real stack canaries work because they're generated randomly per-process-run and the attacker (in the normal threat model) can't read process memory to recover them before triggering the overflow. the moment a "secret" value is baked into a static binary that the attacker can read at leisure — or leaked through some other bug — it stops being a secret and becomes a formality you type back. the mitigation technique is real. this particular implementation of it isn't.
