# Airbnb Listings — Data Cleaning & Exploratory Data Analysis

Exploratory data analysis of a global Airbnb listings dataset spanning **10 cities across 8 countries**, covering data cleaning, feature engineering, statistical relationship analysis, and business insights.

## Contents

- `Airbnb_Listings_EDA.ipynb` — main notebook (Google Colab)

## Dataset

- Source file: `Listings.csv`[https://www.kaggle.com/datasets/ulrikthygepedersen/airbnb-listings?resource=download] (loaded via Google Drive in Colab)
- Cities covered: Paris, Rome, New York, Sydney, Istanbul, Rio de Janeiro, Hong Kong, Mexico City, Bangkok, Cape Town
- Prices are in local currency by city; converted to a `price_usd` column using fixed approximate exchange rates (see [Limitations](#limitations))

## What the notebook does

### 1. Data Cleaning (Parts 1–7)
- **Missing values** — column-by-column treatment with documented reasoning (drop, mode/median fill, group-level fill with fallback), plus a threshold-sensitivity check for the neighbourhood-mean imputations and a full transparency/imputation summary table
- **Duplicates** — checked at row and `listing_id` level (none found)
- **Data types** — `host_since` corrected from object to datetime
- **Invalid values** — removed listings with price ≤ 0 and `minimum_nights > maximum_nights`
- **Inconsistent categories** — whitespace/casing standardized across `city`, `property_type`, `room_type`
- **Currency normalization** — local-currency prices mapped and converted to `price_usd`
- **Outliers** — IQR method on price, validated statistically (Mann-Whitney U test against review scores and amenity counts) rather than dropped on sight, since they turned out to be genuine premium listings
- **Feature engineering** — parsed the raw `amenities` text column into a list, an `amenities_count`, and boolean flags for key amenities (wifi, kitchen, parking, pool, AC, washer); flagged `maximum_nights` placeholder/"unlimited" values

### 2. Exploratory Data Analysis (Parts 4–5)
- Numerical summaries (price, accommodates, nights, review scores)
- Categorical frequency tables (city, room type, property type)
- Correlation matrix and targeted relationship checks (price vs. rating, price vs. acceptance rate)
- Grouped summaries and pivot tables (price by city/room type, acceptance by superhost status)
- Visualizations: price histogram, box plot, bar chart of average price by city, accommodates-vs-price scatter, room type count plot

### 3. Key Insights (Part 6)
- **Superhost effect is context-dependent, not a universal premium.** Globally, superhosts price ~8.5% *lower* than non-superhosts, but this reverses by market — up to a +25.9% premium in Mexico City vs. a -30% discount in Rio. The consistent pattern is that superhosts have 5–11% higher acceptance rates, suggesting they compete on volume rather than price.
- **Amenities are a weak but real price lever** (Spearman ρ = 0.105, p < 0.001), and are a stronger differentiator specifically within the luxury/outlier segment.
- **Price is driven far more by location and property type than by guest-facing quality metrics** — review score, acceptance rate, and accommodates all show weak or negligible correlation with price.
- **Price distribution is heavily right-skewed** (mean $109.76 vs. median $64.80), so median is used as the representative "typical price" throughout rather than mean.
- Outlier (high-price) listings were statistically confirmed as genuine luxury listings rather than data errors, using Mann-Whitney tests on review scores and amenity counts.

See the notebook's **Part 6 – Key Insights** section for the full list of 10 findings and business implications.

## Limitations

- **Mixed currencies**: prices were converted to USD using a single fixed exchange-rate snapshot, not rates matching each listing's actual collection date. Cross-city price comparisons should be read as directional trends, not precise absolute figures. Within-city comparisons (room type effects, superhost premium, etc.) are unaffected.
- **Geographic imbalance**: Paris alone accounts for ~23% of all listings, followed by New York (~13%), so global aggregates are Paris-weighted.
- **`maximum_nights` data quality**: ~64% of listings carry a placeholder "unlimited" value (≥1,125 nights) rather than a real stay limit; this is flagged in a boolean column (`max_nights_unlimited`) and excluded from numeric summaries of `maximum_nights`.

## Requirements

- Python 3
- `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`

## How to run

The notebook was built in Google Colab and mounts Google Drive to load `Listings.csv`. To run locally:
1. Update the `loading_csv(...)` path in the Setup section to point to your local copy of `Listings.csv`, and remove/skip the `drive.mount(...)` cell.
2. Run all cells top to bottom — later sections (feature engineering, EDA, insights) depend on cleaning steps executed earlier in the notebook.
