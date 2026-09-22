# NW-EP-001

Files `NW-EP-001.AA-BB.PeppolBIS.xml` implement a basic testing scenario for an **Intra-Community Supply (ICS)** of goods, where the VAT rate indicated is 0% because the tax responsibility is shifted to the buyer.
Additionally, the recipient is set to `001110-vidapilot.rejecting-c3`
which causes C2 to always receive an MLS `RE`.

`AA-BB` in the filename indicates the two tax jurisdictions of the seller and buyer, respectively.

## Purpose of the test scenario

This test scenario serves to execute the basic **network "exception path"** of a **cross-border invoice** encountering an MLS `RE`.

The scenario simulates a standard B2B cross-border transaction within the ViDA framework where the seller delivers physical goods to a buyer across their borders. The invoice does not charge VAT because the responsibility for VAT reporting is shifted to the buyer (reverse charge/ICS mechanism).  
The invoice uses the Peppol BIS Billing 3.0 profile.  
The recipient is set to `001110-vidapilot.rejecting-c3`
which causes C2 to always receive an MLS `RE`.

## Details of the test scenario

### The Participants
*   **Sellers (Accounting Supplier Party):** are available from all participating countries and indicated by the first country code in the filename (`AA`).
*   **Buyer (Accounting Customer Party):** are available from all participating countries and indicated by the first country code in the filename (`BB`).
*   **Context:** The parties are registered in different EU/EEA member states and the chargeable event is a supply of goods, the transaction is given as an intra-community supply.
* **Exception Path**: The scenario uses an "always rejecting" C3 provider to trigger exception handling at C2.

### VAT Character of Line Items
The invoice contains two line items, both characterized by a **0% VAT rate** using the specific tax category **"K"**:
*   **Line Item 1:** 3 units of "Great Good" at 102 EUR each (Total: 306 EUR).
*   **Line Item 2:** 7 units of "Another Great Good" at 87 EUR each (Total: 609 EUR).

**Key VAT Details:**
*   **Tax Category ID:** `K` (indicates the VAT categories).
*   **Tax Exemption Reason:** `VATEX-EU-IC`, which explicitly refers to **"VAT exempt intra-community supply (Article 138 Directive 2006/112/EC)"**.
*   **Total Tax Amount:** The total tax amount for the entire invoice is `0` EUR, as the supply is exempt.

Note that deliveries of goods involving Norway (`NO`) are treated as import/export (`G` / `VATEX-EU-G`) as customs is relevant when shipping goods outside and into the borders of the EC.

## Choreography of Transaction

### Outbound / sell-side handling:
* C1 sends invoice data to C2 (not in scope of this test scenario).
* C2 generates the source invoice based on the received data (file `NW-EP-001.AA-BB.PeppolBIS.xml`).
* C2 validates the generated source invoice against applicable schematrons (files `supporting-files/NW-EP-001.AA-BB.PeppolBIS-validation.xml`)
* C2 generates the supply-side TDD (file `sample-results/NW-EP-001.AA-BB.PeppolBIS.TDD-C2.xml`)
* C2 validates the generated supply-side TDD against applicable schematrons (files `supporting-files/NW-EP-001.AA-BB.PeppolBIS.TDD-C2.validation.xml`)

### Transmission process (1/2):

* C2 sends the validated invoice to C3.
* C2 sends the validated supply-side TDD to C5 (according to supplier's VAT ID issuer).

### Inbound / buy-side handling of invoice

* C3 receives inbound invoice from C2 (file `NW-EP-001.AA-BB.PeppolBIS.xml`).
* C3 being the "always rejecting" receiver does not execute any of the standardized behaviour. No buy-side TDD is generated or sent.
* C3 prepares a (fixed) MLS with status code `RE` to be returned later (see below)

### Transmission process (2/2):

* C3 sends the validated buy-side TDD to C5 (according to buyer's VAT ID issuer).

### Inbound / Tax Authority handling of TDD
Inbound TDD handling is same at C5A from C2 and at C5B from C3.

* C5 receives inbound TDD from C2/C3, files
  * `sample-results/NW-EP-001.AA-BB.PeppolBIS.TDD-C2.xml` resp.
  * `sample-results/NW-EP-001.AA-BB.PeppolBIS.TDD-C3.xml`.
* C5 validates the received TDD against applicable schematrons, files
  * `supporting-files/NW-EP-001.AA-BB.PeppolBIS.TDD-C2.validation.xml` and
  * `supporting-files/NW-EP-001.AA-BB.PeppolBIS.TDD-C3.validation.xml`.

### Message-level status

* C3 sends negative MLS to C2 (file `sample-results/NW-EP-001.AA-BB.PeppolBIS.MLS-C3.xml`)
* C5A sends positive MLS to C2 (file `sample-results/NW-EP-001.AA-BB.PeppolBIS.MLS-C5A.xml`)
* C5B is not a participant in this transaction as no TDD is received.

### Make results available to C1/C4/C6 (out of scope of Testing Scenario)

* Invoice receiver's side
    * C3 provides a notification about the failed invoice to C4, usually as part of a "failure log".
* Invoice sender's side
    * C2 makes generated invoice and TDD available to C1.
    * C2 makes received outcome of transmissions available to C1.  
    This is important in order for C1 to be aware that the outbound invoice did **not** receive the intended recipient.
    * C2 generates and sends a new TDD with status "D" (Disregard) to C5A. This is only necessary if C5A has received a prior TDD in parallel to the invoice sending transaction.
    * C2 makes the TDD "D" status available to C1.  
    This is important so that C1 is aware that the tax administration does **not** recognize a record on the failed transaction.
* Tax Administration's side
    * C5 makes received TDD available to C6.
    * C5 makes results as confirmed available to C6
    * C5 makes received TDD "D" available to C6, effectively removing the TDD from further consideration.
    * C5 makes results as confirmed available to C6
