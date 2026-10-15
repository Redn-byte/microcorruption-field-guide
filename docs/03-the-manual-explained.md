# the Lockitall manual, de-manualed

every level links a PDF: the actual LockIT Pro user manual, in-universe, written in genuine corporate-manual deadpan. ("this equipment is intended for use in a laboratory test environment only.") it's worth reading once because it's funny, but you don't need to parse manual-speak to get the three things out of it that actually matter for playing. here they are, translated.

## software interrupts: `INT`

the lock firmware doesn't have real OS syscalls, but it has the next best thing: a callgate at a fixed address that acts like one. from your side as a player, using it looks like a two-step dance you'll see in disassembly constantly:

```
push  #0x7f      ; the interrupt number, as an argument
call  #INT       ; INT is just a function at a fixed address
```

push the interrupt number, then call the function named `INT`. that's it. internally it stuffs the number into the high byte of the status register and dispatches, but you'll basically never need to think about that part — you need to recognize the `push #N; call INT` pattern on sight, because it's the single most important idiom in the whole game. **"what number got pushed right before that `call INT`" is, more often than you'd expect, the entire exploit.**

### the interrupt table

| INT | name | arguments | does |
|---|---|---|---|
| `0x00` | putchar | 1: character | print one character |
| `0x01` | getchar | none | read one buffered character |
| `0x02` | gets | 2: destination address, max length | read up to *length* bytes of input into memory at *address* |
| `0x10` | enable DEP | none | turn on memory protection: every page becomes either executable or writable, never both |
| `0x11` | set page permission | 2: page number, 1=writable/0=executable | flip a specific page between the two |
| `0x20` | rand | none | get a random 16-bit number |
| `0x7D` | HSM-1 check | 2: password address, flag address | ask the hardware security module if the password is right; if so, write a nonzero byte at *flag address* |
| `0x7E` | HSM-2 check-and-unlock | 1: password address | ask the HSM if the password is right; if so, unlock the door directly — no separate flag step |
| `0x7F` | unlock | none | just open the door. no check. nothing stops a program from calling this with a correct password already verified upstream — and nothing stops *you* from finding a path to it that skips the verification entirely |

a couple of these are worth sitting with:

- **`0x7F` has no password check built in at all.** it's purely "open the door." every level either (a) doesn't have an easy way to reach it, which is itself interesting, or (b) reaches it only after a password check you're meant to defeat some other way.
- **HSM-1 vs HSM-2 is a trust-boundary question.** HSM-1 (`0x7D`) separates "was the password correct" from "did the door actually open" into two steps — it just sets a flag, and it's up to the rest of the program to *act* on that flag correctly. that gap between "checked" and "acted on" is exactly where several early levels' bugs live. HSM-2 (`0x7E`) collapses that gap by unlocking directly, which closes off that particular bug — and opens a different one, because now there's often no separate `unlock_door` function anywhere in the binary for you to jump to. you have to make your own.

## memory protection, i.e. DEP

`0x10` and `0x11` are, in miniature, exactly what Data Execution Prevention / the NX bit does on a real operating system: every page of memory is either **executable** or **writable**, never both, once protection is turned on. the reasoning is the same reasoning real OSes use — if an attacker can only ever write to memory that can't be executed, "inject code onto the stack and jump to it" stops working.

the game-sized version of the bypass is the same shape as the real-world one, too: **find a way to get `0x11` called, with arguments you control, on the page where your own bytes live.** real exploit chains do this by hijacking control flow to call `VirtualProtect` or `mprotect` with attacker-chosen arguments; here it's the same move against a much smaller target.

## the C standard library stand-in

the lock's firmware calls a tiny emulated libc. you'll see these constantly in disassembly:

- `gets(buf, len)` / `getsn` — read input, essentially the same as `INT 0x02`.
- `putchar(c)`, `getchar()`, `puts(buf)` — unsurprising.
- `printf(fmt, ...)` — supports `%s` (print a string from a pointer), `%x` (print a value as hex), `%c` (print a character), and **`%n`**.

sit with `%n` for a second, because it's doing a lot of work across several levels. `%n` doesn't print anything — it takes a pointer argument and **writes** the number of characters printed *so far* to that address. if a program ever lets you control the format string itself, rather than just supplying arguments to a fixed format string, you've been handed both an arbitrary memory **read** (via `%s`/`%x`, walking the stack to leak whatever pointers are sitting there) and an arbitrary memory **write** (via `%n`) — for free, with no buffer overflow required at all. this is the classic format-string vulnerability, and you'll meet it directly once you're a third of the way through the level list.
