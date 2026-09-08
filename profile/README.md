# braemons

**[Daemons](https://en.wikipedia.org/wiki/Daemon_(computing)) for brain research.**
A behavioural rig, decomposed: one small service per job, each owning its
hardware, coupled to the others by trigger edges rather than by a shared
codebase.

> **Alpha.** Under active development, not validated for data collection. Each
> README says plainly what is real and what is not.

- **[vstimd](https://github.com/braemons/vstimd)** — visual stimulation. Vulkan straight onto KMS/DRM, fired off trigger lines.
- **[statemachined](https://github.com/braemons/statemachined)** — the within-trial state machine, on a microcontroller. Times the trial and names its outcome.
- **[triald](https://github.com/braemons/triald)** — trial control. Picks what runs next and records the session; the rule is a Python class.
- **[console](https://github.com/braemons/console)** — one page for a rig, composed from the daemons' own web panels.
- **[packages](https://github.com/braemons/packages)** — signed apt archive, so rigs upgrade in place.

What they have in common:

- **Trigger-driven.** Configure a device ahead of time; it then acts on an edge in its own clock. The timing-critical path never leaves the box that owns it.
- **Ordinary Unix daemons.** A systemd unit, a config file, logs in the journal, a package that installs it — on a Raspberry Pi, an amd64 desktop, or a laptop with nothing attached.
- **Controlled from Python or a browser**, over APIs meant to be inspectable — a protocol nobody can `curl` is a protocol nobody can debug at 2 a.m.
- **TTL at the edges.** A rig is not only braemons: Bpod, photometry, an ephys recorder couple in the same way, because a trigger line does not care whose box is on the other end.
- **Freely combined.** They do not import each other. Run any subset, or substitute your own.
