# Installing Ollama

Ollama installs like a program, not a
project: one download, no runtime to
assemble, no Python environment to nurse.
This chapter covers the three desktop
systems and the one decision that matters
— whether the server starts itself at
boot or only when you ask.

## Linux: the script

The official way is a single command,
which downloads Ollama, puts the `ollama`
program on your path, and — on systems
running systemd — installs a service so
the server starts at boot:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Piping a stranger's script into a shell
deserves a pause. If you would rather look
first, download the script, read it, then
run it: it is short, and all it does is
fetch the release archive for your
processor (almost always `amd64`;
`arm64` on ARM machines), unpack it to
`/usr/local`, and write the service file.
The guide's lab ran the script on a plain
Ubuntu 24.04 machine; the transcript is
`ollama-lab/transcripts/t00-install.txt`.

Check:

```bash
ollama --version
curl http://localhost:11434/
```

The first prints the version; the second
should answer `Ollama is running`, which
means the service started. If your system
has no systemd, start the server yourself
when you want it:

```bash
ollama serve
```

Leave that window open — the server lives
as long as the command does — and use a
second window for everything else.

**Where things land.** The program is
`/usr/local/bin/ollama`. When the service
runs it, models are stored under the
service account's home,
`/usr/share/ollama/.ollama/models`. When
you run `ollama serve` yourself, the store
is `~/.ollama/models`. Two stores on one
machine is a common confusion; chapter 7
explains the store, and chapter 15 covers
the "my model vanished" version of this.

**Graphics cards.** On a machine with an
NVIDIA or AMD graphics card, the Linux
archive already carries what the runner
needs to use it; there is nothing extra
to install. The server log says which
hardware it found — chapter 7 shows where
to look, and how to tell whether your
model is actually on the card or quietly
back on the processor.

## macOS and Windows: the apps

On macOS, download the app from
ollama.com, drag it to Applications, and
run it: it puts a small icon in the menu
bar, starts the server in the background,
and installs the `ollama` command for
Terminal windows. On Windows the shape is
the same: run the installer, and Ollama
lives in the system tray with the command
available in PowerShell and Windows
Terminal. On both, the store sits in your
home directory (`~/.ollama`), and the
server answers at the same
`localhost:11434`.

Homebrew users on macOS can instead
`brew install ollama` and start it with
`brew services start ollama`; the cask
(`brew install --cask ollama`) is the app.
The guide's lab is a Linux machine, so
the macOS and Windows paths here are from
Ollama's documentation, not run for this
guide — they are labelled documented-only
in `criteria.md`, and the rest of the
guide is identical once the server
answers, because everything after this
chapter talks to the server, not the
installer.

## The one decision: who starts the server

- **The service (Linux) or the app
  (macOS/Windows)** starts the server at
  login or boot, so `ollama run` just
  works. This is what most people want.
- **By hand** (`ollama serve` in a window)
  suits a machine where models are an
  occasional guest, or where you want to
  set options — a different port, a
  different store, an address other
  machines can reach — for this run only.
  Chapter 10 covers running it properly
  as a service with your own settings.

Either way, the check is the same: if
`curl http://localhost:11434/` answers,
you have a server, and chapter 4 can put
a model on it.
