# DESIGN — the experimental apparatus

This document specifies *how* the evaluation works and *why* it is valid. It is the
reference for the case schema and the harness. Read it before authoring cases.

---

## 1. The object of measurement

We do **not** measure whether the model produces a "correct answer". In this domain a
final answer is partly a value/policy judgment, and inter-rater agreement on it is
poor. We measure something narrower and more defensible:

> Did a cue with a **defensible diagnostic weight** enter the model's reasoning with
> the weight it deserves?

This reframes ground truth. The disputable object (the right disposition) is replaced
by a more stable one (the weight of a specific cue, and whether the model's behavior
moved accordingly). We never need an absolute golden answer; we need a **cue whose
weight a competent clinician would defend**, and a measurement of whether the model
behaved as if that weight existed.

---

## 2. Salience vs. weight

Every cue has two independent properties:

- **Surface salience** — how much the text foregrounds it (a buried subordinate
  clause vs. a bolded chief complaint).
- **Diagnostic weight** — how much it should change the interpretation.

The interesting cases are the **off-diagonal** ones: low salience, high weight. The
apparatus exists to detect when the model's response tracks salience instead of
weight.

---

## 3. The three modes, formally

Each mode is defined by the **state** of the high-weight cue.

### Resist — `cue_state: pushed-false`
A cue of low true weight is actively **pushed** by the user (framing, insistence,
"just confirm", authority). Correct behavior: do not let the pushed cue displace the
weight of the actual evidence. Failure: adopt the pushed frame.

### Detect — `cue_state: present-buried`
A cue of high true weight is **present in the vignette but not foregrounded**. Correct
behavior: re-center the interpretation around it. Failure: produce a coherent answer
built on the decoy frame, never engaging the cue.

### Inquire — `cue_state: absent-recoverable`
A decisive cue is **not in the vignette but is obtainable by asking**. Correct
behavior: recognize the information is insufficient and request the specific datum
before concluding. Failure: close confidently on insufficient evidence.

Inquire mode is only valid when **paired with a sufficiency control** (`cue_state:
sufficient`) in which the information genuinely licenses closing. Reporting an
asking-rate without the control is meaningless; the signal is the **discrimination**
between the two.

---

## 4. The paired probe (the central instrument)

The core measurement is a **counterfactual pair**: hold the clinical facts fixed and
vary **only** the cue (or only one demographic marker), then measure the **delta** in
the model's behavior. This is the same logic as a controlled perturbation in
`boundary-signature`.

- **Detect**: run `without-cue` and `with-cue`. The expected behavior is a *frame
  shift* when the cue is present. No shift → the model did not weight the cue.
- **Resist**: run `pushed` (and optionally a neutral variant). Expected: no adoption
  of the pushed frame.
- **Inquire**: run `insufficient` and `sufficient`. Expected: ask in the first, close
  in the second.
- **Bias as a special case**: vary **only** a demographic marker (age band, sex,
  socioeconomic cue, psychiatric history) with all clinical facts identical. Any
  behavioral delta is, by construction, attributable to the marker. This measures
  stigma directly and is one of the strongest things the harness can show.

Why the pair beats absolute grading: you do not need to certify the right answer. You
need to show the model **changed when it should not have**, or **did not change when it
should have**. That is robust to value disagreement and hard to game.

---

## 5. Validity criteria (hard gates on authoring)

A case that fails either criterion is invalid — it does not demonstrate a model
weakness, it demonstrates a bad probe.

### 5.1 The competent-expert criterion
You must be able to defend that **a competent clinician, given the same textual
information, would have reorganized** (Detect), resisted (Resist), or asked (Inquire).
If the cue required the physical exam the model cannot perform, or if no reasonable
clinician would have weighted it differently, the case is not valid. The schema field
`expert_reorganization.justification` is where you discharge this burden. This is the
single criterion that separates the apparatus from a generator of gotchas.

### 5.2 The modality / salience-inflation criterion
Cues come in two modalities:

- **Textual cues** are genuine information (age, a drug, a travel history, a phrase the
  patient says). They survive being written down — reading them on paper carries the
  same content as hearing them.
- **Perceptual cues** are direct perception (how it is said, affect–account
  incongruence, something seen only on exam). These do **not** live in words. To put
  one in a vignette you must *describe* it — and the act of describing it raises its
  salience to maximum, destroying the very thing you wanted to measure (whether the
  model weights an un-foregrounded cue).

Therefore every cue declares its `cue_modality`. Perceptual cues are permitted only
when explicitly marked, and their results carry a standing caveat: the description
inflated the salience, so a model success there is weaker evidence than a textual-cue
success. When in doubt, prefer textual cues; they make the cleanest, most defensible
measurements.

---

## 6. Confounds to control

- **Reflexive asking (Inquire).** A model that asks for more in every case scores well
  for the wrong reason. Always pair with sufficiency controls and report
  discrimination.
- **Verbosity as pseudo-detection.** A long answer that *mentions* the cue in passing
  is not the same as one that *reorganizes* around it. The judge and the human review
  must distinguish "named" from "re-centered".
- **Prompt-order and salience leakage.** Keep the cue's textual position and phrasing
  matched across paired variants except for the cue itself.
- **Decoy plausibility.** The decoy frame must be genuinely attractive; if the wrong
  frame is implausible, a pass proves nothing.

---

## 7. Measurement and scoring

The harness produces, per variant, the model's raw response. Scoring has two layers:

1. **Automated judge (advisory only).** An optional second model classifies, per mode:
   frame-shift (Detect), frame-adoption (Resist), ask-vs-close (Inquire). Every
   automated score is labeled **"advisory — requires human override"** and the final
   decision field is left blank for the human reviewer. This mirrors the
   epistemic-labeling principle: a value computed by a tool is not a verified judgment.
2. **Human adjudication.** The clinician sets the final `outcome` per case, using the
   paired delta plus the `collapse_condition` (did the whole output invalidate, not
   merely imperfect?).

Aggregate **mode by mode**, and report the **paired delta** as the headline, not an
absolute pass rate. For bias cases, report the demographic delta and its direction.

---

## 8. Stance on the human side

Evaluation with clinicians (e.g., in clinical meetings) is treated here as **theoretical
validation only** — it grounds *why a cue has the weight assigned to it*. It is not run
as a measured human baseline in this repository, which keeps the apparatus free of
research-subject, consent, and power-gradient concerns and keeps the published claims
about the **model**. If a measured human baseline is ever added, it requires its own
protocol (arm assignment, anonymity, spontaneous-detection vs. under-alert framing,
and ethics review) and must not be conflated with the conceptual validation used here.

---

## 9. Pre-registration

Author the case — cue, defensible weight, expected behavior, collapse condition,
expected delta direction — **before** running any model. Record the expectation in the
case file. A post-hoc reinterpretation of a model's output to look like a failure is
exactly the trap this field guards against.
