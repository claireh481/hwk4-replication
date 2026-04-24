# hwk4 — Hospital Pricing & Medicare Pay-for-Performance
This homework studies how Medicare penalty programs (HRRP and VBP) affected hospital prices, drawing on HCRIS cost report data. The empirical strategy moves from simple OLS to instrumental variables to address the endogeneity of penalty status.

## How the Folder Is Organized

```
hwk4/
├── data/
│   └── output/
│       └── HCRIS_Data.txt          ← final combined dataset (built in submission1)
├── submission1/
│   ├── H1_HCRISv1996.ipynb         ← step 1: process v1996 cost reports
│   ├── H2_HCRISv2010.ipynb         ← step 2: process v2010 cost reports
│   ├── combined_hcris.ipynb        ← step 3: merge into one file
│   ├── submission1.ipynb           ← analysis notebook (R kernel)
│   ├── kim-s-hwk4-1.pdf
│   └── submission1.qmd
├── submission2/
│   ├── submission2.ipynb           ← analysis notebook (R kernel)
│   ├── kim-s-hwk4-2.pdf
│   └── submission2.qmd
├── submission3/
│   ├── submission3.ipynb           ← analysis notebook (R kernel)
│   ├── kim-s-hwk4-3.pdf
│   └── submission3.qmd
└── README.md
```

## Building the Data (Start Here)

Before running any analysis, you need to construct the HCRIS dataset. All three build scripts live in `submission1/` and must be run in this order:

1. **`H1_HCRISv1996.ipynb`** — processes cost reports filed under the older v1996 format
2. **`H2_HCRISv2010.ipynb`** — processes cost reports filed under the newer v2010 format
3. **`combined_hcris.ipynb`** — merges both into a single file, handling overlapping years with a weighted average

Once complete, the analysis notebooks read the result from:
```r
final.hcris <- read_tsv("../data/output/HCRIS_Data.txt")
```

## Data Cleaning & Variable Construction

Once `HCRIS_Data.txt` is loaded, the notebook runs several cleaning and construction steps before any analysis:

**Price estimation** — estimated price per discharge is calculated from cost report variables using a standard formula. Observations with negative prices are dropped, and the sample is trimmed at the 1st and 99th percentiles to remove outliers.

**Price change** — the primary outcome variable, computed as the difference in estimated price between 2011 and 2014.

**Net penalty** — calculated as the HVBP payment adjustment minus the HRRP penalty. A negative value indicates the hospital was a net loser under the combined programs.

**Pre-period averages** — for each hospital, means of Medicare discharges, Medicaid discharges, and bed size are computed over 2009–2011. These serve as controls in OLS and as the basis for the IV instrument.

```r
# Price change
price_change = est_price_2014 - est_price_2011

# Net penalty
net_penalty = hvbp_payment - hrrp_penalty

# Pre-period averages (2009–2011)
mean_beds  = mean(beds, na.rm = TRUE)
mean_mcaid = mean(medicaid_discharges, na.rm = TRUE)
mean_mcare = mean(medicare_discharges, na.rm = TRUE)
```

## What the Analysis Does

Each submission notebook covers 10 problems. Before getting to them, a few derived variables are constructed from the raw cost report data:

- **est_price** — estimated price per discharge, trimmed at the 1st and 99th percentiles
- **price_change** — change in estimated price between 2011 and 2014
- **net_penalty** — HVBP bonus minus HRRP penalty (negative = net penalized)
- **pre-period averages** — hospital-level means for beds, Medicare discharges, and Medicaid discharges over 2009–2011

From there, the notebooks work through:

```
HCRIS_Data.txt
      │
      ▼
  Clean & derive variables
  (est_price, price_change, net_penalty, pre-period means)
      │
      ├──► Q1  — How many hospitals file multiple reports in the same year?
      │
      ├──► Q2  — How many unique hospital IDs appear each year?
      │
      ├──► Q3  — How have total charges trended from 2009–2019?
      │
      ├──► Q4  — What does the distribution of estimated prices look like?
      │
      ├──► Q5  — What share of hospitals faced HRRP/VBP penalties each year?
      │
      ├──► Q6  — OLS: does net penalty predict price changes? (3 specs)
      │
      ├──► Q7  — Does pre-2012 Medicare volume correlate with penalty size?
      │
      ├──► Q8  — First stage and reduced form for the IV strategy
      │
      ├──► Q9  — IV estimates of penalty effect on price changes
      │          (instrument: pre-2012 Medicare discharge share)
      │
      └──► Q10 — What does the LATE represent, and how does it differ from ATE?
```

## Notable Code

**Loading packages:**
```r
if (!require("pacman")) install.packages("pacman")
pacman::p_load(tidyverse, ggplot2, dplyr, lubridate, haven, knitr, kableExtra, performance)
```

**OLS specifications (Q6):**
```r
lm(price_change ~ net_penalty, data = ...)
lm(price_change ~ net_penalty + mean_beds, data = ...)
lm(price_change ~ net_penalty + mean_beds + mean_mcaid, data = ...)
```

**IV approach — manual 2SLS (Q8–9):**
```r
# First stage: predict penalty from Medicare share
first_stage <- lm(net_penalty ~ medicare_share_pre, data = ...)

# Second stage: use fitted penalty to explain price changes
lm(price_change ~ fit_net_penalty + mean_beds + mean_mcaid, data = ...)
```

## Setup

Requires R ≥ 4.0 with an IRkernel installed (for Jupyter) or RStudio. All packages can be installed at once:

```r
install.packages("pacman")
pacman::p_load(tidyverse, ggplot2, dplyr, lubridate, haven, knitr, kableExtra, performance)
```

Data files should be in `../data/output/` relative to whichever submission notebook you're running.
