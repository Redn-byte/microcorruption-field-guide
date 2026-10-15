# the MSP430, briefly

you don't need to become an assembly programmer to play this. you need to be able to *read* a screen of it and know what each line did. this is everything that's load-bearing for that.

## the shape of it

16-bit RISC core, RAM-based stack, 16 registers, little-endian, two-byte words. that's the whole architecture summary. a handful of consequences fall straight out of it:

- addresses and register values are 4 hex digits (`0x4400`, not `0x44`).
- a 16-bit value in memory is stored low-byte-first. if you see `5c01` sitting in the memory dump and you need it as a value, it's `0x015c`, not `0x5c01`. this bites everyone at least once — usually while building a return address.
- "word" operations (no suffix, e.g. `mov`) move 2 bytes. "byte" operations (`.b` suffix, e.g. `mov.b`, `cmp.b`) move 1. mixing these up is a classic self-inflicted bug.

## registers

| register | role |
|---|---|
| `r0` (`pc`) | program counter — the address of the next instruction |
| `r1` (`sp`) | stack pointer |
| `r2` (`sr`) | status register — flags (zero, carry, negative, overflow); also where the game stashes the interrupt number when you call `INT` |
| `r3` | constant generator — a hardware trick that lets the assembler encode common constants (0, 1, 2, 4, 8, −1) without burning an extra instruction word. you'll never set this on purpose, you'll just occasionally see it in disassembly and wonder why |
| `r4`–`r15` | general purpose |

the one register fact you'll use constantly: **arguments to a function go in `r15`, then `r14`, then `r13`, then `r12`**, in that order, and `r15` doubles as the return value. so when you see `mov sp, r15` immediately before a `call`, read it as "the first argument is the stack pointer." when a function is about to return something, watch `r15` right before the `ret`.

## instructions you'll actually see

```
mov   src, dst     dst = src
add   src, dst     dst += src
sub   src, dst     dst -= src
and   src, dst     dst &= src
xor   src, dst     dst ^= src
bis   src, dst     dst |= src        (BIt Set — this is "or")
bic   src, dst     dst &= ~src       (BIt Clear — this is "and not")
cmp   src, dst     flags = dst - src, result discarded
bit   src, dst     flags = dst & src, result discarded   (like x86's `test`)
push  src          sp -= 2; mem[sp] = src
pop   dst          dst = mem[sp]; sp += 2
call  dst          push return address, jump to dst
ret                pop into pc        (literally `mov @sp+, pc`)
jmp   dst           unconditional jump
```

notice the order: **`op src, dst`** — the destination is on the right. if you're used to Intel-syntax x86 (`mov eax, ebx` meaning `eax = ebx`), this is backwards from that. get the order wrong in your head and every instruction reads as the opposite of what it does.

conditional jumps, which decide based on the flags a `cmp` or `bit` just set:

| mnemonic | jumps when |
|---|---|
| `jz` / `jeq` | result was zero |
| `jnz` / `jne` | result was non-zero |
| `jc` / `jhs` | carry set / unsigned ≥ |
| `jnc` / `jlo` | carry clear / unsigned < |
| `jn` | negative flag set |
| `jge` | signed ≥ |
| `jl` | signed < |

## addressing modes

how an operand in an instruction tells you where its value actually lives:

| written as | means |
|---|---|
| `rX` | the value in register X, directly |
| `@rX` | the value in memory at the address held in rX (a pointer dereference) |
| `@rX+` | same, then increments rX afterward — this is how the program walks a string or buffer one byte at a time |
| `#N` | the literal constant N |
| `N(rX)` | the value in memory at address `rX + N` — used constantly for reading stack locals relative to the frame pointer or stack pointer |

that last one, indexed addressing, is what most "where does this local variable actually live" questions resolve to. if you see `-0x18(r4)`, that's "the 16-bit value sitting 0x18 bytes *before* whatever address is currently in r4."

## where things live

there's no fixed memory map you can memorize once and reuse — it genuinely varies level to level, sometimes even between revisions of the "same" lock — but the pattern repeats: program code tends to start up around `0x4400`, and your input buffer tends to land somewhere in the `0x2400`–`0x4400` range or directly on the stack, depending on the level. don't trust this doc for exact addresses. trust the live memory view, every time.
