# Arratech (PSE000778) — test run `PROD-V11-0922-0902`

Executed **2026-09-22 07:02 UTC** on Arratech's **production** platform, against the **Peppol test** network (test SML, test certificates, pilot participants). Both business corners are hosted by Arratech, so every exchange is C2 and C3 on the same platform.

* **TDD version:** v1.1 — invoice UUID over BT-31 (TDT-017), `TransmissionUUID` present (TDT-018)
* **Customisation applied**, per this repository's usage notes: the placeholder endpoint identifiers `001109` / `001110` replaced with our own `000778`, and the test-run id `PROD-V11-0922-0902` appended to the invoice number (BT-1), which therefore feeds the invoice UUID.
* **Tax authorities:** AT `0242:001067` · BE `0242:001104` · DK `0242:000152` · FI `0242:001111` · IE `0242:000971` · NO `0242:001111` · SE `0242:001111`

## Scenarios executed

| Scenario | Invoices | Reports (C2+C3) | Completed | Failed |
|---|--:|--:|--:|--:|
| NW-HP-001 | 42 | 84 | 60 | 24 |
| NW-HP-002 | 42 | 84 | 60 | 24 |
| NW-HP-002-RC | 5 | 10 | 7 | 3 |
| NW-HP-006 | 42 | 84 | 60 | 24 |
| NW-HP-008 | 41 | 82 | 58 | 24 |
| **Total** | **172** | **344** | **245** | **99** |

NW-HP-007 was **not** run — see "Not covered" below.

## Invoice exchange

All 172 invoices were accepted, sent and delivered: **344 / 344 business documents COMPLETED** (each invoice appears once on the sending side and once on the receiving side). No invoice failed at any stage.

## TDD verification

Checked by reading every filed TDD back from the platform, not by transaction status alone.

| Check | Result |
|---|--:|
| Reports filed | 344 |
| Reports carrying `TransmissionUUID` (TDT-018) | **344 / 344** |
| Exchanges with both corners reporting | 172 / 172 |
| Same invoice UUID on both corners | **172 / 172** |
| Same `TransmissionUUID` on both corners | **172 / 172** |
| One C2 report and one C3 report per exchange | 172 / 172 |
| Distinct `TransmissionUUID` values across the run | 172 |

## Failures — all delivery to two tax authority endpoints

The 99 failures are **not** document failures. Every one of them was built and stored correctly, carries the correct v1.1 identifiers, and failed at AS4 delivery (`TRANSPORT_ERROR`). They are confined to two receivers:

| Tax authority | Failed | Comment |
|---|--:|---|
| AT `0242:001067-vidapilot.tax_at` | 50 | Delivery refused. Worked from the same code on 2026-09-18; began failing 2026-09-21. |
| BE `0242:001104-vidapilot.tax_be` | 49 | Delivery refused. Worked on 2026-09-18; began failing 2026-09-22. |

Spread evenly across scenarios: 12 + 12 per scenario for the four large ones, 2 (AT) and 1 (BE) for NW-HP-002-RC.

**Both endpoints recovered on 2026-09-23.** A retest the following morning — four exchanges into and out of each — delivered 4/4 to Austria and 4/4 to Belgium, all completed. Austria's SMP publishes complete signed metadata for the ViDA TDD document type naming `https://peppol-vida-pilot.bmf.gv.at/as4`, and that endpoint's TLS chain verifies cleanly. So the failures below were a transient outage at both, now over.

Evidence that this was environmental rather than ours:

* The same build delivered to both endpoints on 2026-09-18 (AT 50/50, BE 49/49 completed).
* DK, FI, IE, NO and SE all completed in this very run, from the same code and the same access point.
* Austria still completed from our production platform on 2026-09-21, one day before this run, and had already started failing from our staging platform that same morning.

### Sweden — resolved, included for completeness

Earlier runs showed 0 reports delivered to Sweden. Cause: `0242:000969-vidapilot.tax_se` serves its leaf certificate without the intermediate, so no trust path can be built. No TDD has ever been delivered to it from either of our environments. We repointed Sweden to the pilot playground AP `0242:001111-vidapilot.tax_se` on 2026-09-21; Sweden reports **48 / 48 completed** in this run.

## Not covered

* **NW-HP-007** — the `##TDD:MINIMUM##` marker is not implemented. This scenario publishes no `sample-results`, so there is no reference showing which fields a minimum TDD retains, and it cannot be built from the description alone.
* **MLS response code** — our C3 answers a validated invoice with `AB` (delivered without confirmation) rather than `AP`. Known gap on our side, being fixed.
* **Schematron** — our TDDs are validated against the v1.1 schema but have not been run through a schematron.

## Open question for the pilot team

The v1.1 commit message describes `TransmissionUUID` as random, while `PeppolViDATDD110ReportedTransactionBuilder` states it is "not derived from the document content, but taken from the transmission itself (e.g. the SBDH Instance Identifier)". We implemented the latter, which is why our C2 and C3 reports carry the same value for one exchange. Only C3 samples are published, so the two readings cannot be distinguished from the test data. Confirmation welcome.
