# Ollama for Mere Mortals

A **large language model** is a file of
numbers — billions of them, learned from
text — plus a program that does arithmetic
with those numbers to predict what word
comes next, one word at a time, until an
answer exists. Running one used to mean a
data centre. **Ollama** is the open-source
tool that makes it mean a program on a
machine of yours: one command pulls a
model from a public library, loads it, and
chats with it in your terminal — and behind
that command sits a server with an HTTP
API that your own programs, scripts and
other machines can use. This guide is the
deep dive on that tool: how it is put
together, how to choose and read models,
how to package your own variations with a
**Modelfile**, how to talk to the API, how
to serve a whole household from one
machine, and where the honest limits are.
For the survey of local AI as a whole —
every tool, not just this one — read the
companion, *Local AI at Home for Mere
Mortals*, first or alongside.

The lab behind this guide ran the real
thing: **Ollama 0.40.11**, installed with
the official script onto a small Linux
machine (two processor cores, no graphics
card), and exercised end to end. A small
model (Meta's Llama 3.2 at one billion
parameters) was pulled from the library,
run in a chat and from the shell, measured
with its own timing numbers, questioned
through the HTTP API in both its dialects,
copied, and removed. A custom model was
built from a Modelfile written for the
guide — a plain-spoken editor — and run
against its parent. The server was run a
second time on a second port and driven
from a client pointed at it, which is the
whole mechanics of serving other machines,
with a shorter cable. The transcripts are
kept in `ollama-lab/transcripts/` and the
full ledger — what was run, what was read
from documentation, and the sandbox's own
limits — is in `criteria.md`.

What the lab could not do, it did not
fake. The lab machine has no graphics
card, so everything here ran on the
processor: the chapters on graphics
hardware describe what to check on yours,
and no speed measured on two CPU cores is
presented as what your machine will do.
Running the server as a boot service with
other real machines as clients is taught
from the documentation and the one-machine
demonstration, and labelled as such.
Pushing a model to the library needs an
account and is likewise documented only.

## What this guide covers

- [**What Ollama is**](what-ollama-is.md) —
  the model: a file of numbers, a server,
  a library, a command; what a local model
  is for and what it is not
- [**The moving parts**](the-moving-parts.md) —
  the server, the command line, the
  runner, the store and the library: what
  each holds, and what breaks when each
  stops
- [**Installing Ollama**](installing-ollama.md) —
  the one-command install, the apps for
  macOS and Windows, and the one decision:
  who starts the server
- [**Your first model**](your-first-model.md) —
  pull, chat, one-shot answers from the
  shell, and `list`, `ps`, `show` while it
  runs
- [**Models and the library**](models-and-the-library.md) —
  names and tags, reading the model's
  card, quantisation labels and their
  sizes, and the kinds of model beyond
  chat
- [**Modelfiles and custom
  models**](modelfiles-and-custom-models.md) —
  the recipe format: `FROM`, `SYSTEM`,
  `PARAMETER`, `TEMPLATE`, `ADAPTER`, and
  building a variation of your own
- [**Disk, memory and
  context**](disk-memory-and-context.md) —
  the three scarcities: the store on disk,
  the working set in memory, and the
  context window, each with its arithmetic
- [**The HTTP API**](the-http-api.md) —
  generate, chat, embeddings, JSON on
  demand, tools, and the
  OpenAI-compatible door
- [**Generation settings**](generation-settings.md) —
  temperature, context size, stop
  conditions, repeats, seeds and thinking:
  the steering wheel
- [**Running as a service**](running-as-a-service.md) —
  the server as a household utility:
  starting at boot, serving other
  machines, tuning keep-alive and
  parallelism — and the warning about
  authentication that comes with it
- [**Moving and backing up
  models**](moving-and-backing-up.md) —
  the store as a folder, Modelfiles as
  the artefact that matters, and
  publishing with `ollama push`
- [**Recipes**](recipes.md) — a second
  opinion, a household editor, a script
  summariser, JSON from a small model,
  and the offline kit
- [**The neighbours**](the-neighbours.md) —
  llama.cpp direct, LM Studio, Open
  WebUI, GPT4All and Jan, vLLM, and the
  hosted giants: what each one trades
- [**Limits**](limits.md) — no
  authentication, the model's ceiling is
  the ceiling, one machine's queue, the
  hard wall of memory, stated plainly
- [**Troubleshooting**](troubleshooting.md) —
  the server that is not there, the pull
  that stalls, the model that vanished
  into the other store, the reply that
  crawls, forgets, or stops mid-sentence
- [**Glossary**](glossary.md) — the terms,
  collected in one place

## What it doesn't cover

Training or fine-tuning a model — the
guide applies adapters other people
trained (chapter 6) and points at the
subject, but the training itself is a
book of its own. Building retrieval
systems over your documents (the
embeddings in chapter 8 are the
ingredient, not the dish), running models
for many strangers at scale (chapter 13
names the tools), and prompt-engineering
as a discipline — the guide teaches the
settings and the system prompt as
machinery, not as persuasion. Safety
research, model evaluation as a science,
and the politics of training data are
likewise pointed at, not taught.

## How to use this guide

Read chapter 1 for the idea and chapter 2
before installing anything — knowing
which part holds what turns later
confusion into a named problem. Chapters
3 and 4 get a model answering in one
sitting. Chapter 5 is the one to read
before your second download: the label
on a model is the whole decision.
Chapter 7 is the other one not to skip —
its arithmetic is the difference between
a model that delights and a download that
crawls. If you came to build something,
chapters 6, 8 and 9 are the workbench;
if you came to serve a household, chapter
10, warning included. The lab's
transcripts live in
`ollama-lab/transcripts/`; its notes are
`examples/lab-notes.txt`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [llama.cpp for Mere Mortals](https://paulsullivan33-dev.github.io/llama-cpp-for-mere-mortals/)
- [Open WebUI for Mere Mortals](https://paulsullivan33-dev.github.io/open-webui-for-mere-mortals/)
- [Local AI at Home for Mere Mortals](https://paulsullivan33-dev.github.io/local-ai-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/ollama-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/ollama-for-mere-mortals/blob/main/LICENSE-CODE).
