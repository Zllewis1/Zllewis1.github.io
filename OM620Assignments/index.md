# OM 620 Assignments Index — Transaction Data Cleaning & Safety Stock Analysis

This subpage showcases some of the the things I have learned and accomplished during my time in OM620, there are guides for the data what some of the values are and what I have learned as well

1. **Assignment 1 — Data cleaning and inspection:** preparing the raw transaction data for analysis.
2. **Assignment 2 — Safety stock:** calculating safety stock for Make-to-Stock (MTS) SKUs at three service levels.

Both assignments are combined in a single notebook, so the full understanding of what we did and how it was done can be read by everyone from top to bottom

---

---

## Purpose

- **Preparation of reliable data**
   - This notebook shows how to turn raw data with different column names, errors in lead times, and missing values and cleans it so that it can be later analyzed

- **Documenting quality decisions**
   - Each decision made comes with comments displaying the reasons why each decision was made so others can understand why things were done a certain way

- **Building a reproducable workflow**
   - This notebook is built from raw data then cleaned and the final results are then shown, the codes in this notebook can be reproduced by anyone to get the same outputs

- **Calculate saftey stock for MTS items**
   - This notebook shows how to turn "Make to Stock" products or held invventory into saftey stock by turning transaction history into sku statistics and then applying the saftey stock formula at varrying levels

- **Support inventory decisions**
   - This notebook shows how I have learned how to make decisions relateed to saftey stock and making different inventory decisions

- **Show learned skills**
   - This notebook shows some of the different skills that has been learned overtime in this course it uses realistic data to shows skills that can translate to the workforce

- **E-Portfolio**
   - This notebook and folders lets someone view my work see what was done and as well as what I have learned and how they could reproduce it as well

---

## Repository Setup

```
.
├── README.md
├── Data/
│   ├── transaction_data.csv      # Raw transaction data 
│   ├── meta_data.csv             # definitions for the dataset
│   └── prepared_data.csv         # Cleaned data from notebook 1
└── Notebooks/
    └── assignments_1_and_2_combined.ipynb
```

---

## Data Info

The transaction data includes the following column headers:

| Column | Description |
|---|---|
| `sku_number` | Individual products |
| `inventory_type` | Type of inventory |
| `stocking_type` | How the item is stocked (MTS = Make-to-Stock, MTO = Make-to-Order) |
| `lead_time` | How long replinishment takes in days |
| `unit_price` | Price per unit |
| `manufacturing_site` | Site codes for each manufacturing sites|
| `division_code` | codes for each Division |
| `transaction_date` | Date of each transaction |
| `order_quantity` | # of Units ordered |

---

## Part 1 — Assignment 1: Data Cleaning & Inspection

**Goal:** Inspect and clean the raw transaction data so it is usable for downstream inventory analysis.

**What was done**

- **Column names:** Renamed columns to be similar to the ones in meta data.
- **What we found using `.describe()`:**
  - *Lead time:* Typical values are 14, 21, and 28 days, but the maximum was 2,800 days. These outliers looked like values multiplied by 100, so they were divided by 100 to match the rest of the data.
  - *Unit price:* 10 negative prices were found, mostly on transactions with positive values, which suggests there was an error inputing the data. They were converted to absolute values. 
  - *Order quantity:* 40,630 negative values were found. These could be returns, cancellations, or inventory adjustments, so they were **kept unchanged** rather than changing valid records.
- **Missing values:**

  | Column | Missing values | The Fix for it |
  |---|---|---|
  | `sku_number` | 5,238 | Rows dropped  |
  | `inventory_type` | 19,443 | Filled with `"Unknown"` |
  | `stocking_type` | 9,900 | Filled with `"Unknown"` |
  | `manufacturing_site` | 153,569 | Filled with `"Unknown"` |
  | `division_code` | 55,571 | Filled with `"Unknown"` |

  After cleaning, no column had missing values, so we filled it with `"Unknown"` rather than guessing. this helps us keep the transactions the same without assigning the wrong class to different things.
- **Sanity checks:** Counted unique SKUs, manufacturing sites, and divisions; listed the top and bottom 10 transactions by order quantity; and created a `total_sales` column (`order_quantity × unit_price`) to find the top and bottom 10 transactions by sales value.

**Key results from the cleaned dataset**

- 612 unique SKUs
- 19 known manufacturing sites (plus "Unknown")
- 54 known divisions (plus "Unknown")

---

## Part 2 — Assignment 2: Safety Stock Analysis

**Goal:** Calculate safety stock for MTS SKUs, since these items are held in inventory and need a bit of a bubble against demand uncertainty.

**How we did it**

1. Loaded the cleaned dataset from Part 1 (`prepared_data.csv`).
2. Filtered to Make-to-Stock transactions (`stocking_type == "MTS"`).
3. Grouped by SKU and calculated the minimum, maximum, mean, median, variance, and standard deviation of order quantity, plus average lead time.
4. Calculated Z-scores with `scipy.stats.norm.ppf()` for three different service levels:

   | Service level | Z-score |
   |---|---|
   | 75% | 0.6745 |
   | 90% | 1.2816 |
   | 95% | 1.6449 |

5. Calculated safety stock per SKU with:

   ```
   Safety Stock = Z × standard deviation(order quantity) × Square root(average lead time)
   ```

**Results at the 95% service level** (as produced by the notebook's current code)

| Rank | SKU | Safety stock |
|---|---|---|
| Highest | `53767219` | 372.58 |
| Lowest | `9BC2A5C4` | 52.37 |
| Average across all SKUs | — | 141.61 |

The transformed SKU table contains 482 SKUs with no missing values. Safety stock varies across the many different SKUs, and overall is driven by differences in demand and lead time.

---

## How to Run

1. Clone the repository and place `transaction_data.csv` and `meta_data.csv` in the `Data/` folder.
2. Open VS Code and install the Jupyter Notebook Extension and Python Extension
3. Install the different packages needed:
   ```
   pip install pandas numpy scipy 
   ```
4. Move your Notebooks to a custom directory in order to run your code and notebooks effeciently as possible
5. Run the notebook as shown

**Tools used:** Python, pandas, NumPy, SciPy, VScode(Jupyter Notebook Extension)

---

--- 

## What was accomplished in the Assignments 

| Assignment 1 |
| --- |
| Renamed the columns of the data to match the meta data |
| Inspected columns and found things that were out if place | 
| Fixed the different missing values in each column |
| Counted SKU's,Manufacturing sites, and Divisions |
| Found the top and bottom 10 transactions by quantity |
| Created a total_sales column and found the top and bottom 10 transactions by sales value |

| **Assignment 2** |
| --- |
| Using the cleaned data from Assignment 1 it was filtered to MTS items |
| Grouped the data by SKU's and calculated the Range, Mean, Median, Variance, and Standard Deviation |
| Calculated the Z-scores for 75%,90%, and 95% service levels |
| Applied to saftey stock formula to every SKU at all three service levels |
| Identified the saftey stock and average at a 95% service level |
