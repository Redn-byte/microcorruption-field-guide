# cheatsheet

everything from `docs/` flattened for quick lookup. if something here feels thin, the matching doc has the actual explanation.

## registers

| reg | role |
|---|---|
| r0 | pc |
| r1 | sp |
| r2 | sr / flags, also carries the interrupt number for `INT` |
| r3 | constant generator (hardware trick, not yours to touch) |
| r4–r15 | general purpose |

**call convention:** arg1 → r15, arg2 → r14, arg3 → r13, arg4 → r12. r15 doubles as the return value.

## instructions

| | |
|---|---|
| `mov src,dst` | dst = src |
| `add / sub / and / xor` | arithmetic/bitwise, dst op= src |
| `bis src,dst` | dst \|= src |
| `bic src,dst` | dst &= ~src |
| `cmp src,dst` | flags = dst − src, discard |
| `bit src,dst` | flags = dst & src, discard |
| `push / pop` | stack ops, sp moves by 2 |
| `call / ret` | ret is `mov @sp+, pc` |
| `jmp` | unconditional |

note the order: **src, dst** — opposite of Intel-syntax x86.

## conditional jumps

| | fires when |
|---|---|
| `jz`/`jeq` | zero |
| `jnz`/`jne` | not zero |
| `jc`/`jhs` | carry / unsigned ≥ |
| `jnc`/`jlo` | no carry / unsigned < |
| `jn` | negative |
| `jge` | signed ≥ |
| `jl` | signed < |

## addressing modes

| form | meaning |
|---|---|
| `rX` | register value |
| `@rX` | memory at [rX] |
| `@rX+` | memory at [rX], then rX++ |
| `#N` | immediate |
| `N(rX)` | memory at [rX + N] |

## interrupts

| INT | args | does |
|---|---|---|
| `0x00` | char | putchar |
| `0x01` | — | getchar |
| `0x02` | addr, len | gets |
| `0x10` | — | enable DEP |
| `0x11` | page, writable? | set page perms |
| `0x20` | — | rand |
| `0x7D` | pw addr, flag addr | HSM-1: check, set flag |
| `0x7E` | pw addr | HSM-2: check and unlock |
| `0x7F` | — | unlock, unconditionally, no check |

idiom to recognize on sight: `push #N` then `call #INT`.

## printf specifiers

`%s` string from pointer · `%x` hex · `%c` char · **`%n`** writes the character count so far to a pointer argument — your write primitive in any format-string bug.

## debugger verbs (mental model — type `help` in-console for exact syntax)

breakpoint · delete breakpoint · step (one instruction) · continue (to next breakpoint) · reset (restart, keep breakpoints) · set a register or memory value directly

## bug-family → level index

| pattern | levels |
|---|---|
| secret sitting in plaintext memory / operands | New Orleans, Sydney |
| checked-value vs. acted-on-value mismatch | Hanoi |
| stack overflow → existing function (ret2win) | Cusco, Johannesburg, Santa Cruz |
| stack overflow → injected shellcode | Whitehorse, Montevideo |
| null-byte-free shellcode | Montevideo |
| hardcoded "secret" canary | Johannesburg |
| validation on the wrong field | Santa Cruz |
| integer overflow defeating a length check | Jakarta |
| format string (read + `%n` write) | Addis Ababa, Novosibirsk |
| heap metadata corruption | Algiers, Chernobyl |
| ASLR | Vladivostok |
| DEP, bypassed via the permission-flip primitive | Bangalore |
| DEP + ASLR + ROP, staged | Lagos |
| self-modifying / packed code | Hollywood |

## hex / endianness reminders

- MSP430 is little-endian: a value `0x4452` in memory reads as bytes `52 44`.
- "word" ops move 2 bytes, `.b`-suffixed ops move 1. don't mix them up when sizing a payload.
- toggle the **hex-encoded input** checkbox any time you need to send raw bytes, a crafted address, or a null byte — plain text can't express any of those.
