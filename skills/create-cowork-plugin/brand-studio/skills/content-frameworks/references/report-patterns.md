# Data Reporting & Visualization Patterns

Techniques for presenting data clearly and driving decisions. Apply these within the REPORT framework.

## 1. Chart Selection Guide

Choose the right visualization for the data story.

| Data story | Chart type | When to use |
|-----------|-----------|-------------|
| Comparison | Bar chart (horizontal or vertical) | Comparing categories or items |
| Trend over time | Line chart | Showing change across periods |
| Parts of a whole | Pie or donut chart | Maximum 5 segments, clear majority |
| Distribution | Histogram or box plot | Showing spread and outliers |
| Correlation | Scatter plot | Showing relationship between two variables |
| Ranking | Horizontal bar chart | Ordered list of items by metric |
| Geographic | Map or heatmap | Location-based data |
| Progress | Gauge or progress bar | Tracking toward a target |

**Rules**:
- Never use 3D charts — they distort data
- Limit pie charts to 5 slices maximum; use "Other" for the rest
- Start bar chart Y-axes at zero to avoid misleading proportions
- Use consistent time intervals on line charts

## 2. The Annotation Principle

Don't make readers interpret charts — tell them what to see.

**Application**:
- Add a one-line insight title above each chart: "Revenue up 23% driven by enterprise"
- Circle or highlight the key data point on the chart
- Use callout labels for anomalies: "Product launch effect"
- Add trend lines or reference lines for targets/benchmarks
- Include a brief "So what?" sentence below each chart

## 3. Dashboard Design

Organize multiple data views into a scannable layout.

**Application**:
- Top row: KPI cards with current value, trend arrow, and period comparison
- Second row: Primary chart (the most important story)
- Bottom rows: Supporting charts and detail tables
- Use consistent color coding across all charts
- Include filter controls or segment labels for context
- Date range always visible in header

## 4. The Inverted Pyramid

Lead with the conclusion, follow with supporting evidence, end with details.

**Application**:
- Executive summary on page 1 with key findings and recommendations
- Supporting data and analysis in the middle sections
- Detailed tables, raw data, and methodology in appendices
- Each section starts with its own summary sentence
- Decision-makers can stop reading at any level and still have the answer

## 5. Comparison Tables

Make differences clear with structured comparisons.

**Application**:
- Use alternating row shading for readability
- Bold the "winner" or highlight the recommended option
- Include a summary row: "Best for..." at the bottom
- Limit columns to 5-6 maximum for readability
- Use icons (checkmarks, X marks) for binary comparisons

## 6. Trend Analysis Patterns

Show where things are going, not just where they are.

**Application**:
- Always show at least 3 data points for a trend (2 is a line, 3 is a trend)
- Include year-over-year and period-over-period comparisons
- Use sparklines for compact trend visualization
- Mark key events on timelines: "New feature launched", "Pricing change"
- Project forward with dotted lines for forecasts (clearly labeled as projections)

## 7. Red/Amber/Green (RAG) Status

Use traffic-light coding for quick status assessment.

**Application**:
- Green: on track, meeting targets, positive trend
- Amber: needs attention, approaching threshold, mixed results
- Red: off track, below target, negative trend requiring action
- Define thresholds clearly: "Green = >90% of target"
- Use in status tables, KPI cards, and project trackers
- Complement with ~~accent-primary and ~~accent-secondary brand colors

## 8. Data Storytelling Narrative

Connect data points into a coherent story.

**Application**:
- Setup: "We set out to answer [question]"
- Tension: "What we found was surprising..."
- Resolution: "The data shows [insight], which means [recommendation]"
- Use transitional phrases between charts: "This pattern becomes clearer when we look at..."
- End each section with "This means..." or "The implication is..."

## 9. Precision and Honesty

Present data accurately without misleading.

**Application**:
- Show confidence intervals or error margins for estimates
- Acknowledge limitations: "This data covers X but not Y"
- Don't cherry-pick: if the trend is mixed, show the full picture
- Use appropriate precision: "$1.2M" not "$1,234,567" for high-level reports
- State sample sizes and data freshness
- Label projections as projections, not facts

## 10. Actionable Metrics vs. Vanity Metrics

Focus on metrics that drive decisions, not just impressions.

**Application**:
- Vanity: "10,000 page views" → Actionable: "3.2% conversion rate from landing page"
- Vanity: "500 followers gained" → Actionable: "12 qualified leads from social"
- For each metric, add: "And this means we should..."
- Include the target alongside the actual: "Actual: 78% | Target: 85%"
- Group metrics by business impact, not by data source
