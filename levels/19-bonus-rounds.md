# 19 — the bonus rounds

NCC Group took over the game and, in late 2022, added four new levels under a "LockIT 2" banner with a different debug interface — one pitched as being for in-field debugging rather than a password prompt. the company's own announcement described these as being modeled on real vulnerability classes their hardware and embedded-systems practice had actually found in client products, which is a nice bit of honesty most CTFs don't bother with.

heads up before you start this section: my research turned up solid, specific detail on Vancouver, and only the official one-line descriptions for the other three. I'd rather tell you that plainly than fake a hint ladder for levels I haven't actually traced through. treat the Cold Lake / Churchill / Baku entries below as "here's what's confirmed to exist and why it's worth your time," not a guide.

## Vancouver

a debug payload format instead of a password: you send an address, a size byte, and raw instructions, and the firmware copies your bytes to that address and jumps to them. it's explicitly billed as a chance to get comfortable writing MSP430 by hand before the rest of the series ramps up again — there's no `unlock_door` lying around, so you're back to writing your own tiny interrupt-calling shellcode like Whitehorse, except this time the vehicle for getting it to run is "the debug interface will straightforwardly run whatever you hand it," which makes it a good warm-up rather than a real puzzle.

**hint:** read the payload format carefully — address, then a size, then your bytes — and notice that the size field controls how much gets *copied*, not necessarily how much needs to exist at the destination already. there's a cute optimization available where you only need to copy a couple of fresh bytes and let them combine with instructions already sitting in memory, but a straightforward "copy my whole five-byte shellcode and jump to it" answer works fine too and is the right place to start.

## Cold Lake and Churchill

both billed, in NCC Group's own description, as bootloader-based vulnerabilities pulled from real findings in their embedded-security practice. I don't have a traced-through walkthrough for either of these to responsibly hand you a hint ladder — which, honestly, might be the more interesting way to meet them: go in with everything else in this repo already in your head, and no scaffolding beyond "this is a bootloader, and bootloaders tend to trust things they shouldn't."

## Baku

NCC Group's own announcement is characteristically unhelpful about this one — paraphrased, it amounts to "we'll just let you discover Baku's secrets on your own." so will I. go find out.
