# Recipes

Short, complete jobs, each doable with
what the earlier chapters built. Assume
Ollama installed (chapter 3) and adjust
model names to your shelf.

## 1. A second opinion that costs nothing

Keep two sizes of one family pulled and
ask both the same hard question
(chapter 4's habit, formalised):

```bash
ollama pull llama3.2:1b
ollama pull llama3.2:3b
ollama run llama3.2:1b "Explain overfitting to a gardener."
ollama run llama3.2:3b "Explain overfitting to a gardener."
```

Read the pair side by side. You have
learned the size ladder of that family
on *your* kind of question, which is the
only syllabus chapter 7's arithmetic
cannot give you.

## 2. The household editor

Build chapter 6's `plain-talker` (or your
own house style), then use it for the
jobs where posture matters more than
brilliance — meeting notes into plain
English, a draft email made polite:

```bash
ollama create plain-talker -f Modelfile
ollama run plain-talker "Rewrite this so a tired person can follow it: ..."
```

Bake the temperature low (0.2–0.3) into
the Modelfile: editing is a clerk's job,
not a jazz musician's (chapter 9).

## 3. A summariser in a script

The one-shot CLI form is a shell tool.
Summarise any text file, privately,
offline:

```bash
ollama run llama3.2:3b "Summarise in three bullet points:" < notes.txt
```

Programs that need the numbers as well
as the words should call the API instead
(chapter 8) and read `eval_count` from
the reply — that is how you learn what
your automation costs in time.

## 4. A private model server for the house

On the spare machine, set
`OLLAMA_HOST=0.0.0.0:11434` in the
service (chapter 10), pull one good
general model and one small fast one,
and point the household's tools at it:
the laptop client with
`OLLAMA_HOST=http://192.168.0.25:11434`,
a chat front-end (chapter 13's Open
WebUI) for the people who do not do
terminals. Read chapter 10's warning
again before you start: this recipe and
a router port-forward are *not* to be
combined.

## 5. JSON out of a small model

Via the API (chapter 8), ask the small
model to classify, not to converse:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2:1b",
  "prompt": "Classify the sentiment of: \"The train was late but the coffee was great.\"",
  "stream": false,
  "format": {"type": "object",
             "properties": {"sentiment": {"type": "string"}},
             "required": ["sentiment"]}
}'
```

What comes back is machine-readable —
the shape a program can build on. Half
of practical local AI is this recipe
wearing different clothes.

## 6. Pin a moment in the shelf

Before a big experiment — a new model, a
wild Modelfile — snapshot the known-good
one under a dated name:

```bash
ollama cp llama3.2:3b llama3.2:3b-keeper
```

It costs a manifest, not a download
(chapter 11), and un-experimenting is
then `ollama rm` plus a name change
rather than a re-pull.

## 7. The offline kit

For a machine that will lose its
network — a trip, a cabin, a cautious
network at work — prepare while you
have bandwidth:

```bash
ollama pull llama3.2:3b
ollama pull nomic-embed-text
cp -r ~/.ollama/models /path/to/usb/
```

Models, an embedding model for search
programs, and the store on a stick:
chapter 11's restore on the far end is
pointing the new server at the copied
store.
