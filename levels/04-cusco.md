# 04 — Cusco

*rev b.02. changelog: "fixed issues with passwords which may be too long." narrator voice: they had not.*

## what's new

your first stack buffer overflow, and your first `ret2win`. the password gets written onto the stack with no length check, which means a long enough input doesn't just fill the buffer — it keeps going, straight over the saved return address sitting just past it.

## hint ladder

1. find where your input lands on the stack, and find how far away the saved return address is from the start of that buffer. send something way too long — forty-something bytes of a single repeated character — and watch, in the register view, whether the program counter ends up containing bytes from your own input after the function returns.
2. once you can crash it on command, the question becomes: instead of junk, what *useful* address could you place exactly where the return address goes?
3. there's an `unlock_door` function sitting in this binary already, fully written, never called from the normal login path. you don't need to write any code of your own this time — you need the program to jump to code that already exists. find its address, and place it, correctly byte-reversed, exactly where the overwritten return address lives.

## why it matters

this is *the* classic stack smash — Aleph One's "Smashing the Stack for Fun and Profit" in miniature, and the direct ancestor of a staggering number of real CVEs. "redirect execution to a function that already exists in the binary" (as opposed to injecting new code) is also the seed of return-oriented programming, which you'll meet properly a dozen levels from now once DEP makes injecting your own code stop working.
