# Threshold and Interpretation Rules

## Purpose

This file defines how prototype attention states are produced.

These rules are for an educational prototype and do not represent official
Government of Canada policy, advice, emergency classification, or forecasting.

## Core principle

Movement and meaning are separate.

Each metric can have:

- Change direction: up, down, or unchanged
- Interpretation: informational, stable, monitor, or attention

Do not derive interpretation from direction alone.
Do not use improving as an interpretation status. If improvement needs to be
communicated, use explanatory text based on a documented metric-specific rule.

## Status levels

### Informational

Use when the metric is provided for context without a documented concern.

### Stable

Use when the metric remains within the prototype's defined stable range.

### Monitor

Use when the metric crosses a documented monitoring threshold or changes
materially from its comparison period.

### Attention

Use when the metric crosses a documented attention threshold or is associated
with a high-priority prototype condition.

## Initial implementation

The prototype dataset may contain preassigned interpretation values so the
interface can be completed before threshold calculations are implemented.

Alert severity is separate from metric interpretation. For the first
prototype, severity must be low, medium, or high. Do not infer severity from
interpretation without a documented rule.

If calculated rules are added:

- Define each rule explicitly
- Keep rules outside presentation components
- Put calculation logic in src/utils/thresholds.ts
- Include the triggering metric
- Include the comparison period
- Include the geography
- Include a plain-language explanation
- Avoid unsupported policy recommendations

## Alert structure

Each attention item should contain:

- id
- title
- description
- severity
- relatedMetricId
- geography
- referencePeriod
- sourceName
- isPrototypeData

## Language standards

Use factual language such as:

- "Increased from the previous period"
- "Above the prototype monitoring threshold"
- "Three regions meet the prototype attention rule"
- "The latest observation is older than the expected reference period"

Avoid language such as:

- "The government must"
- "The Prime Minister should"
- "This will cause"
- "A crisis is imminent"
- "Immediate intervention is required"

## Data-quality attention

The interface may also flag:

- Stale data
- Missing values
- Failed requests
- Fallback data
- Inconsistent reference periods

Data-quality alerts should be visibly different from national-condition alerts.
