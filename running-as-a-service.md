# Running as a Service

A model you run by hand serves the person
sitting at the keyboard. A model run as a
**service** serves the household: it is
there after a reboot, it answers other
machines, and other programs can build on
it. This chapter is about the step from
`ollama serve` in a window to a server
that runs the house — and about the one
warnings label that step requires.

## What you already have

On Linux, the install (chapter 3) already
made a service: systemd starts `ollama
serve` at boot, as its own user, with the
store in `/usr/share/ollama/.ollama`. On
macOS and Windows the desktop app plays
the same role for the logged-in user.
Check it the way chapter 2 taught: after
a reboot, before you touch anything,
`curl http://localhost:11434/` should
answer.

## Letting other machines in

By default the server listens on
`127.0.0.1` — this machine only. To serve
the network, it must listen on an address
the network can reach, which is set with
the `OLLAMA_HOST` environment variable
*on the server*: `OLLAMA_HOST=0.0.0.0:11434`
means "every network interface, the usual
port." (The same variable name, set on a
*client*, means "the server lives over
there" — chapter 2. The direction is the
whole difference; read each use asking
*who is speaking*.)

On Linux with the systemd service, add
your settings the systemd way rather
than editing the shipped unit:

```bash
sudo systemctl edit ollama
```

and in the editor:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_MODELS=/data/ollama-models"
```

then `sudo systemctl restart ollama`.
The drop-in is also where chapter 7's
store move (`OLLAMA_MODELS`) and this
chapter's other knobs live, so one small
file holds the whole personality of your
installation. On a hand-run server the
same variables go in the shell before
`ollama serve`; on macOS and Windows, set
`OLLAMA_HOST` in the environment the app
starts with (the documentation shows the
per-system incantation) and restart the
app.

Check: from *another* machine on the
network,

```bash
curl http://192.168.0.25:11434/
```

(with your server's address) should
answer `Ollama is running`, and
`OLLAMA_HOST=http://192.168.0.25:11434 ollama list`
on that machine lists the server's
models. The lab demonstrates the same
mechanics on one machine — a second
server on a second port, driven by a
client pointed at it — in
`ollama-lab/transcripts/t05-serving.txt`;
the physics are identical, only the
cable is shorter.

## Now the warning, in full

Chapter 14 said it and this chapter is
where it bites: **the server has no
authentication.** The moment it listens
on a network address, anyone who can
reach that address can pull models, run
them, and delete them — no password
exists to forget, because no password
exists. On a home network that means
every device and every guest and every
compromised smart bulb. Judge your own
network honestly, and consider the usual
arrangements, in rising order of
strength: leave it on `127.0.0.1` and let
only local programs use it; reach it from
outside only through a VPN or an SSH
tunnel (`ssh -L 11434:localhost:11434
you@server` gives your laptop a private
door); or put a reverse proxy that does
have logins in front of it. What you
should not do is the thing the address
makes easy: forward the port from your
router and offer your graphics card to
the internet.

## Tuning the service

Three settings matter once other things
depend on the server:

- **`OLLAMA_KEEP_ALIVE`** — how long a
  model stays loaded after its last
  request (default 5 minutes; the API
  and CLI can ask per request). Longer
  means the next question is instant;
  shorter frees memory for other work.
  For a machine that does one thing,
  `24h` is a legitimate answer; on a
  shared laptop, the default earns its
  keep.
- **`OLLAMA_NUM_PARALLEL`** — how many
  requests one model answers at once
  (default 1). More parallelism shares
  the same arithmetic among more
  askers — everyone slower, nobody
  queued — and increases memory use,
  because each conversation carries its
  own context.
- **`OLLAMA_MAX_LOADED_MODELS`** — how
  many different models may sit in
  memory together before loading a new
  one evicts the oldest (default depends
  on the machine; 3 on typical desktops).
  Chapter 7's arithmetic says whether
  your favourites can actually coexist.

All three are server environment
variables, set where `OLLAMA_HOST` is
set, and all take effect at restart.

## Logs and liveness

The Linux service logs to the journal:
`journalctl -u ollama` shows startups,
which hardware the runner claimed, model
loads, and errors — the first place
chapter 15 sends you. A hand-run server
logs to its window. For "is it up" from
another program, the health check is the
same as ever: `GET /` answering
`Ollama is running`, or `/api/tags` if
you want proof it can see its store.
