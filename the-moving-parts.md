# The Moving Parts

Ollama looks like one program and is
really four, arranged so that each can be
understood on its own. When something
misbehaves, naming which part you are
talking to turns confusion into a named
problem.

## The server

Everything goes through a background
service, the **Ollama server**, started
with `ollama serve` (or by your system at
boot, chapter 10). It listens on a network
port — `127.0.0.1:11434` by default, this
machine only — and it is the part that
actually loads models and runs them. It
keeps a loaded model in memory for a few
minutes after the last request, so the
second question is faster than the first,
and it unloads models when memory gets
tight or their time expires.

Check: with the server running,

```bash
curl http://localhost:11434/
```

answers `Ollama is running`. If it does
not, nothing else in this guide will work
either — the server is the ground floor.

## The command line

The `ollama` command you type — `run`,
`pull`, `list`, `ps` — is a **client**. It
does no arithmetic itself; it packages
your request, sends it to the server over
HTTP, and prints what comes back. That is
why `ollama ps` can show a model grinding
away in an interactive session you opened
in another window: both windows are
talking to the same server.

This split has a practical consequence:
the client does not have to sit on the
same machine as the server. Point the
`OLLAMA_HOST` environment variable at
another machine's address and your
laptop's command line drives the bigger
machine's models (chapter 10).

## The runner

When the server loads a model, the actual
generation — turning your text into
tokens, doing the arithmetic, producing
the reply — happens in a **runner**
process built on llama.cpp, the
long-standing open-source engine for
running models in the GGUF file format.
Ollama starts one runner per loaded model,
hands it the model file and your settings,
and collects the output. You rarely talk
to the runner directly, but its existence
explains two things: why models are
distributed as GGUF files (chapter 5), and
why the neighbours in chapter 13 — tools
that also stand on llama.cpp — behave so
similarly.

## The model store

Downloaded models live in a **store** on
disk: on Linux, `~/.ollama/models` for a
personal install (the system service uses
a service account's home instead). Inside,
the store keeps big shared **blobs** —
the weight files, named by their
checksums — and small **manifests** that
say which blobs make up the model called
`llama3.2:1b`. Two models that share most
of their weights store those weights once.
Chapter 7 covers what this means for disk
space, and chapter 11 for moving the
store to another machine.

## The library

The server's models come, by default, from
the **Ollama library** at ollama.com: a
public registry of models prepared for
Ollama, each with a name, a set of sizes,
and tags for variations. `ollama pull`
asks the library for a model's manifest
and downloads the blobs it names. You can
also build models from files you already
have (chapter 6) and, with an account,
push your own to the library (chapter 11,
documented only).

## The shape of a request

Put together, one `ollama run` looks like
this: the client asks the server for the
model by name; the server checks the
store, pulls anything missing from the
library, starts a runner with the model
loaded, and streams the reply back token
by token; the client prints the tokens as
they arrive; when you go quiet, the server
keeps the model warm for a while in case
you come back. Every layer in that sentence
is a place a failure can live — and every
one of them can be checked on its own,
which is what chapter 15 is for.
