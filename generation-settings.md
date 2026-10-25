# Generation Settings

A model by itself is only half of what
answers you. The other half is a handful
of **generation settings** — numbers that
steer how the runner picks the next word.
They decide whether your model is a
careful clerk or a jazz musician, how much
of the conversation it can see, and when
it stops. Every setting can be given in a
Modelfile (chapter 6), per request in the
API (chapter 8), or for one chat with the
`/set` command.

## The steering wheel: temperature

**Temperature** (default 0.8) controls how
much the model gambles. At each step the
model has a ranked list of plausible next
words; temperature says how far down the
list it may wander.

- **Low (0–0.3):** nearly always the top
  choice. Repetitive, predictable, and
  right for extraction, classification,
  code, and anything you will check
  mechanically.
- **Middle (0.7–0.9):** the everyday chat
  range the defaults live in.
- **High (1.2+):** long shots get taken.
  Brainstorming gets stranger and more
  varied — and so do the facts.

There is no correct value, only a correct
value *for the job*: the same model at
0.1 and at 1.3 is two different tools.

In the CLI, set it for a session:

```text
>>> /set parameter temperature 0.2
```

or bake it into a custom model with a
Modelfile `PARAMETER temperature 0.2`
(chapter 6).

## How much it can see: num_ctx

**num_ctx** is the size of the **context
window** the runner sets aside: your
conversation (or document) plus the reply,
measured in tokens. Ollama's default is
modest — 4096 tokens in current releases —
even for models that support far more. If
a conversation outgrows the window, the
oldest part is silently dropped from what
the model sees; nothing warns you except
the model forgetting the beginning.

Raising `num_ctx` costs memory and time:
the attention cache grows with every
token, and the model re-reads the whole
window for each reply (chapter 7 does the
arithmetic). Raise it when you are
actually feeding long documents; leave it
when you are not.

## When it stops: num_predict and stop

- **num_predict** caps how many tokens a
  reply may contain. The default leaves it
  effectively open-ended (the model stops
  when it finishes). A cap is a safety
  rail against a runaway reply, and a
  foot-gun when it slices an answer in
  half mid-sentence — both outcomes are
  in chapter 15.
- **stop** lists strings that end the
  reply when produced. Models carry their
  own defaults (visible in `ollama show`);
  chat programs add stops for the role
  markers of the conversation format.

## Repeat after me not: repeat_penalty

**repeat_penalty** (default 1.1) nudges
the model away from words it has already
used, which prevents the looping that
small models fall into — saying the same
sentence with growing confidence. Too high
and the prose gets evasive, dodging
ordinary words like "the" near-misses.
The default is a good citizen; touch it
when you see actual loops.

## Determinism, honestly: seed

Set a **seed** and the same prompt with
the same settings produces the same reply,
which is essential for tests and
evaluations — you cannot measure a change
you cannot reproduce. It does not make
the reply *good*, only repeatable, and it
only holds on the same model, version and
settings. Evaluators care; chatters don't.

## Structured answers: format

Through the API you can demand the reply
be **JSON** — either the plain `json`
mode or a full JSON schema the output
must match (chapter 8 shows both). This
is how programs get machine-readable
answers out of a prose machine, and how
a small model becomes a usable part in a
larger system: ask for
`{"sentiment": "positive"}` instead of a
paragraph you then have to parse.

## Thinking, out loud or not

Some recent models can **think** — produce
a stretch of reasoning before the answer.
On supported models `ollama run --think`
(or the API's `think` option) turns it
on, and the CLI can hide or show the
reasoning. Thinking costs time and tokens
and earns its keep on maths, logic and
code; for a summary it is mostly latency.
Whether a model supports it is on its
library page and in `ollama show`.

## A working rule

Change one setting at a time, on the
same prompt, and read the difference.
Generation settings are a steering wheel
with no markings; the only way to learn
what 0.2 of temperature feels like on
*your* model is to drive it. The lab did
the smallest version of this in
`ollama-lab/transcripts/t04-modelfile.txt`:
the same question put to a model and to
its own low-temperature variation.
