# Revenue Report Reconfiguration Pipeline

> **⚠️ DISCLAIMER:** All datasets used in this project are completely dummy/random data created solely for demonstration purposes. No organization's confidential, proprietary, or real patient data is used anywhere in this code or examples. Any resemblance to actual persons, organizations, or data is purely coincidental.


## Background

In a hospital environment, revenue reports are generated from the **Hospital Information System (HIS)** portal, while all other accounting reports are created inside the **Oracle Fusion** database. Revenue data is interfaced from HIS to Fusion.

However, the two systems treat multi‑payer bills differently:

- **HIS** stores a single bill row with two payer/amount pairs:  
  `Payer Name1` / `Bill Amount1` and `Payer Name2` / `Bill Amount2`.
- **Oracle Fusion** expects such cases as **two separate bills**:  
  - The first bill remains unchanged (original bill number).  
  - The second bill gets the same bill number with a **`-MP` suffix** (multiple payers), using Payer2 and Amount2.

To align HIS revenue reports with Fusion accounting reports, this pipeline **reconfigures** the raw HIS data into the Fusion‑compatible format.

## What This Code Does

1. **Reads** all Excel files from a specified folder.
2. **Consolidates** multiple sheets/files into a single pandas DataFrame.
3. **Identifies** bills that have a second payer (Bill Amount2 > 0).
4. **Transforms** those rows:
   - Creates a new row with `-MP` suffix in the bill number.
   - Swaps the payer and amount columns so that Payer2 becomes Payer1 and Amount2 becomes Amount1.
   - Sets the original Amount1 to zero (since the first bill is now separate).
5. **Merges** the original bills (with Amount2 = 0) and the newly created `-MP` bills.
6. **Drops** the now‑unused `Payer Name2` and `Bill Amount2` columns.
7. **Exports** the final reconfigured data to an Excel file with a timestamp.

## Input Requirements

- All source Excel files must be placed in a single folder.
- Each file should have the exact column structure (order may vary, but names must match):

| Unit | BILLNO | BILLDATE | LOCATIONID | PATIENTSERVICE | Payer Name1 | BILLINGTYPENAME | PATIENT NAME | DOCTORNAME | Bill Amount1 | Payer Name2 | Bill Amount2 |

- `BILLDATE` may contain "AM"/"PM" strings – the script automatically cleans them.
- Missing `Bill Amount2` values are treated as `0`.

## Output

One Excel file per run, named:  
`Consolidated MM Revenue with MP Cases_YYYY-MM-DD HH-MM-SS.xlsx`

The output file contains only the following columns (Fusion‑compatible):
