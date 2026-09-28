<script setup lang="ts">
import { computed, ref } from 'vue'
import { useTheme } from 'vuetify'
import {
  CategoryScale,
  Chart as ChartJS,
  Filler,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  Tooltip,
  type TooltipItem,
} from 'chart.js'
import { Line } from 'vue-chartjs'
import dashboardData from './data/canada-dashboard.json'

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Tooltip, Legend, Filler)

type Metric = {
  id: string
  name: string
  shortName: string
  unit: string
  value: number
  previousValue: number
  changeDirection: 'up' | 'down' | 'unchanged'
  interpretation: 'informational' | 'stable' | 'monitor' | 'attention'
  referencePeriod: string
  source: string
  geography: string
  frequency: string
  updatedAt: string
  retrievedAt: string
  isPrototypeData: boolean
  comparisonLabel: string
  category: string
}

type AttentionItem = {
  id: string
  title: string
  severity: 'low' | 'medium' | 'high'
  description: string
  relatedMetricId: string
  geography: string
  referencePeriod: string
  isPrototypeData: boolean
  metricLabel: string
}

type Region = {
  name: string
  unemployment: number
  populationGrowth: number
  housingStarts: number
}

const attentionItems = dashboardData.attention as AttentionItem[]
const metricCards = dashboardData.metrics as Metric[]
const regionalData = dashboardData.regions as Region[]

const theme = useTheme()
const isDark = computed(() => theme.global.current.value.dark)
const selectedPeriod = ref('Latest')
const selectedAttentionId = ref(attentionItems[0]?.id ?? '')

const themeAction = computed(() => `Switch to ${isDark.value ? 'light' : 'dark'} theme`)
const savedTheme = window.localStorage.getItem('canada-at-a-glance-theme')

if (savedTheme === 'briefDark' || savedTheme === 'briefLight') {
  theme.global.name.value = savedTheme
}

function toggleTheme() {
  const nextTheme = isDark.value ? 'briefLight' : 'briefDark'
  theme.global.name.value = nextTheme
  window.localStorage.setItem('canada-at-a-glance-theme', nextTheme)
}

const metricMap = computed<Record<string, Metric>>(() =>
  Object.fromEntries(metricCards.map((metric) => [metric.id, metric])),
)

const selectedAttention = computed<AttentionItem | undefined>(() =>
  attentionItems.find((item) => item.id === selectedAttentionId.value) ?? attentionItems[0],
)

const selectedMetric = computed<Metric>(() => {
  const metricId = selectedAttention.value?.relatedMetricId ?? metricCards[0].id
  return metricMap.value[metricId] ?? metricCards[0]
})

const chartSeries = computed(() =>
  (dashboardData.trendSeries.series as Array<{ id: string; label: string; values: number[] }>).map((series) => {
    const palette: Record<string, string> = {
      'real-gdp-growth': isDark.value ? '#91cbb8' : '#286b58',
      inflation: isDark.value ? '#efbd78' : '#955817',
      unemployment: isDark.value ? '#df8977' : '#a54536',
      'cad-usd': isDark.value ? '#7ba4d9' : '#3f5e9f',
    }

    return {
      ...series,
      borderColor: palette[series.id] ?? '#91cbb8',
      backgroundColor: palette[series.id] ?? '#91cbb8',
      pointRadius: 3,
      pointHoverRadius: 5,
      borderWidth: 2,
      fill: false,
      tension: 0.28,
    }
  }),
)

const chartData = computed(() => ({
  labels: dashboardData.trendSeries.labels,
  datasets: chartSeries.value.map((series) => ({
    label: series.label,
    data: series.values,
    borderColor: series.borderColor,
    backgroundColor: series.backgroundColor,
    pointRadius: series.pointRadius,
    pointHoverRadius: series.pointHoverRadius,
    borderWidth: series.borderWidth,
    fill: series.fill,
    tension: series.tension,
  })),
}))

const chartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  interaction: {
    mode: 'nearest' as const,
    intersect: false,
  },
  plugins: {
    legend: {
      position: 'bottom' as const,
      labels: {
        usePointStyle: true,
        boxWidth: 10,
        color: isDark.value ? '#f1f3ee' : '#202a25',
      },
    },
    tooltip: {
      callbacks: {
        label: (context: TooltipItem<'line'>) => {
          const label = context.dataset.label ?? ''
          const value = context.parsed.y
          return `${label}: ${value}${label === 'CAD/USD' ? ' C$ / US$' : '%'}`
        },
      },
    },
  },
  scales: {
    x: {
      grid: { display: false },
      ticks: { color: isDark.value ? '#dfe5de' : '#2d352f' },
    },
    y: {
      grid: { color: isDark.value ? 'rgba(241,243,238,0.08)' : 'rgba(32,42,37,0.08)' },
      ticks: {
        color: isDark.value ? '#dfe5de' : '#2d352f',
        callback: (value: string | number) => `${value}%`,
      },
    },
  },
}))

const periodOptions = ['Latest', '3 months', '6 months', '12 months', '24 months']

const formatMetricValue = (metric: Metric) => {
  const value = Number(metric.value)

  if (metric.unit === '%') {
    return `${value.toFixed(1)}%`
  }

  if (metric.id === 'cad-usd') {
    return `${value.toFixed(2)} ${metric.unit}`
  }

  return `${value.toFixed(1)} ${metric.unit}`
}

const formatDelta = (metric: Metric) => {
  const delta = Number(metric.value - metric.previousValue)
  const prefix = delta > 0 ? '+' : delta < 0 ? '−' : ''

  if (metric.unit === '%') {
    return `${prefix}${Math.abs(delta).toFixed(1)} pts`
  }

  if (metric.id === 'cad-usd') {
    return `${prefix}${Math.abs(delta).toFixed(2)}`
  }

  return `${prefix}${Math.abs(delta).toFixed(1)}`
}

const getDirectionLabel = (metric: Metric) => {
  if (metric.changeDirection === 'up') return 'Up'
  if (metric.changeDirection === 'down') return 'Down'
  return 'Unchanged'
}

const getInterpretationLabel = (metric: Metric) => {
  const labels = {
    informational: 'Informational',
    stable: 'Stable',
    monitor: 'Monitor',
    attention: 'Attention',
  }
  return labels[metric.interpretation]
}

const getSeverityLabel = (item: AttentionItem) => item.severity.charAt(0).toUpperCase() + item.severity.slice(1)
</script>

<template>
  <VApp>
    <VAppBar class="site-header" flat>
      <VToolbar class="header-inner" aria-label="Main header">
        <a class="brand" href="#main" aria-label="Canada at a Glance home">
          <span class="brand-mark" aria-hidden="true">CA</span>
          <span class="brand-name">Canada at a Glance</span>
        </a>

        <VSpacer />

        <div class="header-controls" aria-label="Dashboard controls">
          <label class="period-select" for="reference-period">
            <span class="sr-only">Reference period</span>
            <select id="reference-period" v-model="selectedPeriod" aria-label="Select reference period">
              <option v-for="period in periodOptions" :key="period" :value="period">
                {{ period }}
              </option>
            </select>
          </label>

          <VBtn
            class="theme-toggle"
            variant="outlined"
            :aria-label="themeAction"
            :title="themeAction"
            @click="toggleTheme"
          >
            <VIcon :icon="isDark ? 'mdi-white-balance-sunny' : 'mdi-weather-night'" start />
            <span>{{ isDark ? 'Light mode' : 'Dark mode' }}</span>
          </VBtn>
        </div>
      </VToolbar>
    </VAppBar>

    <VMain id="main">
      <VContainer class="briefing-shell">
        <header class="briefing-intro">
          <p class="eyebrow">National Daily Briefing</p>
          <h1>Canada at a Glance</h1>
          <p class="intro-copy">
            A concise view of national conditions, meaningful changes, and the regions they touch.
          </p>
        </header>

        <div class="data-notice" role="status" aria-live="polite">
          <span class="status-mark" aria-hidden="true"></span>
          <strong>Prototype data</strong>
          <span class="notice-divider" aria-hidden="true"></span>
          <span>{{ dashboardData.meta.status }}</span>
          <span class="notice-divider" aria-hidden="true"></span>
          <span>{{ dashboardData.meta.lastRefreshedLabel }}</span>
        </div>

        <section class="overview-grid" aria-label="Priority issues and selected metric detail">
          <article class="panel attention-panel" aria-labelledby="attention-title">
            <div class="section-heading compact-heading">
              <div>
                <p class="eyebrow">01 / Requires attention</p>
                <h2 id="attention-title">Attention panel</h2>
              </div>
            </div>

            <div class="attention-list">
              <button
                v-for="item in attentionItems"
                :key="item.id"
                class="attention-item"
                :class="{ active: selectedAttention?.id === item.id }"
                type="button"
                :aria-pressed="selectedAttention?.id === item.id"
                @click="selectedAttentionId = item.id"
              >
                <div class="attention-item-head">
                  <span class="alert-badge" :data-level="item.severity">{{ getSeverityLabel(item) }}</span>
                  <span class="attention-time">{{ item.referencePeriod }}</span>
                </div>
                <h3>{{ item.title }}</h3>
                <p>{{ item.description }}</p>
                <div class="attention-meta">
                  <span>{{ item.geography }}</span>
                  <span>{{ item.metricLabel }}</span>
                </div>
              </button>
            </div>
          </article>

          <article class="panel focus-panel" aria-labelledby="focus-title">
            <div class="section-heading compact-heading">
              <div>
                <p class="eyebrow">Selected issue</p>
                <h2 id="focus-title">National condition</h2>
              </div>
            </div>

            <div class="focus-stack">
              <div class="focus-header">
                <span class="focus-badge" :data-level="selectedAttention?.severity ?? 'medium'">
                  {{ selectedAttention ? getSeverityLabel(selectedAttention) : 'Attention' }}
                </span>
                <span class="focus-period">{{ selectedAttention?.referencePeriod }}</span>
              </div>

              <h3>{{ selectedAttention?.title }}</h3>
              <p class="focus-copy">{{ selectedAttention?.description }}</p>

              <dl class="focus-metadata">
                <div>
                  <dt>Related metric</dt>
                  <dd>{{ selectedMetric.name }}</dd>
                </div>
                <div>
                  <dt>Location</dt>
                  <dd>{{ selectedAttention?.geography }}</dd>
                </div>
                <div>
                  <dt>Source</dt>
                  <dd>{{ selectedMetric.source }}</dd>
                </div>
              </dl>

              <div class="spotlight-value">
                <span class="spotlight-label">Current value</span>
                <strong>{{ formatMetricValue(selectedMetric) }}</strong>
                <span class="spotlight-change">{{ getDirectionLabel(selectedMetric) }} {{ formatDelta(selectedMetric) }}</span>
              </div>
            </div>
          </article>
        </section>

        <section class="pulse-section" aria-labelledby="pulse-title">
          <div class="section-heading">
            <div>
              <p class="eyebrow">02 / Overview</p>
              <h2 id="pulse-title">National Pulse</h2>
            </div>
            <p class="section-note">Current national indicators and their latest movement.</p>
          </div>

          <div class="metrics-grid">
            <VCard v-for="metric in metricCards" :key="metric.id" class="metric-card" variant="flat">
              <VCardText>
                <div class="metric-card-top">
                  <p class="metric-kicker">{{ metric.category }}</p>
                  <span class="metric-interpretation" :data-interpretation="metric.interpretation">
                    {{ getInterpretationLabel(metric) }}
                  </span>
                </div>

                <div class="metric-value-row">
                  <div>
                    <h3>{{ metric.name }}</h3>
                    <p class="metric-period">{{ metric.referencePeriod }}</p>
                  </div>
                  <strong>{{ formatMetricValue(metric) }}</strong>
                </div>

                <div class="metric-meta">
                  <span>{{ getDirectionLabel(metric) }}</span>
                  <span>{{ metric.comparisonLabel }}</span>
                </div>

                <div class="metric-footer">
                  <span>{{ metric.source }}</span>
                  <span>{{ metric.unit }}</span>
                </div>
              </VCardText>
            </VCard>
          </div>
        </section>

        <section class="detail-grid" aria-label="Economic and affordability detail">
          <article class="panel chart-panel" aria-labelledby="economic-title">
            <div class="section-heading compact-heading">
              <div>
                <p class="eyebrow">03 / Economic momentum</p>
                <h2 id="economic-title">Economic trend</h2>
              </div>
            </div>

            <div class="chart-wrap">
              <Line :data="chartData" :options="chartOptions" aria-label="Economic trend chart" />
            </div>
          </article>

          <article class="panel affordability-panel" aria-labelledby="affordability-title">
            <div class="section-heading compact-heading">
              <div>
                <p class="eyebrow">04 / Affordability</p>
                <h2 id="affordability-title">Canadians and affordability</h2>
              </div>
            </div>

            <ul class="affordability-list">
              <li>
                <span>Food-price change</span>
                <strong>+4.8%</strong>
              </li>
              <li>
                <span>Shelter-cost change</span>
                <strong>+5.1%</strong>
              </li>
              <li>
                <span>Wage growth</span>
                <strong>+3.7%</strong>
              </li>
              <li>
                <span>Housing starts</span>
                <strong>4.3k</strong>
              </li>
            </ul>
          </article>
        </section>

        <section class="lower-grid" aria-label="Regional comparison and national conditions">
          <article class="panel region-panel" aria-labelledby="regions-title">
            <div class="section-heading compact-heading">
              <div>
                <p class="eyebrow">05 / Regions</p>
                <h2 id="regions-title">Provincial and territorial comparison</h2>
              </div>
            </div>

            <div class="region-table" role="table" aria-label="Regional comparisons">
              <div class="region-row region-head" role="row">
                <span role="columnheader">Region</span>
                <span role="columnheader">Unemployment</span>
                <span role="columnheader">Population</span>
                <span role="columnheader">Starts</span>
              </div>
              <div v-for="region in regionalData" :key="region.name" class="region-row" role="row">
                <span role="cell">{{ region.name }}</span>
                <span role="cell">{{ region.unemployment.toFixed(1) }}%</span>
                <span role="cell">{{ region.populationGrowth.toFixed(1) }}%</span>
                <span role="cell">{{ region.housingStarts.toFixed(1) }}k</span>
              </div>
            </div>
          </article>

          <article class="panel conditions-panel" aria-labelledby="conditions-title">
            <div class="section-heading compact-heading">
              <div>
                <p class="eyebrow">06 / National conditions</p>
                <h2 id="conditions-title">National overview</h2>
              </div>
            </div>

            <div class="map-card" aria-label="National conditions placeholder map">
              <div class="map-glow"></div>
              <div class="map-grid">
                <span class="map-regions region-west">BC</span>
                <span class="map-regions region-plain">AB</span>
                <span class="map-regions region-east">ON</span>
                <span class="map-regions region-central">QC</span>
                <span class="map-regions region-atlantic">AT</span>
              </div>
            </div>
            <p class="conditions-note">Prototype conditions map showing areas with elevated affordability and labour pressure.</p>
          </article>
        </section>

        <footer class="page-footer">
          <span>Independent educational prototype</span>
          <span>Not an official Government of Canada product</span>
        </footer>
      </VContainer>
    </VMain>
  </VApp>
</template>
