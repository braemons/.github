# braemons

**Daemons for brain research.** A behavioural rig, decomposed: one small Linux
service per job, each owning its hardware, each reachable over the network,
combined into an experiment by triggers rather than by a shared codebase.

> **Status: alpha.** Every repo here is under active development and none of it
> has been validated for data collection. Each README says plainly what is real
> and what is not. Do not run a subject on this yet.

## The daemons

| | |
|---|---|
| **[vstimd](https://github.com/braemons/vstimd)** | Visual stimulation. Renders with Vulkan straight onto KMS/DRM — no X11, no compositor — and fires frame-accurate stimuli off trigger lines. Rust; ZMQ + protobuf. |
| **[statemachined](https://github.com/braemons/statemachined)** | The within-trial state machine, on a microcontroller. Debounces inputs, times in its own clock, drives the valve, and names the trial outcome. Portable C++17 core plus a host bridge. |
| **[triald](https://github.com/braemons/triald)** | Trial control. Chooses what runs next, decides whether an outcome was accepted, and records the session. The decision rule is a Python class. |
| **[console](https://github.com/braemons/console)** | One page for a rig. Composes the daemons' own web panels; holds no domain logic of its own. |
| **[packages](https://github.com/braemons/packages)** | Signed apt archive. Rigs `apt upgrade` in place instead of being re-imaged. |

Sound, optogenetics and DAQ bridging are the same shape and are not written yet.

## The principles

**Trigger-based experimental control.** The timing-critical path never leaves the
box that owns it. You configure a device ahead of time — a scene, a state graph,
an armed animation — and it then acts on a trigger edge in its own clock. Nothing
frame-accurate waits on a host, a network hop, or a Python interpreter.

**Fits into the Bpod ecosystem, because TTL does not care whose box it is.**
[Bpod](https://sanworks.io/shop/products.php?productFamily=bpod) (Sanworks, out
of the Kepecs lab) is the direct intellectual ancestor of statemachined: the
state matrix as the per-trial unit of configuration — states with timers,
event-to-next-state maps, and output actions — is Bpod's idea, and we say so.
The interoperability is the trigger lines. A braemons daemon is armed by an edge
on a line and raises edges of its own, so a Bpod state machine can trigger
vstimd, and statemachined can drive or be driven by a Bpod module, with no shared
software between them. Bring the rest of your rig — a Bpod box, a photometry
system, an ephys recorder — and it is coupled the way the daemons are coupled to
each other.

The differences are in what runs where: statemachined puts the state matrix on a
commodity board (an Uno R4 Minima first, then Teensy 4.1 and ESP32) as one
participant on a trigger bus, rather than on dedicated hardware owning the trial
loop. It is not a Bpod clone and does not speak Bpod's wire protocol.

**Unix daemons, on embedded or desktop hardware.** Each one is a long-running
service with a systemd unit, a config file under `/etc/braemons`, logs in the
journal, and a package that installs it. The reference deployment is a Raspberry
Pi 5 per job; the same binaries run on an amd64 desktop, and the same code runs
on a laptop with nothing attached, which is what makes the tests real.

**Controllable from Python or a browser.** Every daemon has a documented network
API and ships its own web UI, so a rig is reachable from any machine on the
network rather than from the one keyboard in the booth. Protocols are chosen to
be inspectable — HTTP + JSON where the deadline allows it, on the principle that
a protocol nobody can `curl` is a protocol nobody can debug at 2 a.m.

**Flexibly combined.** The daemons do not import each other. They are coupled by
trigger edges and by a small number of wire contracts — the trial outcome codes,
the trigger-line map, the session record — so a rig can run any subset of them,
substitute its own component for one of them, or add a new one without touching
the others.

## Prior art

The domain logic is carved out of **VStim** (Andreas Kreiter, Cognitive
Neurophysiology Lab, Bremen), which has run experiments for years as one Windows
process, and takes ideas from Michael Stephan's
[StimServer](https://github.com/esi-neuroscience/StimServer) and from Bpod. What
changes here is that the parts are separable, scriptable, testable without a rig,
and installable as packages.
