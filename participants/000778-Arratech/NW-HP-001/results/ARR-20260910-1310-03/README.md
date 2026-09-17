# NW-HP-001 FI-AT — Arratech test run ARR-20260910-1310-03

Executed 2026-09-10 13:10 UTC by Arratech (PSE000778) on the Peppol test network, with all four business corners hosted by Arratech:

* C1/C2: `9913:000778-vidapilot.FI12345678` (Suomi Myynti Oy)
* C3/C4: `9913:000778-vidapilot.ATU12345679` (Österr. Abnehmer GmbH)
* C5(A): `0242:001111-vidapilot.tax_fi` · C5(B): `0242:001067-vidapilot.tax_at`

Test-run id `ARR-20260910-1310-03` is appended to the invoice number (BT-1).

| Artefact | File | Result |
|---|---|---|
| Invoice as sent (SBDH-wrapped) | `NW-HP-001.FI-AT.PeppolBIS.sent-SBD.xml` | delivered, validated OK at C3 |
| Sell-side TDD, C2 → C5(A) | `…TDD-C2.xml` | ReporterRole C2, DocumentScope IC, invoice UUID `fe987ebb-094d-5ca3-98cf-ce7294e17041` |
| MLS from C5(A) to C2 | `…MLS-TDD-C5-C2.xml` | **AP** |
| Buy-side TDD, C3 → C5(B) | `…TDD-C3.xml` | ReporterRole C3, DocumentScope IC, same invoice UUID |
| MLS from C5(B) to C3 | `…MLS-TDD-C5-C3.xml` | **AP** |
| MLS from C3 to C2 for the invoice | `…MLS-Invoice-C3-C2.xml` | **AB** (delivered without confirmation) |

Notes for the pilot team:

* The invoice UUID in both TDDs follows ID-BDID-01 over the seller identifier (BT-29 `9876-3333333`), BT-3, BT-1 and BT-2.
* Our C3 returns MLS status AB for a validated invoice; AP on C4 acknowledgement is planned.
* The AS4 receipt was returned later than the sender's timeout, so the invoice was received three times; only the first buy-side TDD is included here. A fix is in progress on our side.
