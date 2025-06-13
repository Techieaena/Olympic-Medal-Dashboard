
# Power BI Project – Olympic Medal Dashboard

## 1. Power Query Editor (Data Transformation Layer)

### Steps Performed:
1. **Loaded Excel file:**  
   Source: `"MedalsData.xlsx"`

2. **Promoted Headers:**
   ```m
   #"Promoted Headers" = Table.PromoteHeaders(Medals_Sheet, [PromoteAllScalars=true])
   ```

3. **Changed Data Types:**
   ```m
   #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{
       {"Country", type text},
       {"Year", Int64.Type},
       {"Games", type text},
       {"Gold", Int64.Type},
       {"Silver", Int64.Type},
       {"Bronze", Int64.Type}
   })
   ```

4. **Removed Rows With Errors:**
   ```m
   #"Removed Errors" = Table.RemoveRowsWithErrors(#"Changed Type", {"Country"})
   ```

5. **Removed Blank Rows:**
   ```m
   #"Removed Blank Rows" = Table.SelectRows(#"Removed Errors", 
       each not List.IsEmpty(List.RemoveMatchingItems(Record.FieldValues(_), {"", null}))
   )
   ```

6. **Renamed Columns (if needed)** using:
   ```m
   #"Renamed Columns" = Table.RenameColumns(#"Previous Step", {
       {"Column1", "Country"}, {"Column2", "Year"}, {"Column3", "Games"}, ...
   })
   ```

## 2. Data Model Layer: Calculated Measures (DAX)

### Key Measures Created:

| Measure Name         | DAX Expression |
|----------------------|----------------|
| **Total Medals**     | `SUM(Medals[Gold]) + SUM(Medals[Silver]) + SUM(Medals[Bronze])` |
| **Gold Medals**      | `SUM(Medals[Gold])` |
| **Silver Medals**    | `SUM(Medals[Silver])` |
| **Bronze Medals**    | `SUM(Medals[Bronze])` |
| **Total Games**      | `DISTINCTCOUNT(Medals[Games])` |
| **Top Country Medals** | `CALCULATE([Total Medals], TOPN(10, ALL(Medals[Country]), [Total Medals]))` |
| **Medal Growth %**   | `DIVIDE([Total Medals] - CALCULATE([Total Medals], PREVIOUSYEAR(Medals[Year])), CALCULATE([Total Medals], PREVIOUSYEAR(Medals[Year])))` |

## 3. Dashboard Creation (Report View)

### Visuals Used:

- **KPI Cards:**
  - Total Medals
  - Total Games
  - Average Medal Index

- **Stacked Bar Chart:**
  - Title: *Medals by Country*
  - Axis: Country
  - Values: Gold, Silver, Bronze

- **Pie Chart:**
  - Title: *Group by Medal Type*
  - Values: Sum of Gold, Silver, Bronze

- **Column Chart:**
  - Title: *Top 10 Countries by Total Medals*
  - Visual-level filter: Top 10 based on total medals

- **Line Chart:**
  - Title: *Sum of Total Medals by Games*
  - Axis: Games
  - Value: Total Medals

## Formatting Details

- **Font Style:** Segoe UI
- **KPI Card Size:** 200px width × 100px height
- **Theme Colors:** Gold (#D4AF37), Silver (#C0C0C0), Bronze (#CD7F32)
- **Drop-down Slicer:** Dropdown style enabled with click-to-open

## Interactivity & Deployment

- Slicers: Country and Year slicers to filter visuals dynamically
- Tooltip: Medal breakdown by type on hover
- Drill-down: Country-wise details enabled
- Published to Power BI Service with Viewer access rights

