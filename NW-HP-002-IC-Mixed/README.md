# NW-HP-002-IC-Mixed

File `NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.xml` implements a testing scenario for a transaction with both **Intra-community supply (ICS)** of goods and delivery of services under **Reverse Charge**, where the VAT rate indicated is `0%` because the tax responsibility is shifted to the buyer.

The scenario files use **Tax Category ID: `K`**, defined as "VAT exempt for EEA intra-community supply of goods and services - 
a tax category code indicating the item is VAT exempt due to an intra-community supply in the European Economic Area."
for all line items regardless of their type.

> [!IMPORTANT]
> Note that this usage follows the [Technical Guidance document on Tax Codes for Electronic Invoicing](https://ec.europa.eu/digital-building-blocks/sites/spaces/DIGITAL/pages/467108974/Registry+of+supporting+artefacts+to+implement+EN16931#RegistryofsupportingartefactstoimplementEN16931-TechnicalGuidancedocumentonTaxCodesforeInvoicing), which recommends using `K` for any intra-community supply of goods or services.  
> As a result, the **VAT Breakdown** contains only one line with the totals for all line items.

`AA-BB` in the filename indicates the two tax jurisdictions of the seller and buyer, respectively.


## Purpose of the test scenario

This test scenario serves to execute the basic **network "happy path"** for a mixed **Intra-community supply (ICS)** of goods and **service-based invoice** involving reverse charge mechanisms.

The scenario simulates a standard B2B transaction within the ViDA framework where a seller provides goods and services to a buyer located in a different member state. Because the delivery of (physical) goods is an intra-community supply and invoicing of services is subject to the reverse charge mechanism, the seller does not charge VAT, and the tax responsibility is shifted to the buyer.  
The document uses the Peppol BIS Billing 3.0 profile.

## Details of the test scenario:

### The Participants
*   **Sellers (Accounting Supplier Party):** are available from all participating countries and indicated by the first country code in the filename (`AA`).
*   **Buyers (Accounting Customer Party):** are available from all participating countries and indicated by the first country code in the filename (`BB`).
*   **Context:** The parties are registered in different EU member states, and the invoice covers services where the reverse charge mechanism applies.

### VAT Character of Line Items

The invoice (and TDD) contain a total of three line items.

The first two line items, both characterized by a **0% VAT rate** use the specific tax category **"K"**:
*   **Line Item 1:** 16 units of "Super Service" at 124 EUR each (Total: 1984 EUR).
*   **Line Item 2:** 32 units of "More Super Service" at 124 EUR each (Total: 3968 EUR).

**Key VAT Details:**
*   **Tax Category ID:** `K` (indicates the ICS of goods and services category).
*   **Tax Exemption Reason:** `VATEX-EU-IC` (indicates that ICS applies).
*   **Total Tax Amount:** The total tax amount for these line items is `0` EUR, as the supply is exempt under ICS.

The invoice contains another line item, characterized by a **0% VAT rate** using the specific tax category **"K"**:
*   **Line Item 3:** 3 units of "Great Good" at 102 EUR each (Total: 306 EUR).

**Key VAT Details:**
*   **Tax Category ID:** `K` (indicates the VAT categories).
*   **Tax Exemption Reason:** `VATEX-EU-IC` corresponding to ICS `K` items.
*   **Total Tax Amount:** The total tax amount for this line item is `0` EUR, as the supply is exempt under ICS.


### VAT Breakdown

As a result, the **VAT Breakdown** in the invoice (and TDD) contain one entry:
* **VAT Category:** `K`, with a total taxable amount of `6.258,00` EUR for delivery of goods and services.
* **Exemption Reason** covers both cases of goods and services: *VAT exempt intra-community supply (Article 138 Directive 2006/112/EC) and services under reverse charge following Art. 196 of Directive 2006/112/EC*
* **Total Tax Amount:** The total tax amount for the entire invoice is `0` EUR, as the supplies of goods and services are exempt in the jurisdiction of the invoice issuer.


## Choreography of Transaction

### Outbound / sell-side handling:
* C1 sends invoice data to C2 (not in scope of this test scenario).
* C2 generates the source invoice based on the received data (file `NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.xml`).
* C2 validates the generated source invoice against applicable schematrons (files `supporting-files/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS-validation.xml`)
* C2 generates the supply-side TDD (file `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C2.xml`)
* C2 validates the generated supply-side TDD against applicable schematrons (files `supporting-files/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C2.validation.xml`)

### Transmission process (1/2):

* C2 sends the validated invoice to C3.
* C2 sends the validated supply-side TDD to C5 (according to supplier's VAT ID issuer).

### Inbound / buy-side handling of invoice

* C3 receives inbound invoice from C2 (file `NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.xml`).
* C3 validates the received invoice against applicable schematrons (files `supporting-files/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS-validation.xml`)
* C3 generates the buy-side TDD (file `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C3.xml`)
* C3 validates the generated buy-side TDD against applicable schematrons (files `supporting-files/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C3.validation.xml`)

### Transmission process (2/2):

* C3 sends the validated buy-side TDD to C5 (according to buyer's VAT ID issuer).

### Inbound / Tax Authority handling of TDD
Inbound TDD handling is same at C5A from C2 and at C5B from C3.

* C5 receives inbound TDD from C2/C3, files
  * `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C2.xml` resp.
  * `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C3.xml`.
* C5 validates the received TDD against applicable schematrons, files
  * `supporting-files/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C2.validation.xml` and
  * `supporting-files/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.TDD-C3.validation.xml`.

### Message-level status

* C3 sends positive MLS to C2 (file `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.MLS-C3.xml`)
* C5A sends positive MLS to C2 (file `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.MLS-C5A.xml`)
* C5B sends positive MLS to C3 (file `sample-results/NW-HP-002-IC-Mixed.AA-BB.PeppolBIS.MLS-C5B.xml`)

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