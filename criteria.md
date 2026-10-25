# Writing Criteria

Every guide in this repo is written to the same
standard. If you're adding or editing a guide —
human or AI — hold it to these.

1. **Plain words, real ideas.** Simple language,
   never dumbed-down concepts. Explain jargon on
   first use; collect terms in the glossary.
2. **No hype.** No marketing adjectives, no
   "revolutionary," no promises about what
   something will do for the reader.
3. **Honest about limits.** Say what the thing
   *can't* do. State uncertainty instead of
   smoothing it over. Validation notes include
   what remains unverified.
4. **No invented numbers.** Benchmarks, defaults,
   and specs are either measured or qualified with
   "depends on your setup." If it wasn't tested,
   say so.
5. **Checkable.** Commands are meant to be run,
   with "Check:" blocks showing what success looks
   like. Sources are linked, not asserted.
6. **Respect the reader's setup.** Call out
   Windows/Mac/Linux differences. Don't assume a
   GPU, a budget, or prior experience.
7. **Safety isn't skipped.** Real risks get
   stated plainly, not buried.
8. **Scoped.** Each guide says what it covers and
   points elsewhere for the rest. No guide
   pretends to be the whole subject.
9. **Structure over prose.** Tables, before/after
   examples, symptom → fix. Scannable beats essay.

## Verification notes for this guide

Built and verified 2026-10-24/25 against
**Ollama 0.40.11** on a 2-CPU, 7.7 GiB
sandbox machine running Ubuntu 24.04, with
no GPU — every inference number in this
guide is CPU inference.

**Primary sources read, not remembered:**
Ollama's official documentation and
README for installation and the systemd
service (chapter 3), the model library's
naming, tags and capabilities (chapter 5),
the Modelfile reference (chapter 6), and
the API reference — `/api/generate`,
`/api/chat`, `/api/tags`, `/api/embed`
and the OpenAI-compatible `/v1`
endpoints (chapter 8) — plus the FAQ's
serving variables (`OLLAMA_HOST`,
`OLLAMA_MODELS`, `OLLAMA_KEEP_ALIVE`,
parallel and max-loaded settings,
chapter 10).

**The sandbox, declared.** The lab
machine is a shared sandbox: other work
was running on it throughout, and its
load average sat between 3 and 7 on two
cores while the lab ran. That contention
is in every timing below, and the
chapters say so where the numbers
appear. The sandbox was also replaced
mid-build: the first install and the
model pull survived only as files under
the workspace, and Ollama was installed
a second time from the official script
before the exercises ran (the script
itself needed `zstd` present, which the
fresh machine lacked — the first attempt
on it failed with exactly that message).
The lab's model store lives under the
workspace (`OLLAMA_MODELS` pointed
there), not in the default home
directory, so everything the lab
downloaded is one deletable folder.

**Ran for real** (transcripts in
`ollama-lab/transcripts/`):

- t00 — the official install script run
  end to end: it installed to `/usr/local`,
  created the `ollama` user and a systemd
  service and started it; `ollama
  --version` → 0.40.11; `curl /` →
  "Ollama is running"; `/api/version` →
  0.40.11. The lab then stopped that
  service and ran its own server by hand
  (see the note at the end of t00) so the
  store stayed in the workspace.
- t01 — `ollama pull llama3.2:1b`
  (1.2B parameters, Q8_0, 1.3 GB);
  `ollama list`; `ollama show`; a
  `--verbose` run (a haiku; eval rate
  1.85 tokens/s under the heaviest
  contention of the day); `ollama ps`
  showing the model loaded at 1.5 GB,
  100% CPU, context 4096; a piped-stdin
  chat; `ollama stop`; `ps` empty again.
- t03 — the HTTP API with curl: `/`,
  `/api/version`, `/api/tags`,
  `/api/generate` (non-streaming and
  streaming), `/api/chat` with a system
  message, `/v1/models` and
  `/v1/chat/completions`. Speed computed
  from the reply's own numbers:
  `eval_count` 220 over 35.97 s of
  `eval_duration` → **6.1 tokens/s
  generated**, 35.1 tokens/s prompt
  eval, on the shared 2-core box. The
  server log independently confirms the
  default context of 4096 chapter 7
  describes.
- t04 — `ollama create plain-talker`
  from the Modelfile in
  `ollama-lab/modelfile/` (FROM
  llama3.2:1b, temperature 0.3, a
  plain-spoken-editor system prompt):
  the build reuses the parent's layers
  and adds two small ones, exactly the
  shared-blob behaviour chapter 6
  describes; `ollama show plain-talker`
  shows the baked-in temperature and
  system prompt; the custom model and
  its parent answer the same question,
  side by side.
- t05 — serving mechanics: a second
  server on port 11435 sharing the same
  store; a client pointed at it with
  `OLLAMA_HOST` lists both models and
  runs one; the first server's
  `ollama ps` is unaffected.
- t06 — housekeeping: `ollama cp
  llama3.2:1b llama3.2:1b-keeper` (same
  ID, a manifest not a copy);
  `ollama show --modelfile plain-talker`
  printing the full recipe back;
  `du -sh` on the store (1.3 GB);
  `ollama rm` of the keeper.
- t90 — errors: `ollama run
  no-such-model:1b` fails with "pull
  model manifest: file does not exist"
  (exit 1); the same name through
  `/api/generate` returns
  `{"error":"model 'no-such-model:1b'
  not found"}`; the shelf is unchanged.

**Errors and oddities hit for real:**
the missing-`zstd` install failure
above; a first cold model load slow
enough under contention to look hung
(load durations of 9–11 s appear in the
transcripts' own numbers — chapter 15
covers reading them); and the 1B model
answering a perfectly ordinary piped
question ("In one sentence, what is a
haiku?") with "I'm sorry, but I can't
fulfill this request." — kept in t01,
because small models do this and
chapter 14 says so.

**Documented only, and labelled as
such in the chapters:** GPU detection
and acceleration (the lab has none);
the Windows and macOS installs
(chapter 3); serving between two real
machines over a network (t05 proves
the mechanics on one machine, two
ports); editing the systemd unit with
`systemctl edit` and the other serving
variables in production use (the
installer-created unit ran for t00;
the lab's exercises used hand-run
servers); `/api/embed` and embedding
models (the lab pulled one model
only, to keep downloads modest);
`ollama push` and sign-in; structured
`format` output (recipe 5) and the
remaining recipes as written — they
are built only from commands proven
elsewhere in the transcripts; models
larger than 1B; and the neighbours of
chapter 13, which are surveyed, not
installed.

**Fixtures:** the one downloaded model
is public (Meta's Llama 3.2 1B from
Ollama's library). Everything the lab
made up is small and generic — a
plain-spoken-editor Modelfile, a haiku
about a server room, ten garden birds —
written for any reader to reproduce.
Nothing in the lab is modelled on any
real person, organisation or event.
