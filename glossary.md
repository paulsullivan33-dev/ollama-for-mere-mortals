# Glossary

**Adapter** — a small file of adjustments
(usually from a LoRA fine-tune) layered
over a base model's weights via a
Modelfile's `ADAPTER` instruction.

**Blob** — one of the large files in the
model store, named by its checksum. Model
weights live in blobs; several models can
share one.

**Chat template** — the exact text format
a model was trained to converse in: the
markers wrapping system, user and
assistant turns. Carried by the Modelfile's
`TEMPLATE`.

**Context window** — how much text, in
tokens, the model can hold in view at
once: conversation, documents, and the
reply being written. Sized by `num_ctx`.

**Embedding** — a list of numbers produced
by an embedding model to represent a
text's meaning, so programs can search by
similarity rather than keywords.

**Eval rate** — tokens generated per
second, computed from an API reply's
`eval_count ÷ eval_duration`. The honest
speed of your machine, on your model.

**GGUF** — the model file format Ollama's
runner uses. Most open models are
published as GGUF files (notably on
Hugging Face) and can be imported with a
Modelfile.

**Keep-alive** — how long the server
keeps a model loaded after its last
request before unloading it. Default five
minutes; set by `OLLAMA_KEEP_ALIVE` or
per request.

**Library** — Ollama's public model
registry at ollama.com, from which
`ollama pull` downloads prepared models.

**Manifest** — the small record in the
store that says which blobs make up a
named model, and with what settings.

**Model** — a packaged set of weights
plus its configuration, referred to by a
name and tag such as `llama3.2:1b`.

**Modelfile** — the recipe a custom model
is built from: `FROM`, `SYSTEM`,
`PARAMETER`, `TEMPLATE`, `ADAPTER`. Read
by `ollama create`.

**OLLAMA_HOST** — the environment
variable that, on a client, says where
the server lives, and on the server, says
which address it listens on.

**OLLAMA_MODELS** — the environment
variable that moves the model store to a
different folder.

**Parameters (of a model)** — the
weights' count, the number in the tag:
`7b` means roughly seven billion. The
first predictor of a model's size, speed
and capability.

**Parameters (generation)** — the
settings that steer generation —
temperature, `num_ctx`, and friends —
confusingly the same word as the weights'
count. Context disambiguates; chapter 9
is the steering kind.

**Prompt eval** — the phase in which the
runner reads your prompt and conversation
before generating; measured separately
from generation in API timings.

**Pull** — download a model from the
library into the store: `ollama pull`.

**Quantisation** — storing weights at
reduced precision (Q4_K_M, Q8_0, ...) so
models are smaller and faster, at some
cost in quality. Chapter 5's table.

**Runner** — the per-model process,
built on llama.cpp, that does the
generation arithmetic for the server.

**Server** — the background program
(`ollama serve`, port 11434 by default)
that loads models and answers the CLI
and the API. It has no authentication.

**Store** — the folder of manifests and
blobs holding every model you have:
`~/.ollama/models` for a personal
install.

**Stream** — returning a reply token by
token as it is generated, as the API
does by default, instead of all at once.

**System prompt** — the standing
instructions a conversation starts with,
set by a Modelfile's `SYSTEM`, a chat's
first message, or a model publisher.

**Tag** — the part of a model name after
the colon: the size or variant, such as
`1b` or `8b-instruct-q8_0`. Untagged
means `:latest`.

**Thinking** — a supported model's mode
of producing visible reasoning before
its answer, at the cost of time and
tokens. Chapter 9.

**Token** — the unit models read and
write: roughly three-quarters of an
English word, or a piece of one.
Prompts, replies, contexts and prices of
attention are all counted in tokens.

**Weights** — the model's numbers: the
trained file itself, as distinct from
the program that runs it or the text it
produces.
