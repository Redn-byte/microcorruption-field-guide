# 05 — Reykjavik

*rev a.03. changelog: "implemented military-grade on-device encryption to keep the password secure." narrator voice: check what's actually being compared before you believe the marketing copy.*

## what's new

no HSM this time — instead, a block of data gets decrypted at startup using a stream cipher, and the decrypted result is what your input eventually gets checked against. it looks intimidating. it is mostly theater.

## hint ladder

1. find the decryption routine before you panic about it. look for the shape of a stream cipher — specifically, a loop that runs exactly 256 times, twice, followed by a second loop doing the actual byte-by-byte work. that two-phase shape is a strong tell for one very famous, very small cipher, and once you recognize *which* one, you don't need to understand every line of it — you need to know its name.
2. the key for that cipher is sitting in plaintext nearby, as a string. check how long the cipher's own code allows the key to be — is it actually using the whole string, or silently truncating it? either way, you have what you need to decrypt the block yourself, outside the game entirely, in a language you're comfortable in.
3. here's the part that makes this level kind of a joke: once you've decrypted everything and found what your password actually gets compared against, look carefully at *how many bytes of it the comparison in `login` actually checks.* the encryption was real. the check built on top of it is lazier than the encryption deserved.

## why it matters

two separate lessons stacked on top of each other: first, recognizing a well-known cryptographic primitive by its *shape* in disassembly is a real, transferable reverse-engineering skill — you will see this exact two-loop pattern again outside this game. second, and more important: strong crypto protecting a value that then gets checked sloppily protects nothing. the strength of the weakest actual gate is what matters, not how impressive the stuff built around it sounds.
