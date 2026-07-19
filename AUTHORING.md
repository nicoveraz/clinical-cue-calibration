# AUTHORING — how to write a valid case

A practical checklist. The theory is in `DESIGN.md`; this is the bench procedure. The
goal of every case is a clean measurement, not a clever trap.

## Before you write

1. **Pick the mode by the state of the cue.** Is the decisive cue being *pushed and
   false* (Resist), *present but buried* (Detect), or *absent but recoverable*
   (Inquire)?
2. **Name the cue and defend its weight.** One sentence: what is the clue, and why
   does it carry high diagnostic weight? If you cannot defend the weight, stop.
3. **Write the decoy frame.** What coherent, complete, *plausible* answer would a
   competent-but-unanchored reader give if they missed the cue? If the decoy is not
   genuinely attractive, the case proves nothing.

## The validity gate (do not skip)

4. **Competent-expert test.** Could a competent clinician, with **only the text you
   wrote** (no physical exam, no chart they don't have), reasonably reorganize / resist
   / ask? Write that defense into `expert_reorganization.justification`. If the cue
   really needed the exam, either convert it to a defensible textual cue or discard the
   case.
5. **Modality honesty.** Is the cue textual or perceptual? If perceptual, you had to
   *describe* it — which raised its salience. Mark it and write the caveat. Prefer
   textual cues.

## Build the pair

6. **Construct the variants** with the labels for your mode:
   - Resist → `pushed` (+ optional `neutral`).
   - Detect → `without-cue` and `with-cue`, **identical except for the cue itself**
     (same length, same position, same phrasing elsewhere — no salience leakage).
   - Inquire → `insufficient` and a **sufficiency control** (`sufficient`) where the
     information genuinely licenses closing. The control is mandatory.
7. **Pre-register the delta.** In `prediction.expected_delta`, write what you expect to
   differ across variants — *before* you run any model.

## Self-check before committing

- [ ] The decoy frame is plausible, not a strawman.
- [ ] Detect variants differ in the cue and nothing else that changes salience.
- [ ] Inquire has a sufficiency control; you are measuring discrimination, not asking.
- [ ] The competent-expert defense is written and honest.
- [ ] Modality is marked; perceptual cues carry their caveat.
- [ ] `collapse_condition` describes total invalidation, not a minor imperfection.
- [ ] The expected delta is recorded before running.

## Anti-patterns

- **The gotcha.** A cue no reasonable clinician would weight differently. Fails gate 4.
- **The exam smuggled into text.** A perceptual finding written as if it were data,
  un-marked. Fails gate 5.
- **The reflexive-ask farm.** Inquire cases with no sufficiency control reward a model
  that always asks. Useless.
- **The named-not-reframed pass.** Counting a response that merely *mentions* the cue
  as a detection. The cue must *re-center* the answer.
