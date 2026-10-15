# 10 — Jakarta

*rev b.06. changelog: "username and password combined cannot exceed 32 characters." a real check. checked with a single byte.*

## what's new

Lockitall closed the Santa Cruz gap by checking the *combined* length of username and password together. the bug moved from "which field is checked" to "how is the length itself computed and compared."

## hint ladder

1. find where the combined length gets computed, and find the instruction that compares it against the limit. look specifically at the *size* of that comparison — is it comparing a full 16-bit value, or just the low 8 bits of it (a `.b`-suffixed compare)?
2. an 8-bit comparison can only ever see values from 0 to 255. if the real combined length of what you send is something comfortably larger than that — say, into the several-hundred-byte range — what does the low byte of that total actually look like to a check that can't see past it?
3. work out a username length and a password length that, added together, produce a huge real total while that total's *low byte* still reads as small enough to pass. that gets both fields long enough, combined, to reach and overwrite the return address, with no canary in this version to satisfy on top of it.

## why it matters

this is a straightforward, famous bug class: an integer overflow (or in this case, a truncation — a 16-bit sum getting compared with only an 8-bit-wide instruction) defeating a length check that was, on its own logic, entirely correct. the check isn't wrong about what it compares — it's wrong about how big a number it's capable of seeing. this exact shape (a huge value "wrapping" or truncating down to something that reads as small to a narrower check) has caused real, serious vulnerabilities in image parsers, network protocol handlers, and allocator size calculations.
