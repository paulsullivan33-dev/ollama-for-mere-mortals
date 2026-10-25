# Modelfiles and Custom Models

Sooner or later you want the same model
with different manners: the coding model
that always answers tersely, the family
model that never uses jargon, the small
model that always formats its answer as a
list. Ollama's answer is the **Modelfile**
— a short recipe that starts from an
existing model and packages new
instructions and settings under a new
name. If you have built a container
image, the shape will feel familiar:
`FROM`, instructions, a build command,
a tag.

## A first Modelfile

The lab's example, kept in
`ollama-lab/modelfile/` — a general model
re-packaged as a plain-spoken editor:

```dockerfile
FROM llama3.2:1b

PARAMETER temperature 0.3

SYSTEM """
You are a plain-spoken editor. You answer
in short, clear sentences. You explain any
technical word the first time you use it.
When you are unsure, you say so instead of
guessing.
"""
```

Build it and use it:

```bash
ollama create plain-talker -f Modelfile
ollama run plain-talker
```

`create` reads the recipe, assembles a
new manifest in the store, and names it.
Almost nothing is copied: the new model
points at the same weight blobs as its
parent and adds a small layer of its own,
which is why creating a variation is
nearly instant and costs nearly no disk
(chapter 7's shared blobs). `ollama list`
now shows both, and `ollama show
plain-talker` shows your instructions on
its card. The lab's transcript — build,
run, and the parent's card for
comparison — is
`ollama-lab/transcripts/t04-modelfile.txt`.

## The instructions that matter

- **`FROM`** — the base. Another model
  name (`llama3.2:1b`), one of your own
  custom models (variations on
  variations), or — the door to the wider
  world — a **GGUF file on disk**:
  `FROM ./my-model.gguf` builds a model
  from a file you downloaded from
  Hugging Face or produced with another
  tool. This is how the long tail of
  community models gets in.
- **`SYSTEM`** — the standing
  instructions the model starts every
  conversation with. Behaviour lives
  here: role, tone, rules, the audience
  to assume. Chapter 9's caution applies:
  a system prompt steers, it does not
  guarantee.
- **`PARAMETER`** — the generation
  settings from chapter 9, baked in:
  `temperature`, `num_ctx`, `num_predict`,
  `stop`, `seed`, `repeat_penalty`, and
  the rest. A model with its settings
  baked in is a tool with its handle
  fitted: nobody has to remember the
  numbers at the command line.
- **`TEMPLATE`** — the exact text format
  the model was trained to converse in:
  the markers that wrap system, user and
  assistant turns. Library models carry
  the right one. You need this when
  importing a raw GGUF whose template
  Ollama cannot know, and changing the
  template of a model that already works
  is a reliable way to make it strange —
  chapter 15 has the symptoms.
- **`ADAPTER`** — a LoRA adapter file: a
  small set of adjustments layered on a
  base model, the usual output of
  fine-tuning a model on your own
  examples. Training adapters is beyond
  this guide's scope; applying one is a
  line in a Modelfile.
- **`LICENSE`** and **`MESSAGE`** — the
  licence text to carry along, and
  example exchanges that prime the
  model's style.

## Habits that keep this sane

**Name your models like you mean it.**
`plain-talker` in the store is a promise
about behaviour; a shelf of `test`,
`test2`, `final-real` is a shelf of
mysteries. `ollama cp old new` renames
by copying the manifest; `ollama rm`
cleans up the experiments that did not
earn a name.

**Keep the Modelfile.** The recipe is
the model, the way a Containerfile is
the image: small, readable, versionable.
Keep it in a notes folder or a git
repository and `create` becomes
reproducible; lose it, and `ollama show
--modelfile` can print back what a model
was built from — for models you built,
that is your backup of the recipe.

**Do not expect a new model.** A
Modelfile does not add knowledge or
capability; it aims what is already
there. The editor above is llama3.2:1b
with posture and settings; it will make
the same factual mistakes as its parent,
more politely. When a variation
disappoints, the fix is almost never a
cleverer system prompt — it is a bigger
or better-suited base (chapter 5), or
the honest acceptance of chapter 14's
ceiling.
