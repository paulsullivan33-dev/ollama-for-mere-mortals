# The Neighbours

Ollama is one way to run models at home,
not the only one, and not always the right
one. These are the tools it lives next
to, and what each trades. The companion
survey *Local AI at Home for Mere
Mortals* covers the wider landscape; this
chapter is just the fence lines.

## llama.cpp, directly

Under Ollama's runner sits **llama.cpp**,
the open-source engine that made running
GGUF models on ordinary hardware
practical. You can run it yourself: its
`llama-cli` chats with a model file and
its `llama-server` serves an API of its
own. What you gain is control — every
knob, every new feature the month it
lands, no library between you and the
file. What you lose is exactly what
Ollama exists to provide: you fetch and
organise model files yourself, choose the
build for your hardware yourself, and
write your own service if you want one.
Many people start with Ollama and keep a
llama.cpp checkout for experiments; the
deep dive on that side of the fence is
*llama.cpp for Mere Mortals*.

## LM Studio

**LM Studio** is the polished desktop
app: browse models in a window, click to
download, chat in a window, serve an API
with a switch. For a reader who wants
local models without a command line at
all, it is the friendliest front door,
and its model browser is genuinely good
at surfacing what will fit your machine.
The trades: it is a closed-source app, it
is one desktop rather than a service
other machines and programs naturally
build on, and scripting it is thinner
than Ollama's API. Households often end
up with LM Studio on the laptop and
Ollama on the server, which is not a
contradiction.

## Open WebUI

**Open WebUI** is not a model runner; it
is a chat **front-end** — the web page
with conversation history, user accounts
and document uploads that Ollama itself
does not provide — that talks to Ollama's
API (among others). If what you miss
about the big chat services is the
interface rather than the model, this is
the standard answer, and it is the
subject of its own guide in this series,
*Open WebUI for Mere Mortals*.

## GPT4All and Jan

Two more desktop apps in LM Studio's
neighbourhood. **GPT4All** aims at total
beginners and adds a local-documents
feature; **Jan** is an open-source
desktop app with a similar shape and an
eye on extensions. Both are reasonable
doors into local models for someone who
never wants to see a terminal; neither
grows into a household server the way
Ollama does.

## vLLM and the serving end

At the other end of the scale, **vLLM**
(and its kin) serve models to *many*
users at once on serious graphics
hardware, with batching tricks that trade
a lone user's latency for a crowd's
throughput. If your problem is "fifty
students at 9 a.m.," that is the shelf to
shop on — at the price of NVIDIA-first
hardware expectations, no friendly
library, and considerably more moving
parts. Nothing about it is a criticism of
Ollama: they are answers to different
questions.

## The hosted services, for calibration

And a word for the thing in the cloud:
the big chat services run models far
larger than any home machine in this
guide can hold, remember your
conversations for you, and cost a
subscription and your data's travel.
Nothing in this chapter — or guide —
pretends a 7B model at home replaces
them at their best. What the home machine
offers instead is privacy, offline use,
no meter running, and a thing that is
*yours* to inspect and script. Many
households run both, deliberately:
Ollama for the everyday and the private,
the giants for the genuinely hard.
