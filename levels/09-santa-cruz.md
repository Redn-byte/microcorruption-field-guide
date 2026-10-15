# 09 — Santa Cruz

*rev b.05. changelog: "further rejects passwords which are too long." they checked the password's length this time. they forgot there's a username field too.*

## what's new

your first username-and-password login. there's a length check — but go verify for yourself exactly *which* field it's actually validating before you assume it covers both.

## hint ladder

1. find the two length checks (there should be one guarding a minimum, one guarding a maximum) and trace each one back to which input buffer it's actually measuring. they are not both guarding the same field.
2. the unguarded field is where your overflow needs to happen instead. the mechanics from Johannesburg still apply here — there's a canary byte again, and you'll need to satisfy it the same way: find its hardcoded value in the disassembly and include it at the right offset.
3. one extra wrinkle: the exact length you choose for your overflowing field matters for more than just reaching the return address — it also determines whether a null byte lands exactly where the canary check expects to find one. budget a little trial and error here, using the debugger to watch where things actually land rather than guessing on paper.

## why it matters

"we added validation" is not the same claim as "we added validation to every place that needed it." multi-field forms are exactly where this kind of gap hides in real applications — the obviously-dangerous field gets hardened first, and the quieter one next to it doesn't, until someone goes looking specifically for the field nobody thought to check.
