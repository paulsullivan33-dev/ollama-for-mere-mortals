# The HTTP API

The command line is one client of the
server; the **HTTP API** is how every
other program — your scripts, a chat
front-end, a tool on another machine —
talks to it. It is a small, plain API
over JSON, and this chapter walks the
endpoints you will actually use. The
server from chapter 3 is assumed:
everything below goes to
`http://localhost:11434` unless you
point it elsewhere (chapter 10).

## Checking in: tags and version

```bash
curl http://localhost:11434/api/tags
```

lists every model in the store, with its
size, digest, and the vital statistics
from chapter 5's card, as JSON. It is
the API's `ollama list`, and the first
thing any program should call: if this
fails, the server is not there (chapter
15). `GET /` answers `Ollama is running`,
and `/api/version` names the version —
the health check trio.

## One question, one answer: /api/generate

Generate is the completion endpoint: a
prompt in, a reply out.

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2:1b",
  "prompt": "Why is the sky blue? Answer in one sentence.",
  "stream": false
}'
```

Two things to know. First, by default
the reply **streams**: a series of JSON
objects, one per token-chunk, which is
what a chat window renders as typing.
`"stream": false` waits and returns one
object — what scripts want. Second, that
one object carries the bookkeeping:
`prompt_eval_count` and `eval_count`
(tokens read and written) and their
durations in nanoseconds. Tokens per
second, honestly measured on your own
machine, is `eval_count` divided by
`eval_duration` — the lab's numbers are
in `ollama-lab/transcripts/t03-api.txt`.

## Conversations: /api/chat

Chat is generate with manners. Instead
of one prompt string you send the
conversation as a list of messages, and
the server applies the model's own chat
template (chapter 6) so the roles come
out right:

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.2:1b",
  "stream": false,
  "messages": [
    {"role": "user", "content": "Name three primary colours."}
  ]
}'
```

The reply comes back as a `message`
object with `role: assistant` and the
text in `content`. **The server keeps no
history**: your program sends the whole
conversation each time — system
instructions first, then the turns in
order — and appends each reply before
the next call. Every chat front-end you
have ever used works exactly like this,
and the context window (chapter 7) is
why old turns eventually fall off the
back of the list you send.

System instructions are just a message
with `role: "system"` at the front — the
API-level version of a Modelfile's
`SYSTEM` line, for programs rather than
packaged models.

## Asking for JSON, and for tools

Two options turn a prose machine into a
component:

- `"format": "json"` — or better, a full
  JSON schema in `format` — constrains
  the reply to match. Chapter 9 covers
  the idea; the API is where it is used.
- `"tools": [...]` — for models with the
  tools capability (chapter 5), you may
  describe functions the model may ask
  your program to run; its reply then
  contains a `tool_calls` list instead of
  an answer, and your program executes
  them and reports back. This is the
  machinery behind every "agent" demo,
  and it is exactly as powerful and as
  dangerous as it sounds: the model
  proposes, your code disposes.

## Numbers that describe text: /api/embed

Embedding models (chapter 5) turn text
into lists of numbers via `/api/embed`,
so programs can search by meaning rather
than by keyword. It returns promptly and
chattily not at all: embeddings are
ingredients for other programs
(retrieval over your notes is the
canonical home use), not a thing you
talk to.

## The OpenAI-compatible door

The server also speaks a second dialect:
endpoints under `/v1` (`/v1/chat/completions`,
`/v1/models`, `/v1/embeddings`) shaped
like the OpenAI API. Programs written
against that API — which is a great many
programs — can be pointed at your Ollama
server by changing their base URL to
`http://localhost:11434/v1` and using any
placeholder as the API key. This is the
cheapest interoperability in the whole
subject: one environment variable in the
other tool, and your local model stands
where a hosted one stood. Its limits are
the honest ones: not every OpenAI feature
exists here, and the models are your
models, with their size and their
ceiling (chapter 14).

## Errors are JSON too

Ask for a model you have not pulled and
the server says so, in JSON, with a 404.
Send malformed JSON and it says that
too. Programs should read the `error`
field rather than guess — and so should
you, in the terminal, before concluding
the server is down (chapter 15).
