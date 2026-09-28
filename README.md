# Canada at a Glance

Canada at a Glance is an independent educational prototype created for a
Protogen P301 operational dashboard case study.

The experience gives a fictional senior national decision-maker a concise
view of Canada's economic, social, affordability, regional, and operational
conditions.

## Status

The initial application uses prototype data for demonstration purposes.

It is not an official Government of Canada product and does not contain
confidential or privileged information.

## Core experience

The dashboard helps the user:

- Review headline national indicators
- Identify issues requiring attention
- Inspect trends and regional conditions
- Verify source and reference-period information
- Distinguish live, stale, missing, and prototype data

## Technology

- Vue 3
- Vite
- TypeScript
- Vuetify 3
- Material Design Icons
- Chart.js
- vue-chartjs
- Inter

## Local development

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

Create a production build:

```bash
npm run build
```

## Project documentation

- `PLAN.md`: Product and implementation plan
- `BRIEF.md`: User, experience, and design intent
- `docs/API-SOURCES.md`: API integration notes
- `docs/DATA-DICTIONARY.md`: Metric definitions
- `docs/THRESHOLD-RULES.md`: Prototype interpretation rules
- `.github/copilot-instructions.md`: Repository guidance for GitHub Copilot

## Data approach

The first version uses:

`src/data/canada-dashboard.json`

Potential future integrations include the Bank of Canada, Statistics Canada,
and selected Canadian weather or geospatial services.

Live sources should be introduced individually only after the local-data
experience is complete.

## Disclaimer

This project is an independent educational prototype. It is not affiliated
with, endorsed by, or operated by the Government of Canada or any public
agency.
