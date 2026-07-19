# Clinical Cue Calibration — an evaluation apparatus

A framework and harness for probing whether a clinical LLM assigns diagnostic
evidence **the weight it deserves**. This is not a collection of test cases. It is the
**mould** that generates them: a schema, a paired-probe instrument, and an authoring
discipline. The `cases/` directory ships with the `TEMPLATE` and **three demo
cases** (one per mode) that exist to show the instrument working — the evaluation
sets proper are written by the clinician, not by the tool, and a private holdout
set is withheld against training-data contamination.

---

## The failure this measures

In a real consultation, the clue that should reorganize the whole interpretation is
often **low in surface salience but high in diagnostic weight**. A competent clinician
catches it and re-centers diagnosis, prognosis, and plan around it. A novice — or a
fluent, complacent model — misses it and produces an output that is internally
coherent, complete, and confident, yet **wrong in its entirety because it was built on
the wrong frame**.

The dangerous property is precisely that the failure is *not* a wrong fact inside an
otherwise good answer. Coherence and completeness are preserved intact while the
foundation is mistaken, so **the whole output becomes a hallucination** — and it looks
exactly as assured as a correct one. A confidence signal alone will not catch it,
because the model is not locally unsure; it is globally anchored to the wrong frame.

Stated as one mechanism: **the weight the model assigns to a cue does not scale with
the cue's true diagnostic weight.** Secondary-gain consultations (a patient steering
toward leave, a subsidy, a pension) are one instance. So is the recent travel that
reframes a pneumonia, the drug that makes a symptom iatrogenic, the age that shifts
the pretest probability, the incongruence between affect and account. One
meta-structure, many instances drawn from clinical experience.

---

## Three modes of the same failure

The mechanism — calibration of the weight of evidence — fails at three distinct
points. The apparatus treats them as the spine of the whole evaluation.

| Mode | Cue state | The failure | Probe |
|------|-----------|-------------|-------|
| **Resist** | a *false* cue the user actively pushes | being dragged — over-weighting something undeserving | does the model adopt the pushed frame? |
| **Detect** | a *true* cue present but not highlighted | not reorganizing — under-weighting something present | does behavior change between *with-cue* and *without-cue*? |
| **Inquire** | a decisive cue **absent but recoverable** | closing without asking — not recognizing the evidence is insufficient | does the model ask for the missing datum before concluding? |

**Resist** and **Detect** are mirror images: a false-positive versus a false-negative
of *salience*. **Inquire** is a higher-order failure — it attacks the assumption the
first two take for granted, that the case as given can be answered at all. A
calibrated clinician knows when the information in front of them does not yet license a
conclusion, and goes to get more.

A required guard for **Inquire**: a model that *always* asks for more is not prudent,
it is useless. So inquire-mode cases are paired against **sufficiency** cases where the
information *is* enough and the correct act is to close. The metric is
**discrimination** — ask when it is missing, close when it is enough — not a raw
asking rate.

---

## Relationship to prior work

This is the active counterpart to a deferral signal. `boundary-signature`
(https://github.com/nicoveraz/boundary-signature, DOI 10.5281/zenodo.20672237) asks
*when to defer* an uncertain answer to a clinician — a passive, quantitative signal.
**Inquire** mode asks the model to do the active version of the same thing: not answer
yet, but request what is missing. The two together cover both halves of epistemic
humility in a clinical assistant.

Supporting technical work: `boundary-signature` (UQ / selective prediction),
`NeuroGen` (pre-training research, DOI 10.5281/zenodo.19194323), `urgencias-core`
(production ED forecasting, MIT).

---

## What this repository is, and is not

- **It is** an apparatus: the meta-structure, a case schema (`schema/`), an authoring
  discipline (`AUTHORING.md`), the experimental design (`DESIGN.md`), and a
  paired-probe harness (`run_eval.py`).
- **It is not** a finished benchmark. `cases/` ships the template plus three demo
  cases (`resist-001`, `detect-001`, `inquire-001`) that demonstrate the paired
  probe; evaluation sets proper are authored by the clinician from experience,
  under the validity criteria in `DESIGN.md`, and a private holdout set is
  withheld so results stay meaningful after the public cases enter training
  corpora.
- It uses **no proprietary or patient data**. Cases are synthetic vignettes; the
  validity of each cue rests on clinical judgment, not on any record.

The human side — clinical reasoning and service consensus — is the **source of a
cue's validity**, the answer to "why does this cue deserve this weight". It is not a
measured human baseline and is not claimed as data.

---

## Using the apparatus

```bash
pip install -e '.[all]'          # or .[anthropic] / .[openai]

# author a case from the template, then:
python run_eval.py --validate    # checks structure + validity gates, no API calls
python run_eval.py --dry-run     # prints the probe plan, no API calls

# run paired probes against a target model:
python run_eval.py --provider anthropic --model <name> [--judge-model <name>]
# -> results/report.md  (advisory judge + blank HUMAN DECISION per case)
#    results/results.json
```

The judge is **advisory**; the harness leaves the final outcome to human review.

## License

CC BY 4.0. See [`LICENSE`](LICENSE).
