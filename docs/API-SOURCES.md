# API Sources

## Purpose

This file documents potential live data sources for Canada at a Glance.

The initial version must use local prototype JSON data. Live integrations
should be introduced incrementally only after the complete local-data
experience works.

Exact series identifiers and table selections must be validated before
implementation.

Do not guess identifiers.

## Provider order

1. Local prototype provider
2. Bank of Canada provider
3. Statistics Canada provider
4. Optional weather or geospatial provider

## Local prototype provider

### File

src/data/canada-dashboard.json

### Purpose

Support the complete experience before external APIs are introduced.

### Requirements

- Include all dashboard sections
- Include multiple frequencies
- Include national and regional data
- Include prototype source metadata
- Include at least one missing value
- Include attention-rule test cases
- Label values as prototype data

## Bank of Canada provider

### Intended use

Potential indicators include:

- Policy interest rate
- CAD/USD exchange rate
- Selected financial or economic series

### Authentication

The initial design assumes only publicly accessible endpoints that do not
require credentials.

Validate this against current source documentation before implementation.

### Implementation

Create:

src/services/bankOfCanada.ts

Requirements:

- Keep series identifiers in configuration
- Keep requests out of Vue components
- Request only the required period
- Normalize observations to shared types
- Preserve observation dates
- Preserve source metadata
- Validate the latest observation
- Support loading, empty, and error states
- Fall back to local prototype data when configured
- Identify fallback data clearly
- Avoid repeated identical requests

### Validation checklist

Before connecting a Bank of Canada value:

- Confirm the exact series identifier
- Confirm the unit
- Confirm the frequency
- Confirm the geography
- Confirm the returned date format
- Confirm missing-value behaviour
- Verify recent observations manually
- Document the mapping in DATA-DICTIONARY.md

## Statistics Canada provider

### Intended use

Potential indicators include:

- Real GDP
- Inflation
- Unemployment
- Employment
- Population growth
- Wage growth
- Housing indicators
- Retail activity
- International trade
- Provincial and territorial comparisons

### Implementation

Create:

src/services/statCan.ts

Requirements:

- Use exact validated Product IDs or vector identifiers
- Document selected dimensions
- Document seasonal-adjustment status
- Document geography
- Document unit and scalar factor
- Retrieve only required observations
- Normalize responses to shared types
- Preserve reference periods
- Preserve source metadata
- Handle unavailable or suppressed observations
- Never replace missing observations with zero
- Support local fallback data

### Required documentation for each metric

Before implementation, record:

- Display name
- Source organization
- Product ID or vector ID
- Table title
- Selected dimensions
- Geography
- Unit
- Scalar factor
- Frequency
- Seasonal-adjustment status
- Desired history
- Update cadence if documented
- Source page
- API endpoint pattern
- Transformation rule

## Government of Canada Open Data

### Intended use

Use the Open Data catalogue primarily to discover federal datasets and their
available resources.

Do not assume that catalogue metadata is the underlying statistical data.

For each selected dataset, document the actual resource format and access
method.

## Environment and Climate Change Canada

### Potential future use

- Weather alerts
- Climate information
- Air-quality information
- Water or meteorological conditions
- Geospatial layers

Add this provider only if the national conditions view benefits materially
from the integration.

Do not allow a map integration to delay the core P301 flow.

## Natural Resources Canada

### Potential future use

- Earthquakes
- Geospatial reference data
- Energy or natural-resource information
- National map services

Validate exact services and terms before implementation.

## Secrets and configuration

- Do not place secrets in PLAN.md, BRIEF.md, source code, or Git
- Do not expose private credentials in browser-side environment variables
- Use environment variables only when appropriate
- Commit an example environment file only if configuration is required
- Never commit a populated secret file

## Fallback policy

Every live metric should define:

- Whether fallback is allowed
- Which local metric acts as fallback
- Maximum acceptable age: To be validated per metric and source before integration
- User-facing fallback message
- Source label used during fallback

Default message:

"Live source unavailable. Displaying prototype data."

## Integration gate

Do not connect a new source until:

- The local experience works
- The exact indicator is documented
- The response has been inspected
- Units and dates are understood
- Missing data behaviour is understood
- The normalized mapping is defined
- Appropriate interface states exist
