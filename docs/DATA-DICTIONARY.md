# Data Dictionary

## Purpose

This file is the source of truth for the meaning and treatment of dashboard
metrics.

No live metric should be implemented until its definition is complete.

## Required fields

For every metric, record:

- Metric ID
- Display name
- Short name
- Description
- Category
- Source organization
- Source dataset
- Product or series identifier
- Unit
- Scalar factor
- Geography
- Frequency
- Seasonal-adjustment status
- Reference-period meaning
- Comparison method
- Display format
- Interpretation method
- Missing-value treatment
- Prototype or live status

## Movement and interpretation

- `changeDirection` describes movement only: `up`, `down`, or `unchanged`.
- `interpretation` describes meaning only: `informational`, `stable`, `monitor`, or `attention`.
- Do not use `improving` as an interpretation status. Communicate improvement in explanatory text only when supported by a documented metric-specific rule.
- Alert `severity` is separate from metric interpretation; see THRESHOLD-RULES.md.

Do not guess frequencies, source identifiers, or thresholds. Validate them
against the selected source documentation and mark unresolved values as
`To be validated` before live implementation.

## Initial metrics

### Real GDP growth

- Metric ID: real-gdp-growth
- Category: Economy
- Geography: Canada
- Intended display: Percentage change
- Initial source: Prototype data
- Future source: Statistics Canada
- Frequency: To be validated against the selected source series
- Comparison method: To be documented
- Interpretation: Use documented thresholds only

### Inflation

- Metric ID: inflation
- Category: Affordability
- Geography: Canada
- Intended display: Percentage
- Initial source: Prototype data
- Future source: Statistics Canada
- Frequency: To be validated against the selected source series
- Comparison method: To be documented
- Interpretation: Use documented thresholds only

### Unemployment

- Metric ID: unemployment
- Category: Labour
- Geography: Canada
- Intended display: Percentage
- Initial source: Prototype data
- Future source: Statistics Canada
- Frequency: To be validated against the selected source series
- Comparison method: Compare with previous period
- Interpretation: Use documented thresholds only

### Policy interest rate

- Metric ID: policy-interest-rate
- Category: Financial conditions
- Geography: Canada
- Intended display: Percentage
- Initial source: Prototype data
- Future source: Bank of Canada
- Frequency: To be validated against the selected source series
- Comparison method: Compare with previous observation
- Interpretation: Do not infer positive or negative direction automatically

### Population growth

- Metric ID: population-growth
- Category: Population
- Geography: Canada
- Intended display: Percentage
- Initial source: Prototype data
- Future source: Statistics Canada
- Frequency: To be validated against the selected source series
- Comparison method: To be documented
- Interpretation: Contextual, not automatically positive or negative

### CAD/USD exchange rate

- Metric ID: cad-usd
- Category: Financial conditions
- Geography: Canada
- Intended display: Exchange rate
- Initial source: Prototype data
- Future source: Bank of Canada
- Frequency: To be validated against the selected source series
- Comparison method: Compare with previous observation
- Interpretation: Direction alone should not imply positive or negative status

## Supporting metrics

Potential later additions:

- Food-price change
- Shelter-cost change
- Employment change
- Wage growth
- Housing starts
- Retail activity
- Exports
- Imports
- Provincial unemployment
- Provincial population growth
- Provincial economic growth

Add exact definitions before implementation.

## Missing values

Use:

- `null` for an explicitly missing value
- A separate status or reason when known
- A user-facing dash or "Not available" label

Do not:

- Use zero
- Reuse the previous value without disclosure
- Remove the metric silently
- Invent an estimate

## Reference periods

Every displayed value must show the period represented by the observation.

Do not substitute the retrieval date for the reference period.

Examples of distinct metadata:

- Reference period: the period measured by the value
- Updated date: when the source published or revised it
- Retrieved date: when the application obtained it

## Formatting

Use centralized rules.

Potential formats:

- Currency
- Percentage
- Whole number
- Decimal
- Index
- Rate
- Abbreviated large number
- Exchange rate

Raw values should remain numeric in the data model.
