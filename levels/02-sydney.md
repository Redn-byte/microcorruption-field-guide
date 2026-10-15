# 02 — Sydney

*rev a.02. same idea as New Orleans, but Lockitall got cleverer about where they hide things. narrator voice: they did not.*

## what's new

the password isn't sitting in a labeled memory region this time — it's baked directly into the instruction operands of `check_password` itself, as a series of immediate-value comparisons.

## hint ladder

1. `check_password` here has almost no loop. it's a short, flat sequence of `cmp` instructions against literal constants. that's unusual — why would you hardcode a comparison value directly into an instruction instead of loading it from memory?
2. you don't need the debugger at all for this one, really — you need to read the disassembly and pull the constant operands out of each `cmp`, in order.
3. remember two things while you reconstruct the password from those constants: the MSP430 is little-endian, so each 2-byte constant is byte-reversed from how it'll read as text, and you'll need the hex-encoded-input checkbox to send raw bytes back rather than typing letters.

## why it matters

"hardcoded credential baked into a compiled binary" is an extremely real, extremely common finding in actual firmware and app security reviews — anyone with a disassembler (which, again, is free and built into your browser right now) can pull it straight out of the instruction stream. hiding a secret *better* isn't the same as encrypting or deriving it; it's still just sitting there, one layer down.
