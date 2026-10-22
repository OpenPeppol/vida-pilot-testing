
# C2/C3-Minimum Tax Data Documents

The two files ending with `‑MANUAL` in this directory were created by hand from the corresponding **TDD‑C3.xml** sample.

- The two manual files represent the **C2** and **C3** perspective of the tax data document.  
- All non‑mandatory fields have been commented out, leaving only the mandatory elements for minimal reporting 

These files provide a clear representation of the required structure from each perspective while relating to the original XML from the TDD C3 (full) sample for reference.

## Differences

### C2 View versus C3 view

Compared to C3, C2 is reduced by the following items:

* **BT-110**, **BT-117**, **BT-112**, **BT-115**  
  are all amounts that include a VAT Tax amount. Therefore, C2 does not need to report this.
* **BT-119**, **BT-152** are the corresponding VAT rates (in percent).  
  C2 does not need to report these.

In reverse, the C3/Buy-side report contains these data values.


### C2/C3 minimum view compared to original (full) file

Additionally, the following (optional) data values were removed from both C2/C3 as compared to "full" C3:

* **TDT-011**: Tax Authority Name (optional)
* **BT-021**: Invoice Note (optional)
* **BT-044**: Buyer name (only VAT identifier is mandatory)
* **BT-040**, **BT-055**: Seller and Buyer Country code (all address data is optional, only VAT identifier is mandatory)
* `cac:TaxScheme` in `cac:AccountingSupplierParty` and `cac:AccountingCustomerParty` (only technical reasons in UBL invoice)
* `cac:TaxScheme` in `cac:ClassifiedTaxCategory` on line item level (only technical reasons in UBL invoice)


## Supporting Files

The files contained in `supporting-files` directory
* visualize the TDDs (HTML and PDF)
* Show the validation report (currently failing on XSD schema validation already)

