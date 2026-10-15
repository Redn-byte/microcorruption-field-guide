# 07 — Montevideo

*rev c.03. changelog: "rewrote the login code to conform to our internal secure development process." the code now uses `strcpy`. this is, somehow, a downgrade.*

## what's new

same HSM-2 shellcode-injection shape as Whitehorse, except your input now gets moved into place with a copy routine that stops the instant it hits a null (`0x00`) byte — exactly like the real C `strcpy`. if your shellcode from last level contains a null byte anywhere, it gets silently truncated before it ever reaches the stack.

## hint ladder

1. confirm the overflow and injection point still work the same way as Whitehorse. then look hard at the exact bytes your Whitehorse-style shellcode assembles to — is there a `0x00` hiding anywhere in there? (there's a very good chance there is, right where you'd push a small interrupt number as a full two-byte word.)
2. you need a different, functionally identical way to get the value `0x7F` into a register or onto the stack, without ever writing the literal byte `0x00` anywhere in your shellcode. think about operations that *produce* a small value out of two larger ones, rather than writing the small value directly.
3. two general approaches both work here: build `0x7F` out of a larger immediate value using arithmetic (something like a load-then-mask or a subtract that lands exactly on the number you need), or notice that the number `0x7E` already exists somewhere else in this very binary — in code you're allowed to read from — and see if you can grab it from there and adjust it by one instead of writing either number directly yourself.

## why it matters

null-byte-free shellcode is its own small, famous genre of real-world exploit engineering, precisely because `strcpy` and friends are everywhere in C code and all of them stop at the first null. the trick of "build the value you need out of operations rather than typing the value directly" generalizes way past this one byte — it's the same instinct behind bypassing character filters, WAF rules, and antivirus signatures that just pattern-match on literal bytes.
