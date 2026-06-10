# Revenue Report Reconfiguration Pipeline

## Overview

In our organization, revenue data originates from the Hospital Information System (HIS), while accounting and financial reports are generated from Oracle Fusion. Revenue transactions are interfaced from HIS to Oracle Fusion for accounting purposes.

A challenge arises because HIS and Oracle Fusion represent multi-payer bills differently:

* In the HIS revenue report, a bill can have:

  * **Payer 1** and **Bill Amount 1**
  * **Payer 2** and **Bill Amount 2** (optional)

* For single-payer bills, only Payer 1 and Bill Amount 1 contain values.

* For multi-payer bills, the bill amount is split between two payers:

  * Payer 1 contributes Bill Amount 1.
  * Payer 2 contributes Bill Amount 2.

However, Oracle Fusion does not store these as a single bill. Instead, it creates:

* One invoice using the original bill number for Payer 1.
* A separate invoice for Payer 2 using the same bill number with the **"-MP"** suffix.

Example:

| HIS Bill Number          | Oracle Fusion Bill Number |
| ------------------------ | ------------------------- |
| AH-HYD-IP-1025           | AH-HYD-IP-1025            |
| AH-HYD-IP-1025 (Payer 2) | AH-HYD-IP-1025-MP         |

Because of this structural difference, direct reconciliation between HIS revenue reports and Oracle Fusion accounting reports becomes difficult.

## Project Objective

This project transforms and reconfigures the raw HIS revenue report into a format that aligns with Oracle Fusion accounting reports.

The pipeline:

1. Reads revenue report files from a specified folder.
2. Consolidates all source files into a single DataFrame.
3. Identifies bills containing multiple payers.
4. Splits multi-payer records into separate rows.
5. Creates a new bill record for the secondary payer using the **"-MP"** suffix.
6. Preserves all relevant bill, patient, doctor, unit, and service information.
7. Generates a standardized output file that can be directly correlated with Oracle Fusion reports.

## Transformation Logic

### Input (HIS Format)

| Bill No        | Payer 1     | Amount 1 | Payer 2            | Amount 2 |
| -------------- | ----------- | -------- | ------------------ | -------- |
| AH-HYD-IP-1025 | Medi Assist | 91956    | Family Health Plan | 30652    |

### Output (Oracle Fusion Aligned Format)

| Bill No           | Payer Name         | Bill Amount |
| ----------------- | ------------------ | ----------- |
| AH-HYD-IP-1025    | Medi Assist        | 91956       |
| AH-HYD-IP-1025-MP | Family Health Plan | 30652       |

This transformation ensures that every payer contribution is represented as an individual bill record, matching Oracle Fusion's accounting structure.

## Features

* Automated processing of multiple revenue files.
* Consolidation of source data into a unified dataset.
* Detection of multi-payer (MP) cases.
* Automatic creation of "-MP" bill records.
* Oracle Fusion-compatible output structure.
* Excel export for downstream reporting and reconciliation.
* Reduced manual effort during revenue-accounting validation.

## Technologies Used

* Python
* Pandas
* NumPy
* Jupyter Notebook
* OpenPyXL

## Output

The final output is an Excel file containing revenue data restructured to match Oracle Fusion invoice patterns, enabling seamless reconciliation between operational revenue reports and accounting reports.

## Business Impact

* Improves accuracy of revenue reconciliation.
* Eliminates manual bill splitting activities.
* Standardizes reporting across systems.
* Enables faster financial validation and audit processes.
* Provides a consistent bridge between HIS and Oracle Fusion reporting structures.
