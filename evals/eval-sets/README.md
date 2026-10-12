# evals/eval-sets/

One eval-set document per module (or per requirement group): the specification table of `EVAL-nnn`
cases — written **before** implementation — plus out-of-scope disclosures and where each was
transmitted upstream. A Pattern A register. The executable tests live with the implementation and
are named `test_eval_<id>_<description>` so document and suite stay mechanically linked.

Specified by `create-eval-set`; changed by `modify-eval-set`; reviewed by `review-eval-set`. Every run
is logged to `logs/eval-log.md`.
