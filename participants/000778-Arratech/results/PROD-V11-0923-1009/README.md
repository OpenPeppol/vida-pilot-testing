# Arratech (PSE000778) — test run `PROD-V11-0923-1009`

Executed **2026-09-23 08:09 UTC** on Arratech's production platform against the **Peppol test** network. Both business corners are hosted by Arratech, so each exchange is C2 and C3 on the same platform.

* **TDD v1.1** — invoice UUID over BT-31 (TDT-017), `TransmissionUUID` (TDT-018) present
* **Customisation**, per this repository's usage notes: placeholder endpoint identifiers `001109` / `001110` replaced with our own `000778`, and the test-run id appended to the invoice number (BT-1), which therefore feeds the invoice UUID
* **Tax authorities:** AT `0242:001067` · BE `0242:001104` · DK `0242:000152` · FI `0242:001111` · IE `0242:000971` · NO `0242:001111` · SE `0242:001111`

## Result

| Scenario | Invoices | Reports (C2+C3) | Completed | Failed |
|---|--:|--:|--:|--:|
| NW-HP-001 | 42 | 84 | 84 | 0 |
| NW-HP-002 | 42 | 84 | 84 | 0 |
| NW-HP-002-RC | 5 | 10 | 10 | 0 |
| NW-HP-006 | 42 | 84 | 84 | 0 |
| NW-HP-008 | 41 | 82 | 82 | 0 |
| **Total** | **172** | **344** | **344** | **0** |

Invoices: **344 / 344 business documents delivered** (each invoice once on the sending side, once on the receiving side).

Reports by tax administration, all completed: AT 50 · BE 49 · DK 52 · FI 49 · IE 49 · NO 47 · SE 48.

## TDD verification

Every filed TDD was read back and parsed, rather than trusting transaction status.

| Check | Result |
|---|--:|
| Reports carrying `TransmissionUUID` (TDT-018) | 344 / 344 |
| Exchanges with both corners reporting | 172 / 172 |
| Same invoice UUID on both corners | 172 / 172 |
| Same `TransmissionUUID` on both corners | 172 / 172 |
| One C2 and one C3 report per exchange | 172 / 172 |
| Distinct `TransmissionUUID` values | 172 |

## Samples

One exchange per scenario, with the invoice as sent, both corners' TDDs and the MLS each received.

## Not covered

* **NW-HP-007** — the `##TDD:MINIMUM##` marker is not implemented. The scenario publishes no `sample-results`, so there is no reference for which fields a minimum TDD retains.
* **MLS response code** — our C3 answers a validated invoice with `AB` rather than `AP`. Known gap on our side.
* **Schematron** — TDDs are validated against the v1.1 schema, not against a schematron.

## Open question

The v1.1 commit message describes `TransmissionUUID` as random, while `PeppolViDATDD110ReportedTransactionBuilder` states it is "not derived from the document content, but taken from the transmission itself (e.g. the SBDH Instance Identifier)". We implemented the latter, so our C2 and C3 reports carry the same value for one exchange. Only C3 samples are published, so the readings cannot be distinguished from the test data. Confirmation welcome.
