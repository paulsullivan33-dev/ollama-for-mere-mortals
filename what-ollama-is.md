# What Ollama Is

Ollama runs **large language models** —
the kind of program that answers questions,
writes text and writes code — on a machine
you own. Not in a data centre, not behind
someone else's account: on the laptop on
your desk, the spare PC under it, the small
server in the cupboard. You type

```bash
ollama run llama3.2
```

and a model downloads, loads and starts
answering, in the same window, in seconds.
That one command is most of what people
know about Ollama, and it is deliberately
boring: the project's whole idea is that
running a model locally should feel like
running any other program.

## The trade it makes

A language model is a very large file of
numbers — its **weights** — plus a program
that does arithmetic with them. The hard
parts of running one at home are not the
arithmetic; they are the fiddly parts
around it: finding a model file in the
right format, picking the build that suits
your processor or graphics card, loading
it with sensible settings, and talking to
it in the dialect it expects. Ollama
bundles all of that into one program:

- A **library** of ready-made models you
  pull by name, the way you pull a
  container image (chapter 5).
- A **server** that keeps models loaded
  and answers requests, from the command
  line or over HTTP (chapters 2 and 8).
- A **Modelfile** format for packaging
  your own variations — a model plus
  instructions plus settings — under your
  own name (chapter 6).

What it does not do is train models, and
it does not make small models cleverer
than they are. A model that fits on a
laptop is a fraction of the size of the
ones behind the big chat services, and
chapter 14 is honest about the gap.

## Who it is for

Ollama suits anyone who wants a language
model within reach of their own machines:
the home-lab keeper who wants summaries
and answers without sending family notes
to a cloud; the developer who wants a
local model to test against, offline, for
free; the writer who wants a tireless
first-draft machine on a train with no
signal. The common thread is one machine,
honestly run — the same ground the
companion survey, *Local AI at Home for
Mere Mortals*, walks across all the local
tools. This guide is the deep dive on
this one tool.

## What it is not

Ollama is not a chat service: there is no
account, no conversation history kept for
you, no memory between sessions unless a
program you build keeps it. It is not a
security boundary: the server has no
logins, and chapter 10 says plainly what
that means before you let other machines
talk to it. And it is not magic about
hardware: a model needs memory to sit in
and arithmetic to run, and a bigger model
on a small machine is a slow model on a
small machine (chapter 7).

Check: after installing (chapter 3),
`ollama run llama3.2:1b` downloads a small
model and opens a chat with it. Type
`/bye` to leave. The lab's transcripts of
exactly that are in
`ollama-lab/transcripts/`.
