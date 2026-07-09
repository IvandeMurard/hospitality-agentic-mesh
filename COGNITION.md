# Cognition, not just prediction

*The design thesis behind the mesh. Aetherix is the proof; Anima is the promise.*

## The argument

The prediction layer of vertical AI is commoditizing fast. Our own benchmark
([`benchmark/`](benchmark/)) shows Prophet tying a naive same-weekday baseline on the median
day, on real restaurant data. What does not commoditize is what sits around the prediction:

- **capturing outcomes** next to the forecasts that preceded them,
- **self-reporting** the miss to the human, in plain language, the next morning,
- **guarding** the training data against implausible inputs (typed, auditable rejections),
- **recalibrating** on what actually happened,
- and compounding all of it into a **per-property operational memory** that a competitor
  cannot copy by training the same model.

Aetherix, the F&B node, implements this loop today. The demo artifacts in
[`demo/closed-loop/`](demo/closed-loop/) show it end to end: the agent citing its drivers the
evening before, flagging a corrupted POS export, naming its own drift after three consecutive
misses, and recovering from a +25% regime shift through weekly recalibration. Sandbox data,
real mechanics, deterministic reruns.

## Extending the thesis to guests: Anima (design stage)

Anima applies the same cognitive architecture to the guest relationship. It is a **design
thesis, not a shipped system**, and we say so plainly. The published outline:

- **Four memory layers with different lifetimes.** Working memory (the current stay, expires),
  episodic memory (stay + a short tail), semantic memory (durable preferences), and an
  anonymized segment layer (what guests-like-this tend to need). Temporal separation is the
  point: most guest-AI failures come from treating everything as permanent.
- **Scope-gated access.** Any consumer must declare which layer it queries. No blanket
  "give me everything about this guest".
- **Cognition informs; it never decides.** Anima answers "who is this guest, right now?".
  A separate orchestrator, with human validation, decides what to do about it. Same boundary
  Aetherix enforces between perception and decision.
- **Privacy first, structurally.** Inferred guest state is sensitive personal data. The
  non-negotiable gate before any build: formal GDPR/CNIL analysis and a DPIA. We consider the
  privacy posture part of the product, not a compliance tax: a guest-cognition system a hotel
  cannot legally deploy is worthless.

The detailed schemas (signal contracts, confidence weighting, federation design) are
deliberately private. This page states the thesis; the proof will follow the same path
Aetherix took: build, instrument, benchmark honestly, publish the loop.

## Reading list in this repo

- [`demo/closed-loop/memory_tour.md`](demo/closed-loop/memory_tour.md): what the system knows
  after 30 days
- [`demo/closed-loop/whatsapp_transcript.md`](demo/closed-loop/whatsapp_transcript.md): the
  full manager thread: anticipation, self-reports, drift detection
- [`benchmark/`](benchmark/): the real-data benchmark that motivated this whole framing
