# Flight Delay Exploratory Data Analysis & Feature Engineering

## ✈️ Project Overview
This project focuses on executing end-to-end Exploratory Data Analysis (EDA) on a dense aviation industry dataset tracking commercial flight records. The primary goal is to assess flight reliability, trace patterns leading to departure/arrival delays, and engineer custom metrics that assist in evaluating airline and flight route performance.

## 📊 Dataset Profile
* **Source:** Commercial flight logs (`flights_final.csv`) containing real-world flight operations metrics.
* **Initial Shape:** 436,228 records across 12 tracking columns.
* **Core Structural Attributes:** * `dep_time` / `sched_dep_time` (Actual vs. Scheduled Departure)
  * `arr_time` / `sched_arr_time` (Actual vs. Scheduled Arrival)
  * `carrier` / `flight` / `tailnum` (Aircraft identity and airline codes)
  * `origin` / `dest` (Flight routing nodes)
  * `air_time` / `distance` (Flight durations and path limits)
  * `time_hour` (Operational timestamp)

---

## 🛠️ Data Pipeline & Engineering Workflow

### 1. Data Cleaning & Diagnostics
* **Duplicate Scrubbing:** Identified and purged `876` duplicated log rows to protect metric aggregation from structural bias.
* **Data Typings Remapping:** Converted `time_hour` from generic string formats (`object`) into native `datetime64` representations for efficient time-series extraction.
* **Missing Value Imputation Strategy:** * Discovered data gaps across numerical metrics (`dep_time`, `arr_time`, `air_time`). Because the data trends didn't show skewed differences between mean and median values, median values were safely imputed.
  * Replaced critical text missing identifiers (`tailnum`) with the statistical mode (most frequent flag).
* **Post-Cleaning Dataset Shape:** Stabilized down to a clean array of `435,352` rows.

### 2. Feature Engineering & Context Synthesis
To enrich the raw fields, several contextual features were engineered:
* **Time-Series Breakdowns:** Segmented date parameters down to `year`, `month`, `day`, `hour`, `minute`, and extracted the descriptive `day_of_week` text matrix.
* **Operational Time Slots:** Categorized flight operations into specific day brackets using the actual flight departure hours:
  * *Morning* (5:00 AM – 11:59 AM)
  * *Afternoon* (12:00 PM – 4:59 PM)
  * *Evening* (5:00 PM – 8:59 PM)
  * *Night* (9:00 PM – 4:59 AM)
* **Route Synthesis:** Constructed explicit routing metrics (`route`) by merging airport directional targets (e.g., `JFK-SFO`).
* **Calculated Flight Delay Metrics:** Created a time calculation framework (`to_minutes`) translating military HHMM formats to standard baseline integers past midnight.
  * Computed custom indices `dep_delay` and `arr_delay`.
  * Accounted for complex midnight cross-over limits by implementing an auto-correction logic (+1,440 minutes for cross-day operations) to ensure overnight flights maintained realistic delay calculations.
* **Delay Stratification Groups:** Standardized delays into a hierarchy metric (`arr_delay_category` & `dep_delay_category`):
  * `On Time/Early` ($\le$ 0 mins)
  * `Slight Delay` (1–15 mins)
  * `Moderate Delay` (16–60 mins)
  * `Long Delay` (61–180 mins)
  * `Very Long Delay` ($>$ 180 mins)

---

## 📈 Key Insights & Analytical Takeaways
* **Time-Slot Dependability:** Early morning flights (5:00 AM – 11:00 AM) regularly show the strongest performance windows, with on-time averages often climbing past 70% to 80% boundary lines.
* **The Late-Night Bottleneck:** Flight performance experiences noticeable degradation inside the Night window (9:00 PM – 4:00 AM), where operational punctuality drops significantly (under 20% limits on highly active sectors).
* **Sector Variability:** High-traffic corridors exhibit varying profiles; specifically, the `JFK-SFO` corridor suffers severe nocturnal performance drop-offs, while `LGA-BOS` and `LGA-ORD` keep higher relative stability in evening schedules.

## 💻 Tech Stack & Environment
* **Language:** Python 3 (Jupyter Notebook environment)
* **Core Data Tools:** `pandas`, `numpy`
* **Visualization Layer:** `matplotlib`, `seaborn`
