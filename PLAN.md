# Canada at a Glance: Implementation Plan

## Project type

Protogen P301 Operational Dashboard

## Product summary

Canada at a Glance is a responsive web dashboard that gives a fictional
senior national decision-maker a concise daily view of Canada's economic,
social, affordability, regional, and operational conditions.

It should help the user quickly understand:

1. How Canada is performing
2. What has materially changed
3. Where pressures are emerging
4. Which regions are affected
5. What requires leadership attention

This is an independent educational prototype. It is not an official
Government of Canada product and must not imply access to confidential,
classified, privileged, or non-public information.

## Core question

What national conditions require leadership attention today?

## Product principles

- Decision usefulness over data volume
- Clarity over decoration
- Trustworthy sourcing over artificial precision
- Progressive disclosure over information overload
- Accessible design from the beginning
- Local prototype data before live integrations
- One strong end-to-end flow before feature expansion
- No unnecessary dependencies or abstractions

Every component, dependency, interaction, and data point must earn its place.

## Primary user

A fictional senior national decision-maker such as the Prime Minister,
a senior policy adviser, or a central agency executive.

The user has limited time and needs to understand the national operating
picture without opening several separate reports.

## Use scenario

The user opens the dashboard at the beginning of the day.

Within the first viewport, the user should be able to:

- Review headline national indicators
- Identify the highest-priority issues
- See whether conditions are improving, stable, or worsening
- Understand affected regions
- Select an issue for more detail
- Verify the source and reference period

## Core end-to-end interaction

The user selects an item from the Requires Attention panel.

The application then:

1. Highlights the related metric
2. Updates the primary trend visualization
3. Shows the affected geography
4. Presents a short factual explanation
5. Displays the source and reference period

This flow must work completely with local prototype data before live APIs
are introduced.

## Information architecture

### Header

Include:

- Product name: Canada at a Glance
- Subtitle: National Daily Briefing
- Last refreshed date and time
- Overall data-status indicator
- Reference-period control
- Light and dark theme control

### Requires Attention

Show three to five prioritized issues.

Each issue should include:

- Title
- Severity
- Short factual explanation
- Related metric
- Relevant geography
- Reference period
- Control to inspect the issue

Alerts are prototype indicators based on documented threshold rules.

They must not be represented as official recommendations, emergency notices,
forecasts, or policy advice.

### National Pulse

Display six headline indicators:

- Real GDP growth
- Inflation
- Unemployment
- Policy interest rate
- Population growth
- CAD/USD exchange rate

Each metric card should include:

- Current value
- Comparison value
- Direction of movement
- Interpretation status
- Unit
- Reference period
- Source
- Data freshness

Direction and interpretation must remain separate.

An increase is not automatically positive. Higher GDP may be favourable,
while higher inflation or unemployment may warrant attention.

### Economic Momentum

Provide a trend visualization that allows the user to inspect:

- Real GDP
- Inflation
- Employment
- Retail activity
- International trade

The first version may use a focused subset to avoid overcrowding.

### Canadians and Affordability

Provide indicators for:

- Food-price change
- Shelter-cost change
- Employment
- Wage growth
- Housing starts
- Population growth

Use only the measures that materially improve the experience.

### Provincial and Territorial Comparison

Provide a sortable regional comparison.

Potential measures include:

- Unemployment
- Population growth
- Housing starts
- Economic growth

Selecting a province or territory should update a related detail view.

### National Conditions

Provide a map or map-ready panel.

Potential future layers include:

- Weather alerts
- Air quality
- Earthquakes
- Regional unemployment
- Population growth

The first version may use a carefully designed map placeholder or simplified
regional visualization if a reliable map implementation would add
unnecessary scope.

## Technical stack

Use only:

- Vue 3
- Vite
- TypeScript
- Vuetify 3
- Material Design Icons
- Chart.js
- vue-chartjs
- Inter font

Do not add Pinia, JSX, testing frameworks, ESLint, Prettier, routing, or extra
utility libraries unless a demonstrated requirement emerges.

This is initially a single-page application.

Do not add Vue Router unless the active Protogen instructions require it or
the application eventually requires a genuinely separate view.

## Application architecture

Use this flow:

Vue presentation components
→ shared dashboard composable
→ normalized internal data model
→ provider adapters
→ local JSON or external API

Vue components must not call external APIs directly.

External and local data must be normalized before reaching presentation
components.

## Project structure

src/
  components/
    AppHeader.vue
    MetricCard.vue
    AttentionPanel.vue
    AttentionItem.vue
    TrendChart.vue
    RegionalComparison.vue
    NationalConditions.vue
    SourceLabel.vue
    DataStatus.vue
    EmptyState.vue
  composables/
    useDashboardData.ts
    useDashboardFilters.ts
    useThemePreference.ts
  data/
    canada-dashboard.json
  services/
    localData.ts
    bankOfCanada.ts
    statCan.ts
    dataAdapter.ts
  types/
    dashboard.ts
    metrics.ts
  utils/
    formatters.ts
    thresholds.ts
    validators.ts
  views/
    DashboardView.vue
  App.vue
  main.ts

Only create files that are used.

If a proposed file does not yet have a real responsibility, do not create it.

Future API-service files should be created only when their integrations are
implemented.

In particular, do not create src/services/bankOfCanada.ts or
src/services/statCan.ts until their respective live integrations are being
implemented.

## Shared metric model

Normalize each metric to a common TypeScript interface containing:

- id
- name
- shortName
- description
- category
- value
- unit
- previousValue
- change
- changeDirection
- interpretation
- geography
- frequency
- referencePeriod
- updatedAt
- retrievedAt
- sourceName
- sourceUrl
- isPrototypeData
- isStale

Field semantics:

- changeDirection describes movement only: up, down, or unchanged.
- interpretation describes meaning only: informational, stable, monitor, or attention.
- Do not use improving as an interpretation status. Explain improvement in text only when supported by a documented metric-specific rule.
- Alert severity is separate from metric interpretation and uses low, medium, or high for the first prototype.

Do not store formatted display values as the source of truth.

Format raw values through centralized formatter functions.

Missing, unavailable, invalid, or suppressed data must remain missing.
Never convert missing data to zero.

## Initial data strategy

Create the prototype dataset at:

src/data/canada-dashboard.json

It should contain:

- Headline national indicators
- At least 24 monthly periods for metrics where monthly frequency is plausible
- Quarterly observations where appropriate
- Regional observations for provinces and territories
- Prototype attention items
- Source and reference-period metadata
- A combination of positive, stable, and attention states
- At least one missing-value scenario
- At least one stale-data scenario

Use realistic but simplified prototype values.

Do not present prototype numbers as current facts.

Show this label wherever appropriate:

"Prototype data for demonstration purposes"

## Reference-period filtering

Provide these options:

- Latest
- 3 months
- 6 months
- 12 months
- 24 months

The initial selection should be Latest.

The shared control should update:

- Metric cards
- Trend charts
- Attention items
- Regional comparison
- National conditions

Indicators may have different release frequencies.

Always display each metric's actual reference period.

If no observation exists in the selected period, show a clear no-data state
rather than displaying zero.

## Charts

Use Chart.js through vue-chartjs.

Register only the Chart.js elements required by the implemented chart types.

Initial visualizations:

1. A national economic trend visualization
2. A Canadians and affordability visualization
3. A provincial and territorial comparison
4. A national conditions view or map-ready placeholder

Charts must include:

- Descriptive titles
- Relevant units
- Accessible tooltips
- Legends where needed
- Reference periods
- Source labels
- Accessible text summaries

Use a restrained and cohesive chart palette.

Do not use a different colour for every series unless the distinction is
necessary.

Avoid chart effects, animations, or decoration that do not improve
comprehension.

## Visual direction

The experience should feel:

- Calm
- Authoritative
- Modern
- Non-partisan
- Trustworthy
- Information-dense but easy to scan

Use:

- Inter typography
- Strong hierarchy
- Restrained colour palette
- Consistent spacing
- Clear grid alignment
- Accessible contrast
- Subtle elevation
- Minimal borders
- Subtle transitions
- Purposeful whitespace

Do not imitate official Government of Canada branding closely enough to imply
that the application is an official product.

## Theme behaviour

Support:

- Light theme
- Dark theme
- Persistent user theme preference

Use the user's saved preference when one exists.

Otherwise, use dark mode as the initial prototype default.

Theme controls must have accessible labels and visible focus states.

## Responsive behaviour

Design mobile-first.

### Small screens

Order content as:

1. Header and data status
2. Requires Attention
3. National Pulse
4. Economic Momentum
5. Canadians and Affordability
6. National Conditions
7. Regional Comparison

Cards should stack without horizontal page scrolling.

Charts should retain readable labels and accessible controls.

### Medium screens

Use a reduced multi-column grid while preserving content priority.

### Large screens

Use a concise national briefing layout:

- Headline metrics near the top
- Attention panel alongside the primary trend
- Regional and national conditions below

The first viewport should contain meaningful information.

## Interaction design

Use subtle transitions for:

- Metric cards
- Attention-item selection
- Filter changes
- Theme changes
- Chart updates
- Detail-panel changes

Animation should support orientation and comprehension.

Respect reduced-motion preferences.

Do not animate critical numbers in a way that slows comprehension.

## Accessibility

Target WCAG AA expectations.

Include:

- Keyboard navigation
- Logical focus order
- Visible focus treatment
- Semantic headings
- Accessible control labels
- Sufficient contrast
- Text alternatives for status icons
- Chart descriptions
- Reduced-motion support
- No colour-only meaning
- Layout compatibility with increased text size

## Data states

Create intentional states for:

- Initial loading
- Successful load
- Empty response
- Missing observation
- Delayed data
- Stale data
- API unavailable
- Local fallback active
- No attention items
- No filter results
- Map layer unavailable

When fallback data is active, show:

"Live source unavailable. Displaying prototype data."

A stale value must show its actual reference period and freshness status.

## Formatting

Create centralized formatter functions for:

- Currency
- Percentages
- Whole numbers
- Large-number abbreviations
- Dates
- Reference periods
- Change values
- Missing values

Do not duplicate formatting logic inside components.

## Live API sequence

Do not connect live APIs during the initial scaffold.

Once the local experience is complete, add providers in this order:

1. Bank of Canada
2. Statistics Canada
3. Optional weather or geospatial source

Implement one series at a time.

Do not replace every local metric simultaneously.

Every provider must:

- Request only the required data
- Normalize the response
- Validate the value
- Preserve source metadata
- Preserve reference periods
- Handle incomplete responses
- Support an explicit fallback
- Identify live versus prototype data
- Avoid exposing secrets in browser code
- Avoid unnecessary repeated requests

## Performance

- Share identical data requests
- Avoid unnecessary reactive recalculation
- Cache session data where appropriate
- Do not continuously refresh monthly or quarterly indicators
- Prevent stale responses from replacing newer selections
- Keep chart rendering responsive
- Clean up resources when components are removed
- Avoid adding a state-management library unless demonstrated complexity
  requires one

## Validation

First-pass validation is manual and fixture-based using cases in
src/data/canada-dashboard.json. Do not install a testing framework for this
pass.

Before displaying an external value, confirm:

- The request succeeded
- The expected series exists
- A valid observation exists
- The value is numeric when required
- The unit is correct
- The geography is correct
- The frequency is correct
- The reference period is preserved
- The source is identified
- Missing values remain missing

## Out of scope

- Confidential government information
- Authentication
- Predictive policy recommendations
- Generative policy summaries
- Classified or privileged briefings
- Real emergency-management functionality
- Native mobile development
- Integration with every available API
- Content that implies official endorsement
- Unnecessary administration screens
- User account management

## Build sequence

1. Create repository documentation
2. Scaffold the Vue project
3. Install required dependencies
4. Remove all starter content
5. Create shared TypeScript types
6. Create the local prototype dataset
7. Create the responsive page shell
8. Create reusable metric cards
9. Create the Requires Attention panel
10. Add shared reference-period filtering
11. Add the primary trend visualization
12. Add the regional comparison
13. Add the national conditions view
14. Add loading, empty, error, stale, and fallback states
15. Add light and dark themes
16. Test accessibility and responsiveness
17. Build and deploy the local-data version
18. Confirm that the deployed experience works
19. Connect one Bank of Canada series
20. Add further live series individually only if they improve the product

## Success criteria

A first-time user can:

1. Identify Canada's most important current issue
2. Understand the supporting indicator
3. See the affected geography
4. Inspect the trend
5. Identify the data source and reference period

The deployed site must:

- Work on desktop and mobile
- Match BRIEF.md
- Handle important non-happy-path states
- Use a clear and intentional visual system
- Preserve data provenance
- Avoid unnecessary complexity

The repository must contain:

- PLAN.md
- BRIEF.md
- README.md
- LICENSE
- Organized context files
- Descriptive commits showing progress over time
