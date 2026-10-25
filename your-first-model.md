# Your First Model

Time to put a model on the server and talk
to it. This chapter uses a small one on
purpose: `llama3.2:1b`, about 1.3 GB to
download, quick to load, honest about its
limits. Everything here works identically
for a model ten times its size — it just
takes longer and uses more memory
(chapter 7).

## Pull, then run

Two commands do the whole job:

```bash
ollama pull llama3.2:1b
ollama run llama3.2:1b
```

The first downloads the model into the
store and stops. The second loads it into
memory and opens a chat: type a question,
press Enter, watch the answer arrive
token by token. Ask it something it can
actually do — summarise a paragraph you
paste, draft a polite email, explain a
word — rather than quizzing it like a
search engine (chapter 14 explains why).

Actually, you can skip the first command:
`ollama run` pulls anything missing
before it runs. Pulling separately is
still a good habit — it makes the slow
part (downloading) explicit instead of
hiding it inside your first question's
wait.

Check: while the chat is open, the prompt
looks like `>>>`. Leave with `/bye`.
The lab's transcript of a pull and a
first run is
`ollama-lab/transcripts/t01-pull-and-run.txt`.

## Talking from the command line

A chat window is not the only way in. Give
`run` a prompt as arguments and it prints
the answer and exits — the form scripts
want:

```bash
ollama run llama3.2:1b "Write a haiku about a server room."
```

Add `--verbose` and it also prints the
timings: how long loading took, how many
tokens the prompt and the answer used,
and the generation speed in tokens per
second. Those numbers are the honest
measure of your machine (chapter 7).

For a longer conversation in a script,
`/api/chat` keeps the history for you to
send each time — chapter 8. The CLI chat
keeps its own history only while it runs.

## While it runs: ps, list, show

Open a second terminal while a model is
loaded:

```bash
ollama list   # what is in the store
ollama ps     # what is loaded right now
ollama show llama3.2:1b
```

`list` is the shelf: every model you have
downloaded, with its size on disk. `ps` is
the desk: models currently in memory, how
much room they take, and when the server
will put them away. (Leave a model alone
for about five minutes and it unloads
itself; `ollama stop llama3.2:1b` puts it
away now.) `show` opens the model's card:
size in parameters, quantisation, context
length, its default settings, and the
system prompt it carries, if any. Chapter
5 teaches you to read that card properly.

Check: after `/bye`, `ollama ps` may still
list the model — that is the warm cache
from chapter 2, not a leak. `ollama stop`
followed by `ollama ps` shows an empty
desk.

## The shape of a good first hour

1. Pull the small model and chat with it.
2. Ask it the same question twice with
   `/set parameter temperature 0.1` in
   between, and once at `1.5`: you have
   just learned more about generation
   settings (chapter 9) than a chapter of
   prose could teach.
3. Pull one size up — `llama3.2:3b` — and
   ask both models the hardest question
   you tried. The difference between 1 and
   3 billion parameters, on your own
   machine, with your own questions, is
   the real syllabus for chapter 7's
   arithmetic about sizes.

Nothing here can break anything: models
are files in a store, deleted as easily
as they came (`ollama rm llama3.2:1b`).
The next chapter is about choosing them
on purpose rather than by name.
