# Uber Trips Analysis Dashboard — Power BI

## 1. Project overview

This project analyses a sample Uber trips dataset using **Microsoft Excel, Power Query Editor, Power BI data modelling, DAX measures and interactive report visuals**.

The objective is to turn trip-level records into an interactive report that helps a viewer understand:

- How many trips were requested and how many were completed or cancelled.
- The cancellation rate and trip-completion performance.
- Fare and trip-distance patterns.
- When demand is higher during the day or across dates.
- Which pickup and drop-off areas appear most frequently.
- Which payment methods customers use.
- How driver and customer ratings are distributed.

> **Dataset note:** The supplied workbook describes the fares and surge multipliers as illustrative sample data. Treat the results as a demonstration/portfolio analysis, not as official Uber operational or financial reporting.

## 2. Dataset information

**Source file:** `Uber_Trips_Dashboard_Dataset.xlsx`  
**Worksheet used:** `Uber_Trips`  
**Supporting worksheet:** `Data_Dictionary`  
**Dataset size:** 1,200 rows and 16 columns  
**Date coverage:** 1 January 2026 to 29 June 2026  
**Geographic context:** Sample Bengaluru pickup and drop-off areas.

### Column descriptions

| Column | Meaning | How it can be used |
|---|---|---|
| `Trip_ID` | Unique trip identifier | Count trips; identify records |
| `Pickup_Date` | Date the trip started | Date trends and date filtering |
| `Pickup_Time` | Pickup time | Time-of-day analysis |
| `Day` | Day name | Weekday comparisons |
| `Month` | Month name | Monthly comparisons |
| `Hour` | Hour of pickup (0–23) | Peak-hour analysis |
| `Pickup_Location` | Trip starting area | Pickup-area analysis |
| `Dropoff_Location` | Trip destination area | Destination analysis |
| `Trip_Distance_KM` | Distance in kilometres | Distance analysis and average distance |
| `Trip_Duration_Min` | Duration in minutes | Duration analysis |
| `Fare_INR` | Sample fare in Indian rupees | Fare and revenue-style measures |
| `Surge_Multiplier` | Illustrative surge multiplier | Surge analysis |
| `Payment_Method` | Payment category | Payment-method split |
| `Trip_Status` | Completed or Cancelled | Completion and cancellation analysis |
| `Driver_Rating` | Driver rating | Average driver rating |
| `Customer_Rating` | Customer rating | Average customer rating |

## 3. Tools and technologies

- **Microsoft Excel:** Source workbook and initial data inspection.
- **Power Query Editor:** Data profiling, cleaning, transformation and type setting.
- **Power BI Desktop — Data view / Model view:** Data validation and modelling.
- **DAX (Data Analysis Expressions):** Measures and calculated aggregations.
- **Power BI Report view:** KPI cards, charts, slicers, formatting and dashboard interactions.

## 4. Workflow — from source data to dashboard

### Step 1 — Import the Excel workbook

1. Open Power BI Desktop.
2. Select **Home → Get data → Excel workbook**.
3. Browse to `Uber_Trips_Dashboard_Dataset.xlsx`.
4. In Navigator, select the `Uber_Trips` worksheet.
5. Select **Transform Data** rather than loading immediately. This opens Power Query Editor.
6. Keep `Data_Dictionary` available as a reference for understanding the column meanings. Load it only if it is useful in the report.

**Why this matters:** Importing through Power Query creates a repeatable preparation process. When the source workbook is updated, the transformations can be refreshed rather than repeated manually.

### Step 2 — Inspect the data in Power Query Editor

Before changing the data, inspect:

- Column names and whether they are meaningful.
- Data types (date, time, whole number, decimal number or text).
- Blank/null values.
- Duplicate records and duplicate trip IDs.
- Inconsistent category spelling or extra spaces.
- Unusual values in distance, duration, fare, ratings and surge multiplier.

Useful Power Query options:

- **View → Column quality**
- **View → Column distribution**
- **View → Column profile**

These profiling options help identify empty, valid and error values. Column profiling can be switched to examine the entire dataset rather than only the preview sample.

### Step 3 — Clean and transform the data

The following are the recommended cleaning checks for this workbook. Keep only the transformations that you actually applied in your Power Query query steps.

1. **Promote headers:** Ensure the first row contains the correct column names.
2. **Rename columns if required:** Use clear, consistent names. Avoid renaming columns after writing measures unless you also update the DAX.
3. **Set data types:**
   - `Trip_ID`, `Day`, `Month`, locations, payment method and trip status → Text.
   - `Pickup_Date` → Date.
   - `Pickup_Time` → Time (if the source values are recognised correctly).
   - `Hour`, `Trip_Duration_Min` → Whole Number.
   - `Trip_Distance_KM`, `Fare_INR`, `Surge_Multiplier`, `Driver_Rating`, `Customer_Rating` → Decimal Number.
4. **Trim and clean text:** Select text columns and use **Transform → Format → Trim** and **Clean** to remove unwanted spaces and non-printing characters. This helps prevent values such as `UPI` and `UPI ` from being treated as separate categories.
5. **Check nulls and errors:** Filter each column for blanks/errors. Decide whether to correct, replace or remove a record based on the business meaning. Do not automatically replace every blank with zero; a missing rating is not a zero rating.
6. **Check duplicates:** Use `Trip_ID` to identify repeated trip identifiers. Remove a duplicate only after confirming that it is an accidental duplicate, not a legitimate separate record.
7. **Validate numeric values:** Check for impossible or suspicious distances, durations, fares, ratings and surge values. Investigate outliers before removing them.
8. **Check category consistency:** Verify that `Trip_Status` contains the expected categories (for example, `Completed` and `Cancelled`) and that payment categories are consistently written.
9. **Review derived time fields:** Check that `Hour` agrees with `Pickup_Time`, and that `Day` and `Month` agree with `Pickup_Date`. If you derive these fields, document which version is used in the report.
10. **Close & Apply:** Select **Home → Close & Apply** to load the prepared query into Power BI.

**Important:** Power Query's Applied Steps pane is the record of the transformations actually performed. This README describes a reproducible cleaning checklist; it does not claim that every listed optional operation was necessarily applied in your PBIX file.

### Step 4 — Validate the prepared data

The supplied source workbook was inspected for this README. Its original `Uber_Trips` sheet contains:

- 1,200 records and 16 columns.
- No blank values in the 16 columns.
- No fully duplicated rows.
- No duplicate `Trip_ID` values.
- 1,092 records with `Completed` status and 108 with `Cancelled` status.
- Pickup dates from 1 January 2026 through 29 June 2026.

Use these as **source-file validation benchmarks**. If your Power Query process removes or changes records, your final loaded row count may differ. Check the Power Query preview and Power BI Data view after cleaning.

### Step 5 — Data modelling

After applying the query:

1. Open **Model view** (the relationship/model icon on the left).
2. Inspect the tables and relationships.
3. For a report built entirely from the `Uber_Trips` table, a single-table model is sufficient; you do not need to create an artificial relationship.
4. If you create a separate Calendar table, relate its date column to `Uber_Trips[Pickup_Date]` using a one-to-many relationship, with Calendar on the “one” side and trips on the “many” side.
5. Mark the Calendar table as the date table if you use it for time intelligence.
6. Confirm that the relationship is active and that the date column has the Date data type.

**Optional Calendar table DAX:**

```DAX
Calendar =
CALENDAR (
    MIN ( Uber_Trips[Pickup_Date] ),
    MAX ( Uber_Trips[Pickup_Date] )
)
```

Useful additional Calendar columns:

```DAX
Year = YEAR ( Calendar[Date] )

Month Number = MONTH ( Calendar[Date] )

Month Name = FORMAT ( Calendar[Date], "MMMM" )

Year-Month = FORMAT ( Calendar[Date], "YYYY-MM" )
```

Sort `Month Name` by `Month Number` using **Column tools → Sort by column**. Otherwise, month names may appear alphabetically. If the report covers more than one year, use a Year-Month field sorted by a numeric year-month key for a correct continuous trend.

### Step 6 — Create DAX measures

Measures calculate results dynamically in response to slicers and visual filters. Create them from **Modeling → New measure**. The examples below assume that the table is named `Uber_Trips` and the column names are unchanged.

#### Trip volume and status

```DAX
Total Trips =
DISTINCTCOUNT ( Uber_Trips[Trip_ID] )
```

Counts unique trip IDs. This is generally safer than counting rows if the data could contain repeated IDs.

```DAX
Completed Trips =
CALCULATE (
    [Total Trips],
    Uber_Trips[Trip_Status] = "Completed"
)
```

Counts trips whose status is Completed.

```DAX
Cancelled Trips =
CALCULATE (
    [Total Trips],
    Uber_Trips[Trip_Status] = "Cancelled"
)
```

Counts trips whose status is Cancelled.

```DAX
Cancellation Rate =
DIVIDE ( [Cancelled Trips], [Total Trips], 0 )
```

Calculates cancelled trips divided by all trips. Format this measure as **Percentage** in Measure tools. Do not multiply by 100 if you use percentage formatting; Power BI handles the display.

```DAX
Completion Rate =
DIVIDE ( [Completed Trips], [Total Trips], 0 )
```

Calculates the proportion of trips completed.

#### Fare and trip performance

```DAX
Completed Fare (INR) =
CALCULATE (
    SUM ( Uber_Trips[Fare_INR] ),
    Uber_Trips[Trip_Status] = "Completed"
)
```

A revenue-style total using only completed trips. This avoids treating cancelled-trip fare values as completed-trip revenue. Because the source describes fares as illustrative, label this as sample completed fare rather than actual Uber revenue.

```DAX
Average Completed Fare (INR) =
CALCULATE (
    AVERAGE ( Uber_Trips[Fare_INR] ),
    Uber_Trips[Trip_Status] = "Completed"
)
```

Average fare per completed trip.

```DAX
Average Trip Distance (KM) =
CALCULATE (
    AVERAGE ( Uber_Trips[Trip_Distance_KM] ),
    Uber_Trips[Trip_Status] = "Completed"
)
```

Average distance for completed trips.

```DAX
Average Trip Duration (Min) =
CALCULATE (
    AVERAGE ( Uber_Trips[Trip_Duration_Min] ),
    Uber_Trips[Trip_Status] = "Completed"
)
```

Average duration for completed trips.

#### Ratings and surge

```DAX
Average Driver Rating =
AVERAGE ( Uber_Trips[Driver_Rating] )
```

```DAX
Average Customer Rating =
AVERAGE ( Uber_Trips[Customer_Rating] )
```

```DAX
Average Surge Multiplier =
AVERAGE ( Uber_Trips[Surge_Multiplier] )
```

These measures calculate averages in the current filter context. If cancelled records should not contribute to a particular rating or surge metric, add a Completed status filter consistently.

#### Optional distinct counts

```DAX
Pickup Areas =
DISTINCTCOUNT ( Uber_Trips[Pickup_Location] )
```

```DAX
Dropoff Areas =
DISTINCTCOUNT ( Uber_Trips[Dropoff_Location] )
```

**Measure formatting suggestions**

| Measure | Suggested format |
|---|---|
| Total Trips / Completed Trips / Cancelled Trips | Whole number |
| Cancellation Rate / Completion Rate | Percentage, 1–2 decimals |
| Completed Fare / Average Completed Fare | Currency (₹), 0–2 decimals |
| Average Trip Distance | Decimal number, 1–2 decimals |
| Average Trip Duration | Decimal number, 1 decimal |
| Average Driver / Customer Rating | Decimal number, 1–2 decimals |
| Average Surge Multiplier | Decimal number, 2 decimals |

### Step 7 — Build the report visuals

Choose visuals that answer a specific question. The descriptions below explain the purpose and interpretation of common visuals for this dataset. Retain only the visuals that are present in your actual report.

#### A. KPI cards — overall performance

Possible cards:

- **Total Trips:** overall trip volume.
- **Completed Trips:** successful trip count.
- **Cancelled Trips:** trips that did not complete.
- **Cancellation Rate:** cancelled trips as a percentage of total trips.
- **Completed Fare (INR):** sample fare total for completed trips.
- **Average Driver Rating:** average driver rating.

**How to interpret them:** KPI cards provide a quick summary before the viewer studies the detailed charts. A high trip count indicates activity volume; the cancellation rate adds context about service completion. A fare total should always be read with the date and status filters applied.

#### B. Trip status chart — completed versus cancelled

Use a donut chart, pie chart or stacked column chart:

- Legend/Category: `Trip_Status`
- Values: `[Total Trips]` (or a trip count measure)

**Purpose:** Shows the mix of completed and cancelled trips.

**Interpretation:** Compare the size of the Completed and Cancelled categories. A cancellation percentage is more useful than the cancellation count alone when comparing periods with different trip volumes.

#### C. Monthly trip trend

Use a line chart or column chart:

- X-axis: Calendar `Year-Month` or `Pickup_Date` at the required date granularity.
- Y-axis: `[Total Trips]`.
- Optional legend: `Trip_Status`.

**Purpose:** Shows how trip volume changes over the selected date range.

**Interpretation:** Peaks may indicate higher-demand periods and dips may indicate lower activity. Check that months are sorted chronologically, not alphabetically. A partial month should not be compared directly with a full month without accounting for the number of days represented.

#### D. Hourly demand / peak-hour analysis

Use a column chart:

- X-axis: `Hour`.
- Y-axis: `[Total Trips]`.
- Sort by Hour in ascending order.

**Purpose:** Identifies the hours with relatively high and low trip activity.

**Interpretation:** Taller columns indicate more trips recorded in that hour. Use the 0–23 hour sequence so the chart reads from midnight through the day. A peak in this sample is not proof of a general citywide demand pattern.

#### E. Pickup and drop-off location analysis

Use a bar chart:

- Y-axis: `Pickup_Location` or `Dropoff_Location`.
- X-axis: `[Total Trips]`.
- Sort descending by trip count.
- Optionally apply a Top N filter to display the busiest areas.

**Purpose:** Highlights areas that appear frequently as trip origins or destinations.

**Interpretation:** The chart helps compare locations and identify frequently used areas. Pickup and drop-off charts answer different questions; do not combine them into one category unless the analysis is explicitly designed for that purpose.

#### F. Payment method analysis

Use a donut chart or bar chart:

- Category: `Payment_Method`.
- Values: `[Total Trips]`.

**Purpose:** Compares the number of trips associated with UPI, cards, cash and wallet payments.

**Interpretation:** The largest segment/bar is the most frequently recorded payment category in the selected filter context. This measures trip count by payment method, not payment amount.

#### G. Fare analysis

Possible visuals:

- Column chart: average completed fare by month or pickup area.
- Bar chart: completed fare by pickup location.
- Histogram-style bins: fare distribution, if bins are created.

**Purpose:** Explores how sample fares vary across periods or locations.

**Interpretation:** Average fare and total fare answer different questions. Total fare is affected by trip volume; average fare describes the typical fare level. Compare like-for-like periods and remember the source fares are illustrative.

#### H. Distance and duration analysis

Use a scatter chart or column chart:

- X-axis: `Trip_Distance_KM`.
- Y-axis: `Trip_Duration_Min`.
- Details: `Trip_ID` (for a trip-level scatter plot).
- Tooltips: `Fare_INR`, `Surge_Multiplier`, `Trip_Status`.

**Purpose:** Examines whether longer trips tend to take more time and how fare or surge values vary across trips.

**Interpretation:** A general upward pattern suggests that longer-distance trips often take more time. Traffic, route choice and other factors can create exceptions. A scatter plot shows association, not causation.

#### I. Ratings analysis

Use cards or bar charts for average driver and customer ratings.

**Purpose:** Gives a high-level view of the rating fields.

**Interpretation:** Averages can hide variation. If rating quality is important, also examine rating distributions and the number of records contributing to each average.

### Step 8 — Add slicers and interactions

Useful slicers for this report:

- `Pickup_Date` (Between or Relative date).
- `Month` or Calendar `Year-Month`.
- `Trip_Status`.
- `Pickup_Location`.
- `Dropoff_Location`.
- `Payment_Method`.
- `Day`.

Slicers allow users to focus the entire report on a selected time period, location, status or payment category.

Check interactions using **Format → Edit interactions**. For example, selecting a payment method should filter the relevant KPI cards and charts if that is the intended report behaviour. Avoid leaving a visual unfiltered accidentally.

### Step 9 — Format the dashboard

Recommended presentation practices:

1. Use a clear report title such as **Uber Trips Analysis Dashboard**.
2. Put high-level KPI cards in the top section.
3. Group charts by topic: trip performance, demand, locations, payments and customer experience.
4. Use consistent colours for statuses throughout the report (for example, one colour for Completed and another for Cancelled).
5. Use readable titles, axis labels and tooltips.
6. Display fare values in INR (₹), distance in KM and duration in minutes.
7. Avoid unnecessary visual clutter, excessive gridlines and too many colours.
8. Align visuals and maintain consistent spacing.
9. Keep slicers in a clearly visible area.
10. Test the report at the normal viewing size and verify that labels are not cut off.

## 5. How to read and use the dashboard

A suggested reading sequence:

1. **Start with the KPI cards** to understand the total activity and completion/cancellation performance.
2. **Review trip status** to see the completed/cancelled mix.
3. **Check the monthly trend** to identify changes over the date range.
4. **Inspect hourly demand** to identify busier time slots.
5. **Compare pickup and drop-off areas** to see where trip activity is concentrated.
6. **Review payment methods** to understand the payment mix.
7. **Explore fare, distance and duration** to understand trip characteristics.
8. **Check ratings** for a high-level view of the rating data.
9. **Apply slicers** to investigate a particular month, area, status or payment type.
10. **Clear slicers** before returning to the overall view.

Always check the active filters before interpreting a KPI. A card showing 100 trips may represent all records or only the 100 trips matching the current slicers.

## 6. Example findings from the original source workbook

The following figures are calculated from the supplied original Excel worksheet, before any user-specific Power Query changes:

| Check | Source workbook result |
|---|---:|
| Total records | 1,200 |
| Completed trips | 1,092 |
| Cancelled trips | 108 |
| Cancellation rate | 9.0% |
| Completion rate | 91.0% |
| Average trip distance (all source rows) | 8.04 KM |
| Average duration (all source rows) | 30.53 minutes |
| Average fare (all source rows) | ₹185.96 |
| Average driver rating (all source rows) | 4.53 |
| Average customer rating (all source rows) | 4.49 |

These are dataset-level reference figures, not necessarily the exact values displayed by your dashboard. Your measures may filter to completed trips, and slicers or Power Query transformations can change the displayed results.

## 7. Quality checks before publishing

- Confirm that the report's total trip count matches the cleaned table's unique `Trip_ID` count.
- Confirm that Completed Trips + Cancelled Trips equals Total Trips, if these are the only two status categories.
- Confirm that Cancellation Rate is formatted as a percentage and is not multiplied by 100 twice.
- Confirm that fare measures exclude cancelled trips if they are labelled as completed fare/revenue.
- Confirm that Month and Hour fields are sorted correctly.
- Confirm that slicers filter the intended visuals.
- Check that blank values and unexpected categories do not appear in charts.
- Check that all KPI cards use measures rather than accidental implicit sums/counts where a defined measure is intended.
- Refresh the report and verify that no Power Query errors occur.

## 8. Limitations and interpretation

- The workbook is a sample dataset, and its dictionary describes fares and surge multipliers as illustrative.
- The data covers only 1 January to 29 June 2026; it does not represent a full year.
- The dataset has 1,200 records and sample Bengaluru-area labels; it should not be treated as a complete representation of all Uber trips in Bengaluru.
- The analysis describes patterns in the supplied records. It cannot establish why a trip was cancelled, why a fare changed or why demand increased.
- Ratings and averages should be interpreted with their record counts and filter context.
- A dashboard is only as reliable as the source data, transformations, relationships and DAX logic behind it.

## 9. Skills demonstrated

- Importing Excel data into Power BI.
- Data profiling and cleaning using Power Query Editor.
- Applying correct data types and validating data quality.
- Understanding fact-table structure and relationships in Model view.
- Creating DAX measures using `DISTINCTCOUNT`, `CALCULATE`, `SUM`, `AVERAGE` and `DIVIDE`.
- Building KPI cards, trend charts, category charts and slicers.
- Formatting and arranging an interactive Power BI report.
- Translating raw trip records into business-oriented insights.

## 10. Suggested GitHub repository structure

```text
Uber-Trips-Analysis/
│
├── Dataset/
│   └── Uber_Trips_Dashboard_Dataset.xlsx
│
├── PowerBI/
│   └── Uber_Trips_Dashboard.pbix
│
├── Screenshots/
│   └── dashboard-overview.png
│
└── README.md
```

Before publishing to GitHub, check whether you are permitted to share the dataset and remove any private or confidential information. Add your actual `.pbix` file and dashboard screenshot to the folders above.

## 11. Project summary

The Uber Trips Analysis Dashboard demonstrates an end-to-end Power BI workflow: importing a structured Excel dataset, preparing it in Power Query Editor, validating and modelling the data, creating reusable DAX measures, and presenting the results through interactive report visuals. It helps users explore trip volume, trip status, cancellation rate, time-based demand, location patterns, payment preferences, fare characteristics and ratings in one report.

---

**Author:** Chandraleka R  
**Project:** Uber Trips Analysis Dashboard  
**Tools:** Excel | Power Query | Power BI | DAX


---

# 12. Documentation of the uploaded Power BI report (`UBER DASHBOARD.pbix`)

This section is based on inspection of the uploaded PBIX report definition. It describes the visuals and field assignments actually saved in the report, rather than generic visual suggestions.

## 12.1 Report canvas and layout

- **Report page:** one page, displayed as `Page 1`.
- **Canvas dimensions:** 1280 × 720 pixels (16:9 widescreen layout).
- **Page display setting:** Fit to page.
- **Background:** an image resource is applied as the page background, with 16% transparency.
- **Report heading:** `UBER TRIPS ANALYSIS DASHBOARD`, centred, white and bold, with a 40 pt font.

The page is arranged with four KPI cards and a date slicer in the upper band. The lower section contains a payment-method pie chart, a monthly revenue line chart, an hourly column chart and a map visual.

## 12.2 KPI cards saved in the report

The report contains these four card visuals:

| Card measure | What it communicates |
|---|---|
| `Total Bookings` | Overall booking volume represented by the measure |
| `Completed Trips` | Number of trips counted as completed |
| `Total Revenue` | Revenue/fare total represented by the measure |
| `Cancellation Rate` | Share of bookings classified as cancelled, if the measure uses the expected cancellation-rate formula |

### How to explain the cards

- **Total Bookings** gives the viewer the overall scale of activity in the selected date range.
- **Completed Trips** shows how many bookings reached the completed status.
- **Total Revenue** summarises the monetary value calculated by the report's measure. Its interpretation depends on whether the DAX measure includes all statuses or completed trips only.
- **Cancellation Rate** expresses cancellations relative to bookings. It should be formatted as a percentage.

The PBIX visual definition confirms the measure names used by the cards. The embedded semantic model is stored in Power BI's binary model format, so this inspection does not expose the full DAX formula text. Do not assume that `Total Revenue` excludes cancelled trips until its measure formula is checked in Power BI Desktop.

## 12.3 Pickup date slicer

The report contains a **dropdown slicer** using `Uber_Trips[Pickup_Date]`.

**Purpose:** It allows the viewer to select one or more pickup dates and filter the report to a chosen date range or set of dates, depending on the slicer's selection behaviour.

**How to use it:** Open the dropdown, select the required date(s), and observe the KPI cards and charts update. Use the select-all option or clear the selection to return to the wider date context.

**Why it matters:** A date filter makes it possible to compare activity across different periods without creating a separate report page for each period.

## 12.4 Payment method pie chart

The saved visual is a **pie chart** with:

- **Category / legend:** `Payment_Method`
- **Values:** `[Completed Trips]`

**Purpose:** It compares completed-trip counts across payment categories. The slice size represents completed trips, not total bookings and not the rupee amount collected.

**How to interpret it:** A larger slice means that payment method accounts for a larger number of completed trips in the current date-filter context.

**Important interpretation detail:** Because the value is `[Completed Trips]`, this chart is a payment-method breakdown of completed trips. It should not be described as the payment-method distribution of every booking unless the value measure is changed to a total-bookings measure.

## 12.5 Monthly revenue line chart

The saved visual is a **line chart** with:

- **X-axis / category:** `Month`
- **Y-axis / value:** `[Total Revenue]`

**Purpose:** It compares the report's total-revenue measure across month categories and helps the viewer identify relative changes in the selected period.

**How to interpret it:** Higher points represent months with a higher calculated revenue total; lower points represent months with a lower total.

**Recommended improvement — chronological sorting:** The saved visual definition sorts the Month category by `Total Revenue` in descending order. This can put months in revenue order rather than calendar order, which makes the line unsuitable for reading a chronological trend. For a time trend:
1. Create or use a numeric `Month Number` column (1–12).
2. Select the `Month` column in Data/Model view.
3. Choose **Column tools → Sort by column → Month Number**.
4. In the line chart, use the month field and sort it ascending by the calendar month order.
5. If the dataset may span multiple years, use a Year-Month field (for example `2026-01`) sorted by a numeric YearMonth key, so January from different years is not combined.

Also confirm whether `Total Revenue` includes cancelled records before describing it as completed-trip revenue.

## 12.6 Hourly bookings column chart

The saved visual is a **column chart** with:

- **X-axis / category:** `Hour`
- **Y-axis series 1:** `[Total Bookings]`
- **Y-axis series 2:** `Sum of Driver_Rating` (an implicit sum aggregation of the `Driver_Rating` column)

**Purpose:** The hour field makes it possible to compare booking activity across the 24 hours of the day and identify hours with comparatively high or low booking volume.

**How to interpret bookings:** Taller booking columns indicate more bookings for that hour in the current filter context.

**Recommended improvement — do not combine unlike units:** `Total Bookings` is a count, while `Sum of Driver_Rating` is a sum of rating points. Plotting them together on the same value axis mixes different units and can make the rating series difficult to interpret. A sum of ratings is also not a meaningful substitute for average driver rating.

A clearer design is:
1. Keep `[Total Bookings]` as the only Y-axis value in the hourly column chart.
2. Sort `Hour` in ascending order from 0 to 23.
3. If driver ratings by hour are important, create a separate measure such as:

```DAX
Average Driver Rating =
AVERAGE ( Uber_Trips[Driver_Rating] )
```

4. Show average driver rating in a separate chart or tooltip, rather than adding the raw sum to the bookings axis.

This makes the chart's message clear: it is an hourly booking-volume chart.

## 12.7 Pickup location map

The PBIX contains a **Map** visual with:

- **Series / location category:** `Pickup_Location`
- **Size:** `Sum of Fare_INR`

**Purpose:** It attempts to show pickup locations geographically, with marker size representing the sum of fare values associated with each pickup location.

**How to interpret it:** Larger markers represent a higher summed fare amount for the pickup location in the current filter context, assuming Power BI successfully geocodes the location labels.

**Data/geocoding consideration:** Location names may be ambiguous. For reliable mapping, set the `Pickup_Location` data category appropriately (such as Place, City or Address depending on the actual values). If the map places points incorrectly, add city/state/country context or latitude and longitude fields if available. Do not interpret marker size as trip count: the saved Size field is `Sum of Fare_INR`, not bookings.

## 12.8 Dashboard interaction and user journey

The saved report contains one date dropdown slicer and multiple visuals on the same page. A practical way to explore it is:

1. Begin with the four KPI cards to understand the current overall totals.
2. Select a date or date selection in the `Pickup_Date` slicer.
3. Review how booking volume, completed trips, revenue and cancellation rate respond.
4. Compare completed-trip payment categories in the pie chart.
5. Review monthly revenue levels in the line chart, after correcting month sorting.
6. Identify busy hours using the hourly booking column chart.
7. Explore pickup-area fare distribution using the map.
8. Clear the date selection to return to the full available period.

Power BI visuals can cross-filter or highlight one another. Verify the intended behaviour using **Format → Edit interactions** in Power BI Desktop, because the PBIX layout alone does not prove every interaction setting.

## 12.9 Important refinements before publishing

The following changes would improve the accuracy and readability of the current report:

- **Monthly line chart:** sort Month chronologically, not by Total Revenue descending.
- **Hourly column chart:** remove `Sum of Driver_Rating` from the booking-count axis; use bookings alone or create a separate average-rating visual.
- **Revenue definition:** inspect the `[Total Revenue]` DAX formula and state clearly whether cancelled trips are included. If the intent is completed-trip fare, use a measure filtered to `Trip_Status = "Completed"`.
- **Map meaning:** explain that marker size is based on summed `Fare_INR`, not trip count.
- **Pie chart meaning:** label it as completed trips by payment method, because its value is `[Completed Trips]`.
- **Date slicer:** ensure it filters all four cards and all charts as intended.
- **Visual titles:** add explicit titles to the pie, line, column and map visuals so a viewer does not have to infer their purpose from the axes.
- **Number formats:** show monetary values in INR (₹), cancellation rate as a percentage, and trip counts as whole numbers.

These are improvement recommendations based on the saved visual field assignments; they do not mean the existing PBIX has already been modified.

## 12.10 Actual report visual inventory

| Report element | Visual type | Field / measure used |
|---|---|---|
| Report heading | Text box | UBER TRIPS ANALYSIS DASHBOARD |
| Total bookings | Card | `Total Bookings` |
| Completed trips | Card | `Completed Trips` |
| Total revenue | Card | `Total Revenue` |
| Cancellation rate | Card | `Cancellation Rate` |
| Date filter | Dropdown slicer | `Pickup_Date` |
| Payment split | Pie chart | `Payment_Method` + `Completed Trips` |
| Revenue by month | Line chart | `Month` + `Total Revenue` |
| Bookings by hour | Column chart | `Hour` + `Total Bookings` + `Sum of Driver_Rating` |
| Pickup area fare map | Map | `Pickup_Location` + `Sum of Fare_INR` |

