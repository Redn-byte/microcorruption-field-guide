# 11 — Addis Ababa

*rev b.03. changelog: "usernames are now printed back to you for verification." thoughtful. also, catastrophic.*

## what's new

your first format-string vulnerability — the thing `03-the-manual-explained.md` warned you to sit with. the program takes your username and hands it directly to `printf` as the *format string itself*, rather than as an argument to a fixed one.

## hint ladder

1. confirm it: find the call where your username gets echoed back, and check what's actually being passed as `printf`'s first argument. if it's your raw input rather than a fixed string like `"Hello, %s"`, you have a format-string bug, and anything you type that looks like a conversion specifier (`%x`, `%s`, `%n`) is going to get interpreted by `printf`, not printed literally.
2. start small and just prove you have control: send a username containing a handful of `%x` in a row and watch what comes back in the I/O console. you're reading raw values off the stack that have nothing to do with your input — that's the leak working.
3. the real goal is a specific flag byte in memory that the real password check reads from later. `%s` lets you read an arbitrary address by putting that address on the stack first (via a `%x` or two to walk the stack pointer into position) and then dereferencing it; `%n` lets you *write* to an arbitrary address the same way, writing however many characters `printf` has produced so far. you need the write side: get a small, controlled number of characters printed, then land a `%n` with the flag's address positioned correctly to receive it.

## why it matters

this is the foundational format-string bug — `printf(user_input)` instead of `printf("%s", user_input)` — and it's both one of the oldest bug classes in C and still a real finding today whenever someone does the same thing with a logger, a template string, or an error message. `%n` turning a *read* primitive into a *write* primitive with zero buffer overflow involved is the part that genuinely surprises people the first time they see it work.
