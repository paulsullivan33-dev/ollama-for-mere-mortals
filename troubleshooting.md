# Troubleshooting

Symptoms, in the order people meet them,
each with the check that names the
problem. The discipline throughout:
decide *which part* is failing (chapter
2) before fixing anything.

## "Could not connect" / the CLI cannot find the server

The client is healthy; it has nobody to
talk to. `curl http://localhost:11434/`
fails the same way — the server is not
running, or not where the client thinks
it is.

- Linux service: `systemctl status
  ollama`; start with `sudo systemctl
  start ollama`. Hand-run: open a window,
  `ollama serve`, watch it start.
- macOS/Windows: is the app running? Its
  icon should be in the menu bar or tray.
- Client pointed elsewhere: an
  `OLLAMA_HOST` left over in your shell
  (from chapter 10's experiments) sends
  every command to a server that is off,
  or someone else's. `echo $OLLAMA_HOST`
  should usually be empty.

## The download stalls or the pull fails

`ollama pull` names its failing layer if
you read the error: DNS, TLS, or a
connection reset are network stories, not
Ollama stories. Check another HTTPS site
from the same machine; try the pull
again (it resumes where it left off —
the blobs in the store are kept);
on a managed network, expect a proxy to
have opinions about a 4 GB file. A model
that "pulled" but will not run is more
often an interrupted download than a bad
model: `ollama rm` it and pull again.

## "Model not found" for a model you can see

Two stores is the classic (chapter 3):
the service's store
(`/usr/share/ollama/.ollama/models`) and
your personal one (`~/.ollama/models`)
are different shelves. `ollama list`
asked the *server* — whose shelf can
differ from the one a hand-run `ollama
serve` in another window is showing.
Decide which server is yours (chapter
10) and use one. Second classic: the
tag — `llama3.2` and `llama3.2:1b` are
different addresses; `ollama list`
prints the exact names you own.

## The reply crawls

Read the timings before blaming the
model: `ollama run --verbose` or an API
reply's numbers (chapter 8).

- **Slow to start, fine once going:**
  loading, plus prompt eval of a long
  conversation or document (chapter 7).
  Expected, not a fault.
- **Uniformly slow generation:**
  arithmetic. Check `ollama ps` — is
  the model split between GPU and CPU,
  or on CPU entirely? Check the size
  against the machine (chapter 7's
  table): a model that only fits with
  swapping will crawl in a way no
  setting fixes.
- **Fast yesterday, slow today:** a
  second model is loaded and sharing, a
  long context is being re-read, or
  something else owns the machine. `ps`
  and the server log (`journalctl -u
  ollama`, or the serve window) tell
  which.

## The model forgot the beginning

Context window, chapter 7 and chapter 9:
the conversation outgrew `num_ctx` and
the oldest part slid out of view. Not a
bug and not fixable by repeating
yourself louder. Raise `num_ctx` (at a
memory price), summarise the
conversation into a fresh one, or split
the job.

## The model is suddenly strange

Changed behaviour with no model change
is a settings change until proven
otherwise. The suspects, in order:
temperature raised by a `/set` or an
API `options` block (chapter 9); a
system prompt you forgot a program was
sending (chapter 8: programs send their
own); a custom model whose Modelfile
grew a `TEMPLATE` edit (chapter 6);
or `:latest` quietly moving to a new
default size (chapter 5). `ollama show`
and `ollama show --modelfile` display
what is actually in force.

## "Requires more system memory" / the load fails

The arithmetic of chapter 7, enforced:
the model plus its context does not fit
in the memory available to the runner.
Free memory (unload other models with
`ollama stop`, close the machine's other
tenants), pick a smaller quantisation,
or shrink `num_ctx` — in that order of
regret.

## Answers cut off mid-sentence

A `num_predict` cap somewhere (chapter
9): in the Modelfile, in the API
request, or in the front-end you are
using. Raise it or remove it; the
finish reason in an API reply
(`"done_reason": "length"`) names this
one outright when it happens.

## It answers like a different product entirely

You are probably not talking to the
model you think: an embedding model
being chatted with (chapter 5 — it
returns vectors, not conversation), a
base model with no instruction tuning
(it continues text rather than answering
questions), or a front-end silently
pointed at a hosted API instead of your
server. `ollama ps` during the reply
shows whose desk the work is on.

## When all else fails

The server log is the witness that was
actually there: `journalctl -u ollama`
on Linux, the serve window for a hand-run
server. It records which hardware the
runner claimed, each model load and
unload, and the real text of errors the
CLI summarised. Read the lines around
the time of the failure before changing
anything — half of this chapter is what
the log would have told you first.
