# 13 — Algiers

*rev d.01. the "LockIT Pro Account Manager" arrives, backed by actual dynamic memory allocation. first heap level. buckle in a little.*

## what's new

username and password now get `malloc`'d onto a heap instead of living on the stack, and get `free`'d again once the HSM has checked them. the bug lives in what `free` is willing to believe about the chunk it's given.

## hint ladder

1. before anything else, figure out the shape of a heap chunk here: a few bytes pointing at the previous chunk, a few pointing at the next, a size-and-status field, then your actual data. you'll need this layout in your head (or sketched on paper — this is a genuinely good level to actually draw out) before anything else makes sense.
2. both your username and password buffers are undersized relative to how much input the program will actually accept from you. that gap is your way into writing past your own chunk's data and into the *next* chunk's metadata — specifically its size-and-status field and its forward/back pointers.
3. `free` trusts the metadata it's given. if you can make it believe a neighboring chunk is unallocated when it isn't, `free` will "helpfully" merge the two chunks for you — recalculating pointers and a combined size using arithmetic you now substantially control. walk through that merge math by hand once (it involves the existing size field, your corrupted one, and a fixed chunk-header size), aim it at something worth overwriting — the saved return address of `login`, or `free`'s own — and let a function whose entire job is "safely manage memory" do your writing for you.

## why it matters

this is a scaled-down version of real heap exploitation technique from the 2000s and 2010s (the unlink/unsafe-unlinking family of bugs, which shows up repeatedly in glibc, dlmalloc, and countless custom allocators). the generative idea underneath all of it: **an allocator's metadata lives in the same memory it's managing, right next to your data, and if you can reach it, the allocator will do arbitrary writes on your behalf because it has no way to tell "the real previous chunk" from "whatever bytes currently happen to look like a previous chunk."**
