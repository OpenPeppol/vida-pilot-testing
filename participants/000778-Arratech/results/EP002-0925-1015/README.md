# Arratech (PSE000778) — test run `EP002-0925-1015`

Executed **2026-09-25 10:15 UTC** on Arratech's production platform. Scope: the **C2 (sending) side of NW-EP-002**, all 42 published invoices.

* **Customisation**, as in our earlier runs: placeholder endpoint identifiers `001109` / `001110` replaced with our own `000778`, and the test-run id appended to the invoice number (BT-1)
* Arratech's C2 validates every outbound invoice and has no switch to skip it, so this run checks that the intentionally invalid invoices are **stopped before transmission**. The scenario README describes a C2 that sends an invalid invoice as non-compliant.

## Result

| Check | Result |
|---|--:|
| Invoices submitted | 42 |
| Stopped by C2 validation (transaction `FAILED`, validation error) | 42 / 42 |
| Sent to C3 | 0 |
| Sell-side TDDs sent to C5 | 0 |
| Fatal rules identical to the published `supporting-files/…-validation.xml` | 42 / 42 |

Fatal rules raised:

| Invoices | Rules |
|--:|---|
| 30 | `BR-S-01`, `BR-IC-05`, `BR-IC-08` |
| 12 (involving NO) | `BR-S-01`, `BR-G-05`, `BR-G-08` |

## Samples

One invoice (BE-DK) under `NW-EP-002/results/EP002-0925-1015/`: the document as submitted to C2, and C2's validation result.

## Not covered

* **NW-EP-002, C3 side** — this needs an invalid invoice delivered to one of our participants, which only a non-validating C2 (the Playground sending AP) can do. By design our C3 answers a fatal validation failure with MLS `RE` listing the failed rules, and skips the buy-side TDD; not yet exercised in the pilot.
* **NW-EP-001** — on 2026-09-25, `9913:001110-vidapilot.rejecting-c3` is published on the Playground SMP but not registered in the test SML, so an SML-based C2 cannot discover it.
