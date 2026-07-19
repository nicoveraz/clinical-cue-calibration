# cases/

This directory holds the cases **you** author. It ships with `TEMPLATE.yaml` and
three **demo cases** — `resist-001`, `detect-001`, `inquire-001`, one per mode —
whose job is to show the instrument working end to end (vignettes in es-CL,
deliberately bedrock clinical content so the probe design is the point).

To add a case: copy `TEMPLATE.yaml` to `<mode>-NNN.yaml` (e.g. `detect-001.yaml`),
fill it in following `AUTHORING.md`, remove the `template: true` line, and validate:

```bash
python run_eval.py --validate
```

Beyond the three demos, no evaluation set is published on purpose: a private
holdout set is withheld against training-data contamination, and the validity of
every case rests on the author's clinical judgment under the criteria in
`DESIGN.md`.
