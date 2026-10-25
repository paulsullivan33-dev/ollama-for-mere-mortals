# Disk, Memory and Context

Three different scarcities govern a local
model, and confusing them is the source
of most disappointment: the **disk** the
model sleeps on, the **memory** it wakes
up in, and the **context** it thinks
with. This chapter gives each its
arithmetic.

## Disk: the store

Models live in the store (chapter 2) and
stay there until removed: what you pull
is what you keep. Sizes are the download
sizes from the library page — roughly
parameters × quantisation from chapter
5's table — so a modest shelf of models
(a 1B, a 3B, an 8B, an embedding model)
is a 15–20 GB shelf. `ollama list` shows
each model's weight; `du -sh ~/.ollama`
shows the whole store. Models that share
weights share the underlying blobs, so
ten variations of one base cost little
more than the base (chapter 6 uses this).

Removing is `ollama rm name` — instant,
and the space is really freed, because
blobs are reference-counted by the
manifests that use them. There is no
cache to prune beyond that: the store is
the cache.

Where the store sits matters on a machine
with a small system disk and a big data
disk. Move it by telling the server a
different home: set `OLLAMA_MODELS` to
the new path before the server starts
(chapter 10 shows it in a service file;
for a hand-run server it is one
environment variable). Move the existing
`models` folder there first, or the
server will start with an empty shelf
and your old models will seem to have
vanished (chapter 15).

## Memory: the working set

A loaded model occupies, at minimum, its
file size again in memory — on the
graphics card if you have a suitable one,
in ordinary RAM if you don't — plus
working room: the context cache below,
and the runner's own overhead.
`ollama ps` shows the truth of your
machine: the loaded size, and whether it
sits on GPU, CPU, or split across both.

The rule of thumb that keeps beginners
out of trouble:

- **File size ≤ graphics memory** — the
  model lives on the card; full speed.
- **File size ≤ graphics + system
  memory** — it runs, split; speed falls
  off a cliff it is polite to call a
  slope. Sometimes acceptable, never
  pleasant.
- **File size > all of it** — it does
  not run. Choose a smaller model or a
  smaller quantisation (chapter 5) rather
  than arguing.

On a CPU-only machine, speed is a
property of the processor and the model
size, full stop. The lab for this guide
— two ordinary processor cores, no
graphics card — generated with a 1B
model at the speed in
`ollama-lab/transcripts/t03-api.txt`.
Your numbers will differ; your
*measurement method* should not: run a
prompt with `--verbose` or read the
timings from an API reply (chapter 8),
where `eval_count ÷ eval_duration` is
the tokens-per-second the marketing
pages round into promises.

## Context: the working memory of the mind

The **context window** is how much text
the model can hold in view at once: your
conversation so far, any document you
pasted, and the reply being written, all
counted in tokens (chapter 2's runner
counts roughly ¾ of a word per token in
English). Ollama's default window
(`num_ctx`, chapter 9) is smaller than
most modern models' maximums, on
purpose: the attention cache that
implements the window costs memory for
every token of it, on top of the model's
own size, and that cache grows as the
conversation does.

Practical consequences:

- A long chat that suddenly forgets its
  first instructions has not broken; it
  has slid out of the window. Start a
  new conversation, or raise `num_ctx`
  and pay the memory.
- Feeding a document costs twice: the
  tokens occupy the window, and the
  model must read them all (the
  `prompt_eval` phase) before the first
  word of the reply. Long document,
  long pause, on any hardware.
- An embedding of "how much context do I
  need" that works: a page of text is
  roughly 500 tokens; a twenty-page
  report, 10,000. Size the window for
  the job in front of you, not the job
  you can imagine.

## Putting it together

The shopping question — *will this model
run well on this machine?* — is three
questions: does it fit on disk (almost
always yes), does it fit in graphics or
system memory (the chapter 5 table times
the parameter count, plus a margin for
context), and is your processor or card
fast enough at that size for the patience
you have (measured, not advertised).
Answer those honestly and Ollama will
rarely surprise you; skip them and every
surprise will feel like the software's
fault. It is arithmetic all the way
down.
