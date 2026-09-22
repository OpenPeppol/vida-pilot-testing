This is the test data home directory for PSE000778 - Arratech

## Testbed reports

Official OpenPeppol Testbed reports for the ViDA Pilot specification test suites,
in `testbed-reports/`:

| Test Suite | Profile | Result | Completed | Report |
|---|---|---|---|---|
| [id=53] Billing 3.0.20 | Specification Testing (C2 C3) | 5/5 COMPLETED | 2026-06-24 | `TestBedReport-PSE000778-20260624T091740.pdf` |
| [id=54] Self-Billing 3.0.1 | Specification Testing (C2 C3) | 5/5 COMPLETED | 2026-06-25 | `TestBedReport-PSE000778-20260625T063006.pdf` |
| [id=55] TDD 1.0.0-HotFix | Specification Testing (C5) | 4/4 COMPLETED | 2026-06-25 | `TestBedReport-PSE000778-20260625T143213.pdf` |

- Endpoint under test: `https://ap.arratech.com/as4`
- C2/C3 receiver (SUT): `0007:5599999991` (SE:ORGNR)
- C5 receiver: `0242:000778-vida` (SPIS)
- Certificate: PEPPOL ACCESS POINT TEST CA - G3

## Test run results

| Run | Scenario | Date | Outcome |
|---|---|---|---|
| `ARR-20260910-1310-03` | NW-HP-001 FI-AT (both corners Arratech) | 2026-09-10 | TDDs accepted by C5(A) and C5(B) with AP; same invoice UUID on both; C3→C2 MLS is AB. See `NW-HP-001/results/`. |

## Contact

Arratech — agenttwo@arratech.com
