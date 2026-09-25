**[Daemons](https://en.wikipedia.org/wiki/Daemon_(computing)) for brain research.**

A behavioural rig, decomposed: one small service per job, each owning its
hardware, coupled to the others by trigger edges and a strongly-typed inter-daemon protocol.

Why: an experiment needs dedicated hardware and software that does what it
is supposed to do and nothing else. Not a desktop
that checks for Windows updates and new printers mid-session, and breaks after
an automatic update.

> **Alpha.** Under active development, not validated for data collection. Each
> README says plainly what is real and what is not.

- **[vstimd](https://github.com/braemons/vstimd)**: reliable 2D and 3D visual stimulation
- **[statemachined](https://github.com/braemons/statemachined)**: within-trial state machine, running on a microcontroller. Times the trial and names its outcome.
- **[mousewheeld](https://github.com/braemons/mousewheeld)**: rotary encoder-based input device
- **[triald](https://github.com/braemons/triald)**: trial control. Picks what runs next and records the session; the rule is a Python class.

What they have in common:

- **Reliable timing and trigger-driven.** Configure a daemon ahead of time (before a trial or even before the experiment); it then acts on trigger edges. Timing-critical parts are running on dedicated hardware.
- **Integrating with other hardware.**: State changes are TTL-based, which can be captured by data acqusition systems and integrated into other ecosystemns such as [Bpods](https://sanworks.github.io/Bpod_Wiki/).
- **Integrating with other software.** Braemons can be controlled via Python, MATLAB or Bonsai (.NET) clients through a binary [Protobuf](protobuf.dev)-based protocol.
- **Built-in monitoring via a browser.** All braemons come with a web interface, and all can be combined into one console.
- **Flexibly combined and distributed.** They do not depend on each other and may optionally run on different machines.
- **Ordinary Unix daemons.** A systemd unit, a config file, logs in the journal, a package that installs it, on a Raspberry Pi, an amd64 desktop, or a laptop.
