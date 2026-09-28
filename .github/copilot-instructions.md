# Repository Instructions

## Project boundaries

- Treat PLAN.md as the implementation plan and BRIEF.md as the product brief.
- This is an independent educational prototype, not an official Government of Canada product. Do not imply official endorsement or access to confidential information.
- Build the complete local-data experience before connecting live sources.
- Keep the application single-page and use the documented Vue 3, Vite, TypeScript, Vuetify 3, Material Design Icons, Chart.js, vue-chartjs, and Inter stack.
- Add only dependencies and files needed for an implemented responsibility. Do not add a router, state-management library, test framework, linter, formatter, or utility package without a demonstrated need.

## Data and providers

- Keep API requests out of Vue components. Normalize local and external data before presentation.
- Do not create `src/services/bankOfCanada.ts` or `src/services/statCan.ts` until the corresponding live integration is being implemented. Use the local JSON provider first.
- Never guess metric frequencies, API identifiers, threshold values, or maximum acceptable data ages. Validate them against source documentation; leave unresolved values explicitly marked `To be validated`.
- Keep raw values numeric. Preserve source, unit, geography, frequency, reference period, update time, retrieval time, and prototype/live status.
- Keep missing, suppressed, invalid, or unavailable observations missing. Never replace them with zero or an undisclosed previous value.
- `changeDirection` describes movement only: `up`, `down`, or `unchanged`.
- `interpretation` describes meaning only: `informational`, `stable`, `monitor`, or `attention`. Do not use `improving` as an interpretation status; explain improvement only under a documented metric-specific rule.
- Alert `severity` is separate from metric interpretation and uses `low`, `medium`, or `high` for the first prototype.

## Validation and interface

- First-pass validation is manual and fixture-based using the local JSON dataset. Do not install a testing framework for this pass.
- Preserve the calm, non-partisan, information-dense visual direction in PLAN.md and BRIEF.md.
- Follow the documented accessibility requirements, including keyboard operation, visible focus, sufficient contrast, non-colour status cues, chart text alternatives, and reduced-motion support.
- Show prototype, stale, missing, and fallback data states clearly, with actual reference periods and source provenance.
