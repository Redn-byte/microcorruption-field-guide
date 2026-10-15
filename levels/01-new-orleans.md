# 01 — New Orleans

*rev a.01. the password lives in memory. nothing is hiding it from you.*

## what's new

your first real "the secret isn't actually secret" level. `check_password` compares your input, byte by byte, against a password sitting somewhere in RAM.

## hint ladder

1. find `check_password`. it reads a byte of your input and compares it against a byte somewhere else. where's "somewhere else"?
2. breakpoint right before the comparison loop starts, run it, and just look at the live memory view at the address the comparison is reading from. you don't need to defeat anything — you need to *read*.
3. the password is sitting in plaintext in memory before you've even sent your first guess. copy it, send it back.

## why it matters

this is the simplest possible version of a real and common mistake: storing a secret in a way that anything with read access to memory (or a firmware dump, or a config file, or a process's heap) can just... read. "the check is correct" and "the secret is protected" are two completely different properties, and this level only has the first one.
