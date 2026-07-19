# cases/

This directory holds the cases **you** author. It ships with only `TEMPLATE.yaml`.

To add a case: copy `TEMPLATE.yaml` to `<mode>-NNN.yaml` (e.g. `detect-001.yaml`),
fill it in following `AUTHORING.md`, remove the `template: true` line, and validate:

```bash
python run_eval.py --validate
```

No example cases are provided on purpose. The validity of every case rests on the
author's clinical judgment under the criteria in `DESIGN.md`.
