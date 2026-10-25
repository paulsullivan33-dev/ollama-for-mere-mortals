# Models and the Library

A model's name in Ollama is an address,
not a description: `llama3.2:1b` says
*which family* and *which size*, and
behind that short string sit a file
format, a quantisation, a context length
and a licence that all matter. This
chapter teaches you to read the label
before you drink.

## Names and tags

Model names follow the pattern
`name:tag`, like container images (and
for the same reason — Ollama's machinery
borrowed the shape on purpose). The name
is the family: `llama3.2`, `qwen3`,
`gemma3`, `phi4`, `mistral`. The tag
usually names the size — `1b`, `3b`,
`7b`, `8b` — where the number is the
parameter count in billions. Leave the
tag off and you get `:latest`, which is
whatever size the publisher chose as the
default; name the size yourself, always,
so the thing you pulled last month is
the thing you think it is.

Tags can also name variants:
instruction-tuned versions (the default
for chat families), base models, and
different quantisations of the same size
— `llama3.2:1b-instruct-q8_0` is the same
1B model stored at higher precision than
the plain `1b` tag.

## Where to browse

The library lives at
[ollama.com/library](https://ollama.com/library).
Each model's page lists its sizes with
their download weights, the context
length it supports, what it is for (text,
vision, tools, reasoning), and the exact
`ollama run` command. There is much more
in the world than the library — the
deep end is Hugging Face, where models
are published as raw GGUF files — and
chapter 6 shows how to bring one of
those home with a Modelfile. But the
library is where to start: everything in
it is packaged, tagged and checksummed
for the tool you are holding.

## Reading the card: ollama show

Before you trust a model with anything,
look at its card:

```bash
ollama show llama3.2:1b
ollama show --modelfile llama3.2:1b
```

The first prints the model's vital
statistics: architecture, parameter
count, context length, embedding size,
and quantisation. The second prints the
Modelfile it was built from (chapter 6):
the system prompt it carries, its default
settings, the chat template it expects.
A model that behaves oddly is very often
a model carrying instructions you had
not read; the card is where you read
them.

## Quantisation: the label inside the label

Almost no model in the library is stored
at full precision. They are **quantised**:
each weight kept at fewer bits, so the
file is smaller and the arithmetic
faster, at a cost in quality that ranges
from "unmeasurable" to "noticeably
dumber." The labels follow llama.cpp's
convention:

| Label | Rough size, per billion parameters | Honest summary |
|-------|-----------------------------------|----------------|
| Q8_0 | ~1.1 GB | Nearly the original; for small models that must not lose any quality they have |
| Q6_K | ~0.8 GB | Excellent; a good ceiling when memory allows |
| Q5_K_M | ~0.7 GB | Small loss, rarely visible |
| Q4_K_M | ~0.6 GB | The default for most library tags, and the right default for you |
| Q3_K / Q2_K | ~0.4 / ~0.35 GB | For squeezing a big model onto a small machine; expect the seams to show |
| FP16 | ~2 GB | The unquantised original; mostly of interest as the thing quants are made from |

Two practical consequences. First, size
on disk is roughly *parameters × the
number in the middle column* — a 7B model
at Q4_K_M is about 4.3 GB, and you can do
this arithmetic on any library page
before downloading (chapter 7 needs it
again for memory). Second, the same name
at a smaller quant is not the same model
in any way that matters for hard work:
when a task matters, reach for the bigger
or the less-quantised copy, in that
order.

## Kinds of model

The library holds more than chat models,
and `ollama show` tells you which kind
you have under **capabilities**:

- **Completion/chat** models — the
  default kind, everything in chapter 4.
- **Embedding** models (`nomic-embed-text`,
  `bge-m3` and friends) produce lists of
  numbers describing text, for search and
  retrieval programs. They do not chat;
  trying to run one conversationally is
  a chapter 15 classic.
- **Vision** models accept images along
  with text.
- **Tools** models are tuned to call
  functions through the API (chapter 8).

## Provenance, briefly

A library model's page names its
publisher and its licence. Models are
trained by companies and communities —
Meta for Llama, Alibaba for Qwen, Google
for Gemma, and many independent
fine-tunes of all of them — and the
licence for *your* use is theirs, not
Ollama's (chapter 14). For home use
nearly everything in the library is free
to run; for anything beyond, read the
card. The digest in `ollama show` and
the manifest in the store mean the file
you run is bit-for-bit the file that was
published — chapter 11 uses that when
moving models between machines.
