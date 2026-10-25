# Limits

A plain list of what Ollama does not do,
so that none of it surprises you in
production — or in your living room.

## It is not a security boundary

The server has **no authentication**. No
accounts, no passwords, no tokens: anything
that can open a connection to its port can
pull models, run them, and delete them.
On the default setting that is a short
list — programs on the same machine — and
it is fine. The moment you bind the server
to a network address (chapter 10), the
list becomes *every device that can reach
that address*, and Ollama gives you no
tools to narrow it. Put it behind
something that can say no — a firewall, a
VPN, a reverse proxy with its own login —
or do not expose it. This is the single
most important limit in this chapter.

## The models are the ceiling

Ollama runs models; it does not improve
them. A 1–8 billion parameter model on a
home machine will write fluent text and
will also state falsehoods in the same
fluent voice, lose the thread of a long
document, and fail at arithmetic it sounds
sure about. Bigger models are better at
most of this and need more memory and
more time (chapter 7). Neither size is a
search engine, a database, or a memory:
the model knows what it was trained on,
knows nothing about your files unless a
program feeds them in, and forgets the
conversation when the conversation is
gone. Programs built on top of Ollama
(embeddings, retrieval, chat front-ends)
supply those parts; the neighbours in
chapter 13 point at them.

## One machine, one queue, shared fairly-ish

The server runs on one machine and shares
it. Several people or programs can be
served at once, up to the parallelism you
allow (chapter 10), but they share one
processor and one graphics card: two heavy
requests at once means each takes roughly
twice as long, and loading a second large
model can evict the first. Ollama is a
household and workbench server, not a
fleet platform; serving many strangers at
scale is what tools like vLLM are for.

## Memory is a hard wall

A model that does not fit in memory
(memory on the graphics card plus system
memory, chapter 7) does not run slowly —
it runs *very* slowly if the system swaps
to disk, or not at all. Context is not
free either: a long conversation or
document costs memory and time on every
reply. The knobs exist (context length,
quantisation, smaller models), and
chapter 7 gives the arithmetic, but the
wall does not move.

## Quantisation trades quality for size

The library's models are **quantised** —
their numbers stored at reduced precision
so they fit and run fast. The default
quantisation is a good trade, but it is a
trade: the smallest quantisations of a
model are measurably worse at hard
reasoning than the full-precision
original. Chapter 5 explains the labels;
the honest summary is that you are almost
always running a slightly compressed copy,
and for most home uses that is the right
choice, made knowingly.

## The library is a convenience, not a promise

Models in the library are prepared by
Ollama and the community from other
people's releases. A name there is not a
guarantee about the model's training data,
its licence for your use, or its behaviour
on your material. Licences vary by model
and are the publisher's, not Ollama's.
For anything beyond home use, read the
model's own page (chapter 5 shows how to
see exactly what you have).

## Platform edges

- On Windows and macOS the server is a
  desktop app: when the user logs out, the
  models go away (chapter 10).
- Graphics support is widest on NVIDIA,
  good on recent AMD and Apple silicon,
  and absent elsewhere — where everything
  still runs, on the processor, at
  processor speeds.
- The API is stable in the ways this guide
  uses it, but Ollama is young software
  that still adds commands between
  releases; the version you installed is
  in `ollama --version`, and the docs for
  that version are the court of appeal.
