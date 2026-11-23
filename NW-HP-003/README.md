# NW-HP-003

Files `NW-HP-003.AA-AA.PeppolBIS.xml` implement a basic testing scenario for a **domestic supply** of goods and services with various VAT rates, categories and exemptions.

This pilot test scenario simply applies the ViDA reporting rules as if they were national law. Actual domestic legislation may deviate from this in various ways, for example:
* Reporting obligation for domestic transactions may be limited to the seller-side only.
* A different set of extracted data, up to a full copy of the transacted invoice, may be required.


`AA-AA` in the filename indicates the tax jurisdictions of the seller and buyer, respectively.
For this domestic scenario, the code is always identical.

## Purpose of the test scenario

This test scenario serves to execute the basic **network "happy path"** of a **domestic invoice** as if the ViDA reporting obligation applied in the same way as for intra-community transactions.  
The motiviation is to show the resulting tax data as produced from a process that is fully compatible with the intra-community reporting for all economic operators.

The scenario simulates a standard domestic transaction within a tax jurisdiction. The invoice contains multiple line items with various VAT categories, rates and exemptions. This constellation demonstrates the handling of multiple VAT rates in the **VAT Breakdown** summary of the invoice and the corresponding Tax Data Document.

Except for Sweden, where the tax rates are enforced through validation rules, the generic sample files use "20%" standard rate and "10%" reduced rate for simplicity.

The invoice uses the Peppol BIS Billing 3.0 profile.

## Details of the test scenario

### The Participants
*   **Sellers (Accounting Supplier Party):** are available from all participating countries and indicated by the first country code in the filename (`AA`).
*   **Buyer (Accounting Customer Party):** are available from all participating countries and indicated by the first country code in the filename (`AA`).
*   **Context:** The parties are registered in the same EU/EEA member state so the transaction falls into domestic tax jurisdiction. The chargeable event is a supply of mixed goods and services, intentionally representing multiple VAT situations.

### VAT Character of Line Items
The invoice contains five line items:
*   **Line Item 1:** 3 units of "Great Good" at 102 EUR each (Total: 306 EUR), taxed at the standard rate.  
    A (symbolic) **20% VAT rate** is used with the tax category `S`.
*   **Line Item 2:** 7 units of "Another Great Good" at 87 EUR each (Total: 609 EUR), taxed at a reduced rate.  
    A (symbolic) **10% VAT rate** is used with the tax category `S`.
*   **Line Item 3:** 8 hours of "Super Service" at 123 EUR each (Total: 984 EUR), taxed at the standard rate.  
    A (symbolic) **20% VAT rate** is used with the tax category `S`.
*   **Line Item 4:** 13 units of "postage stamps" at 1,50 EUR each (Total: 19,50 EUR), and exempt from tax.  
    A **0% VAT rate** is used with the tax category `E`.  
    The exemption reason is given in the VAT Breakdown / summary only, and for all `E` line items together.
*   **Line Item 5:** 7 units of "news papers" at 11,13 EUR each (Total: 77,91 EUR), and taxed at 0%.  
    A **0% VAT rate** is used with the tax category `Z`.  


**Key VAT Details:**
*   **Tax Category ID:** `S`, `E`, `Z`  (indicates the VAT category for each item).
*   **Tax Exemption Reason:** The exemption reason code (`BT-121`) remains empty as long as no national codes are registered in the [VATEX code list](https://ec.europa.eu/digital-building-blocks/sites/download/attachments/467108974/VAT%20Exemption%20Reason%20Code%20list%20VATEX%20-%20version%209.xlsx?version=1&modificationDate=1791822083757&api=v2) available in the [EC's Registry of supporting artefacts](https://ec.europa.eu/digital-building-blocks/sites/spaces/DIGITAL/pages/467108974/Registry+of+supporting+artefacts+to+implement+EN16931) .
*   **Total Tax Amount:** The total tax amount for the entire invoice is `318.90` EUR, as the line items are partially exempt.


## Choreography of Transaction

### Outbound / sell-side handling:
* C1 sends invoice data to C2 (not in scope of this test scenario).
* C2 generates the source invoice based on the received data (file `NW-HP-003.AA-AA.PeppolBIS.xml`).
* C2 validates the generated source invoice against applicable schematrons (files `supporting-files/NW-HP-003.AA-AA.PeppolBIS-validation.xml`)
* C2 generates the supply-side TDD (file `sample-results/NW-HP-003.AA-AA.PeppolBIS.TDD-C2.xml`)
* C2 validates the generated supply-side TDD against applicable schematrons (files `supporting-files/NW-HP-003.AA-AA.PeppolBIS.TDD-C2.validation.xml`)

### Transmission process (1/2):

* C2 sends the validated invoice to C3.
* C2 sends the validated supply-side TDD to C5 (according to supplier's VAT ID issuer).   
  Since both parties fall into the same tax jurisdiction, the supplier's C5 is expected to be the same as the seller's C5.


### Inbound / buy-side handling of invoice

* C3 receives inbound invoice from C2 (file `NW-HP-003.AA-AA.PeppolBIS.xml`).
* C3 validates the received invoice against applicable schematrons (files `supporting-files/NW-HP-003.AA-AA.PeppolBIS-validation.xml`)
* C3 generates the buy-side TDD (file `sample-results/NW-HP-003.AA-AA.PeppolBIS.TDD-C3.xml`)
* C3 validates the generated buy-side TDD against applicable schematrons (files `supporting-files/NW-HP-003.AA-AA.PeppolBIS.TDD-C3.validation.xml`)

### Transmission process (2/2):

* C3 sends the validated buy-side TDD to the (same) C5 (according to buyer's VAT ID issuer).   
  For domestic tax reporting this is the same C5 access point as for the seller.

### Inbound / Tax Authority handling of TDD
Inbound TDD handling is same at C5 from C2 and C3.

* C5 receives inbound TDD from C2/C3, files
  * `sample-results/NW-HP-003.AA-AA.PeppolBIS.TDD-C2.xml` resp.
  * `sample-results/NW-HP-003.AA-AA.PeppolBIS.TDD-C3.xml`.
* C5 validates the received TDD against applicable schematrons, files
  * `supporting-files/NW-HP-003.AA-AA.PeppolBIS.TDD-C2.validation.xml` and
  * `supporting-files/NW-HP-003.AA-AA.PeppolBIS.TDD-C3.validation.xml`.

### Message-level status

* C3 sends positive MLS to C2 (file `sample-results/NW-HP-003.AA-AA.PeppolBIS.MLS-C3.xml`)
* C5 sends positive MLS to C2 (file `sample-results/NW-HP-003.AA-AA.PeppolBIS.MLS-C5C2.xml`)
* C5 sends positive MLS to C3 (file `sample-results/NW-HP-003.AA-AA.PeppolBIS.MLS-C5C3.xml`)

### Make results available to C1/C4/C6 (out of scope of Testing Scenario)

* Invoice receiver's side
    * C3 generates and sends invoice data available to C4.
    * C3 makes received invoice and TDD available to C4.
    * C3 makes results as confirmed available to C4
* Invoice sender's side
    * C2 makes generated invoice and TDD available to C1.
    * C2 makes received outcome of transmissions available to C1.
* Tax Administration's side
    * C5 makes received TDD available to C6.
    * C5 makes results as confirmed available to C6
    * As the same C6 receives both sell-side and buy-side TDDs, data analysis matching the TDDs is possible.   
    The **Invoice UUID** (`TDT-017`) can be used as correlation criteria to find matching records.