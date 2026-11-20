# Arratech (PSE000778) — test run `EP001-1112-1515`

Executed **2026-11-12 15:15 UTC** on Arratech's production platform against the **Peppol test** network. Arratech is C2. C3 is the Playground's always-rejecting receiver `9913:001110-vidapilot.rejecting-c3`.

* **Scenario:** NW-EP-001, all 42 published invoices
* **TDD v1.1**: invoice UUID over BT-31 (TDT-017), `TransmissionUUID` (TDT-018) present
* **Customisation**, per this repository's usage notes: the seller endpoint `001109` replaced with our own `000778`, the buyer endpoint left as `001110-vidapilot.rejecting-c3`, and the test-run id appended to the invoice number (BT-1)
* **Tax authorities:** AT `0242:001067` · BE `0242:001104` · DK `0242:000152` · FI `0242:001111` · IE `0242:000971` · NO `0242:001111` · SE `0242:001111`

## Result

| Step | Result |
|---|--:|
| Invoice delivered to C3 (AS4) | 42 / 42 |
| MLS from C3 on the invoice: `RE`, status reason `FD` | 42 / 42 |
| C2 TDD (`S`) sent to C5A | 42 / 42 |
| MLS from C5A on the TDD: `AP` | 42 / 42 |
| C3 TDD | none, as the scenario expects |

C2 TDDs by tax administration, all accepted: AT 6 · BE 6 · DK 6 · FI 6 · IE 6 · NO 6 · SE 6.

## Not covered

* **TDD `D` (Disregard) after the `RE`.** The scenario lists this step under "out of scope of Testing Scenario". We do not send it yet: the `S` stays filed at C5A for all 42 invoices.

## Observation

For 3 of the 42 TDDs (2 to BE, 1 to AT), C2 received the same C5A MLS twice: same MLS `cbc:ID`, same SBDH InstanceIdentifier. Both copies are `AP`.

## Sample

AT-BE, under `NW-EP-001/results/EP001-1112-1515/`:

* the invoice as sent
* C2's TDD
* the C3 MLS (`RE` / `FD`)
* the C5A MLS on the TDD (`AP`)
