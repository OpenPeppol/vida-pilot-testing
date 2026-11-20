# Arratech (PSE000778) — test run `MIXED-1112-1322`

Executed **2026-11-12 13:22 UTC** on Arratech's production platform against the **Peppol test** network. Both business corners are hosted by Arratech, so each exchange is C2 and C3 on the same platform.

* **Scenarios:** NW-HP-002-IC-Mixed (all 20 published invoices) and NW-HP-002-RC-Mixed (the 1 published invoice)
* **TDD v1.1**: invoice UUID over BT-31 (TDT-017), `TransmissionUUID` (TDT-018) present
* **Customisation**, per this repository's usage notes: placeholder endpoint identifiers `001109` / `001110` replaced with our own `000778`, and the test-run id appended to the invoice number (BT-1), which therefore feeds the invoice UUID
* **Tax authorities:** AT `0242:001067` · BE `0242:001104` · DK `0242:000152` · FI `0242:001111` · IE `0242:000971` · SE `0242:001111`

## Result

| Scenario | Invoices | Reports (C2+C3) | Completed | Failed |
|---|--:|--:|--:|--:|
| NW-HP-002-IC-Mixed | 20 | 40 | 39 | 1 |
| NW-HP-002-RC-Mixed | 1 | 2 | 2 | 0 |
| **Total** | **21** | **42** | **41** | **1** |

Invoices: **42 / 42 business documents delivered** (each invoice once on the sending side, once on the receiving side).

Reports by tax administration, completed: AT 8 · BE 8 · DK 4 · FI 8 · IE 9 · SE 4.

**The one failure** was C2's report for IC-Mixed **AT-SE**, addressed to `0242:001067-vidapilot.tax_at`: the recipient lookup failed (recipient not found on the network). The other 8 reports to the same Austrian endpoint, sent within the same 26 seconds, completed. The invoice was resent as run `MIXED-1112-1340` (13:40 UTC), and both its reports (C2 → AT, C3 → SE) completed, as did both business documents.

## TDD verification

Every filed TDD was read back and parsed, rather than trusting transaction status. Figures include the AT-SE resend.

| Check | Result |
|---|--:|
| One C2 and one C3 report per exchange | 21 / 21 |
| Same invoice UUID on both corners | 21 / 21 |
| Same `TransmissionUUID` on both corners | 21 / 21 |
| C2 and C3 tax breakdown (category, rate, taxable amount, tax amount) identical | 21 / 21 |
| C3 tax breakdown equal to the published `sample-results` TDD-C3 | 21 / 21 |
| C3 line tax category, rate and line amount equal to the published TDD-C3 | 21 / 21 |
| C5 MLS on the sample exchanges | `AP` (4 / 4) |

VAT categories carried into the TDDs, unchanged from the invoices:

* **IC-Mixed:** `K` on all three lines and in the breakdown (20 / 20)
* **RC-Mixed:** `AE`, `AE`, `K` on the lines; breakdown `AE` + `K` (1 / 1)

## Samples

One exchange per scenario, with the invoice as sent and as received, both corners' TDDs and the MLS each TDD received from C5:

* `NW-HP-002-IC-Mixed/results/MIXED-1112-1322/` (BE-DK)
* `NW-HP-002-RC-Mixed/results/MIXED-1112-1322/` (AT-IE)
