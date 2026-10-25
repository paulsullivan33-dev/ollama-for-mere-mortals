# Moving and Backing Up Models

Models are files, and files can be moved,
backed up and lost. This chapter is about
the honest ways to carry a model shelf
from one machine to another — and about
the one artefact that matters more than
the weights: your Modelfiles.

## What a backup actually needs

A model in the store is a manifest plus
blobs (chapter 2). The manifest is small
and names the blobs by checksum; the
blobs are the gigabytes. Backing up the
whole store is therefore exactly backing
up one folder — `~/.ollama/models`, or
the service's store, or wherever
`OLLAMA_MODELS` points:

```bash
du -sh ~/.ollama/models
```

Copy that folder anywhere — external
disk, file server, another machine's
equivalent folder — and the receiving
server, pointed at the same place, has
your models with no downloading. The
checksums make this safe to verify: a
blob is named by its digest, so a
corrupted copy is a detectable copy, not
a subtly wrong model.

Stop the receiving server while you lay
the store down, or don't — a running
server notices new manifests on its next
request in practice, but a restart is
the tidy guarantee.

## The lazy way: just pull again

For models that came from the library,
the store is a cache of things the
internet has. A new machine needs no
backup at all: install Ollama, and
`ollama pull` the same names. What a
backup really protects is:

- **Your custom models** — whose base
  can be re-pulled but whose recipes must
  not be lost (below).
- **Imported GGUF files** you obtained
  once from somewhere slow or
  soon-to-vanish.
- **Time and bandwidth** — a shelf of
  models is tens of gigabytes of
  somebody's download cap.

## The artefact that matters: Modelfiles

A custom model can be rebuilt from two
things: its base (re-pullable) and its
Modelfile (a page of text). So the
smallest serious backup in this whole
guide is a folder of Modelfiles — keep
them in your notes, your dotfiles, your
git repository. Worst case, `ollama show
--modelfile <name>` prints the recipe a
model was built from, so even a model
whose recipe you lost can give it back
while the model itself still exists. The
lab's backup drill is exactly this:
print the custom model's Modelfile, and
the record lives in
`ollama-lab/transcripts/t06-housekeeping.txt`.

Tagging helps a backup mean something:
`ollama cp llama3.2:1b llama3.2:1b-2026`
before an experiment leaves the shelf as
it was if the experiment goes wrong —
a copy costs a manifest, not gigabytes,
because the blobs are shared.

## Sharing a model with the world (or team)

`ollama push <your-name>/<model>` uploads
a model to the Ollama library under your
account, after `ollama signin`. It is how
custom models travel between your own
machines without carrying the store
around, and how community models reach
the library in the first place. Pushing
is documented here, not run: it needs an
account, and a model pushed is a model
published — read the library's terms
and your base model's licence (chapter
5) before treating it as a backup
mechanism for anything private.

## Moving the conversation, not the model

Note what does *not* travel with any of
this: conversations. The server keeps no
history (chapter 8), so there is nothing
to back up — whatever front-end or
script you chat through owns the
transcripts, and its backup story is
its own. Models are replaceable; your
Modelfiles and your front-end's data are
the irreplaceable parts. Back up
accordingly.
