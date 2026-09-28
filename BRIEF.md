# Project Brief: Canada at a Glance

## Summary

Canada at a Glance is an independent educational dashboard prototype that
presents a concise daily view of Canada's economic, social, affordability,
regional, and operational conditions.

The dashboard is intended to help a fictional senior national decision-maker
identify meaningful changes and decide which issues warrant attention.

## Primary user

A fictional senior national decision-maker, senior policy adviser, or central
agency executive.

The user has limited time, consumes information quickly, and needs a reliable
national overview without opening multiple reports.

## User need

The user needs to know:

- What changed
- Why it matters
- Which regions are affected
- Whether the condition is improving, stable, or worsening
- Where to inspect additional evidence
- When the source data was last updated

## Core decision

What national conditions require leadership attention today?

## Core experience

The user opens the dashboard and immediately sees:

- Requires Attention
- National Pulse
- Current data status
- The most important national trend

The user selects an issue and the experience updates the related metric,
trend, geography, explanation, source, and reference period.

## Design intent

Create a calm, authoritative, modern, non-partisan interface appropriate for
a senior briefing.

The dashboard should feel carefully designed rather than generated from a
generic template.

Use:

- Clear visual hierarchy
- Restrained colour
- Inter typography
- Consistent spacing
- Accessible contrast
- Meaningful status labels
- Subtle motion
- Progressive disclosure

Do not imitate official government branding.

## Data approach

The initial build uses realistic but simplified local prototype data.

The complete experience must work with local data before any external API is
connected.

Future data sources may include:

- Bank of Canada
- Statistics Canada
- Government of Canada Open Data
- Environment and Climate Change Canada
- Natural Resources Canada

Each value must preserve its source, unit, geography, frequency, reference
period, and prototype or live status.

## Key features

- Requires Attention panel
- Six National Pulse indicators
- Shared reference-period filter
- Economic trend visualization
- Affordability view
- Provincial and territorial comparison
- National conditions view
- Source and freshness details
- Light and dark themes
- Responsive design
- Loading, empty, missing, stale, error, and fallback states

## Primary flow

1. Open the dashboard
2. Identify the highest-priority issue
3. Select the issue
4. Inspect the related indicator
5. Review the trend and affected geography
6. Verify the source and reference period

## Success criteria

A first-time user can complete the primary flow without assistance.

The experience works across screen sizes, uses an intentional design system,
handles missing and unavailable data gracefully, and clearly distinguishes
prototype data from live information.
