---
name: wrench-charts
description: "Standardized chart generation for all Wrench.ai React deliverables. Trigger: build a chart, add a chart, data visualization, dashboard chart, analytics chart, bar chart, line chart, pie chart, area chart. Load this skill whenever you are generating or updating any chart component — Claude artifact, production React, or HTML report."
---

# Wrench.ai Chart Skill

This skill governs all data visualization work across every Wrench.ai deliverable. Read it before writing a single chart line.

---

## Library by Context

| Context | Library | Notes |
|---|---|---|
| Claude artifacts & wrench-plugin | **Recharts** | Copy components from `wrench-dna/docs/brand/charts/` |
| wrench-frontend (web app) | **ECharts** (`echarts-for-react`) | Use `useChartsStyle` + `CHART_SERIES_COLORS` |

**Same brand tokens apply in both libraries.** Visual output must look identical regardless of which library renders it.

---

## Source Files — Recharts (Claude artifacts + wrench-plugin)

| File | Purpose |
|---|---|
| `wrench-dna/docs/brand/charts/tokens.ts` | All color and style constants |
| `wrench-dna/docs/brand/charts/ChartTooltip.tsx` | Custom tooltip (required on every chart) |
| `wrench-dna/docs/brand/charts/ChartCard.tsx` | Card wrapper (required on every chart) |
| `wrench-dna/docs/brand/charts/WrenchBarChart.tsx` | Bar / column chart |
| `wrench-dna/docs/brand/charts/WrenchLineChart.tsx` | Line / trend chart |
| `wrench-dna/docs/brand/charts/WrenchAreaChart.tsx` | Area / volume chart |
| `wrench-dna/docs/brand/charts/WrenchPieChart.tsx` | Pie and donut chart |
| `wrench-dna/docs/brand/charts/WrenchComboChart.tsx` | Combo bar + line |
| `wrench-dna/docs/brand/charts/chart-type-guide.md` | Chart selection decision tree |

## Source Files — ECharts (wrench-frontend only)

| File | Purpose |
|---|---|
| `src/constants/chart-color.constant.ts` | Brand palette constants + `CHART_SERIES_COLORS` ordered array |
| `src/hooks/useChartsStyle.ts` | ECharts option fragments (axis, tooltip, legend, pie) aligned to brand tokens |

**Install Recharts in wrench-plugin if missing:** `pnpm add recharts`

---

## The 5 Non-Negotiable Rules

1. **Every chart uses `<ChartCard>`** — never a naked chart on the dark background.
2. **Every chart uses `<ChartTooltip>`** — never the Recharts default tooltip.
3. **Colors come from `CHART_COLORS`** — never hardcode hex, never use library defaults.
4. **Grid lines: horizontal only, dashed** — `<CartesianGrid vertical={false} strokeDasharray="3 3" stroke={CHART_TOKENS.gridColor} />`
5. **Legend below the chart, centered** — never at the top.

Break any of these and the chart is non-compliant regardless of the data.

---

## Step 1 — Choose the Right Chart Type

Read `chart-type-guide.md` or use this quick rule:

| What you're showing | Chart |
|---|---|
| Volume over time (area under line matters) | `WrenchAreaChart` |
| Trend/direction over time | `WrenchLineChart` |
| Comparison between categories | `WrenchBarChart` |
| Part-to-whole (≤ 6 slices) | `WrenchPieChart` |
| Volume + rate on same canvas | `WrenchComboChart` |
| Actual vs. target line | `WrenchLineChart` with `dashed: true` on the target series |

**When not sure:** Default to `WrenchBarChart`. It handles more situations correctly than a pie.

---

## Step 2 — Import the Right Components

For **Claude artifacts** and **one-off React files**, copy the component source directly from the files above and include it in the artifact.

For **wrench-plugin** (new chart work):
```tsx
// Copy component source from wrench-dna/docs/brand/charts/ into:
// pages/content-ui/src/components/charts/
import { WrenchBarChart } from '@/components/charts/WrenchBarChart';
```

For **wrench-frontend** (uses ECharts — do NOT add Recharts):
```tsx
import { CHART_SERIES_COLORS } from '@/constants/chart-color.constant';
import useChartsStyle from '@/hooks/useChartsStyle';

// Build an ECharts option object using the brand-aligned hooks:
const { styles } = useChartsStyle();
const option = {
  backgroundColor: styles.colors.background,
  textStyle: { color: styles.colors.text, fontFamily: 'Poppins, system-ui, sans-serif' },
  tooltip: { ...styles.tooltip },
  legend: { ...styles.legend },
  xAxis: { ...styles.axis, type: 'category', data: xValues },
  yAxis: { ...styles.axis, type: 'value' },
  series: data.map((s, i) => ({
    ...s,
    color: CHART_SERIES_COLORS[i % CHART_SERIES_COLORS.length],
  })),
};
```

Do not publish the wrench-dna chart files as an npm package — copy-paste them into the target repo.

---

## Step 3 — Wire Up Data

Data must be an array of plain objects. The `xDataKey` must be a string that exists in every row.

```tsx
// ✅ Correct
const data = [
  { week: 'Jan W1', score: 74, target: 70 },
  { week: 'Jan W2', score: 78, target: 70 },
];

// ❌ Wrong — nested objects break Recharts
const data = [{ week: 'Jan W1', metrics: { score: 74 } }];
```

---

## Step 4 — Format Values Consistently

Import formatters from `tokens.ts`:

| Situation | Formatter | Example output |
|---|---|---|
| Raw counts | `formatNumber(v)` | `1.2K`, `45`, `1.2M` |
| Dollar amounts | `formatCurrency(v)` | `$45K`, `$1.2M` |
| Percentages | `formatPercent(v)` | `84.7%` |
| Scores (0–100) | `(v: number) => \`${v} / 100\`` | `82 / 100` |

Pass formatters to both `formatY` (axis ticks) and `formatTooltipValue` (tooltip):
```tsx
<WrenchBarChart
  formatY={formatCurrency}
  formatTooltipValue={(v) => formatCurrency(Number(v))}
  ...
/>
```

---

## Step 5 — Write Good Titles and Subtitles

Every `ChartCard` requires a `title`. `subtitle` is strongly recommended.

| Prop | Rule |
|---|---|
| `title` | Specific noun phrase. What metric, what dimension. "Lead Score by Channel" not "Scores" |
| `subtitle` | Time range + context. "Weekly average — last 12 weeks" |

Series `name` props in bars/lines must be human-readable — never expose raw `dataKey` strings to users.

---

## Step 6 — Mobile Responsiveness

`<ResponsiveContainer width="100%" height={height}>` handles width automatically. For height:

```tsx
// Derive height from viewport or pass a responsive value
const chartHeight = window.innerWidth < 480 ? 220 : 300;

<WrenchBarChart height={chartHeight} ... />
```

For dense X-axis labels on mobile, reduce tick density:
```tsx
<XAxis interval={Math.ceil(data.length / 6) - 1} ... />
```

---

## Tooltip Content Guidelines

Write tooltip content as a senior analyst reading the data aloud:
- Label: the category or date ("LinkedIn", "Jan W3")
- Value: the formatted metric ("82 / 100", "$45K", "84.7%")
- Multi-series: show all series in one tooltip with colored dots + names

If the chart shows a score, color-code values in the tooltip:
```tsx
valueFormatter={(v, name) => {
  if (name === 'Lead Score') {
    const score = Number(v);
    return `${score} / 100`;
  }
  return formatNumber(Number(v));
}}
```

---

## Hover & Active States

The chart components handle hover automatically:
- **Bar:** cursor highlight (`rgba(154, 173, 192, 0.06)`) on the bar background
- **Line/Area:** active dot enlarges by 2px, stroked with card background color
- **Pie:** active segment expands by 6px; inactive segments dim to 55% opacity

Do not add additional CSS hover effects on chart containers beyond what `ChartCard` provides.

---

## Chart Checklist (run before shipping)

```
[ ] Chart is wrapped in <ChartCard> with specific title
[ ] Custom <ChartTooltip> is used (not Recharts default)
[ ] Colors come from CHART_COLORS[] — no hardcoded hex
[ ] CartesianGrid: vertical={false}, strokeDasharray="3 3"
[ ] XAxis + YAxis: axisLine={false}, tickLine={false}
[ ] Y-axis tick labels use formatNumber / formatCurrency / formatPercent
[ ] Legend is below the chart (not at top) — or omitted for single-series
[ ] Series name props are human-readable
[ ] Chart title and subtitle are specific, not generic
[ ] ResponsiveContainer width="100%"
[ ] Tested at mobile width (< 480px)
```

---

## Anti-Patterns — Never Do These

| Anti-Pattern | Fix |
|---|---|
| `<Tooltip />` (default) | `<Tooltip content={<ChartTooltip />} />` |
| `fill="#8884d8"` (Recharts default) | `fill={CHART_COLORS[0]}` |
| White or grey-100 card background | `background: CHART_TOKENS.cardBg` (#243347) |
| Solid grid lines on both axes | `<CartesianGrid vertical={false} strokeDasharray="3 3" />` |
| `<Legend layout="horizontal" verticalAlign="top" />` | Bottom, centered — default in components |
| Pie with 8 slices | Max 6 slices; aggregate rest into "Other" |
| `fontFamily: 'sans-serif'` | `fontFamily: CHART_FONTS.body` (Poppins) |
| Axis tick label showing `1000000` | Use `formatNumber` → `"1M"` |
| Chart with title "Chart" | Specific noun phrase required |
| `barRadius` on all four corners | `radius={[4, 4, 0, 0]}` — top corners only |

---

## Reference: Full Token Values

```typescript
// Chart palette (assign in order)
CHART_COLORS = ['#8697F5', '#9EEFC7', '#B797F7', '#F4AD8F', '#92F4F6', '#86C7F5', '#F9A9A9', '#8BA4C0']

// Surfaces
cardBg:         '#243347'   // chart card background
cardBorder:     '#36404A'   // card border
tooltipBg:      '#2C3E55'   // tooltip background
tooltipBorder:  '#36404A'   // tooltip border

// Text & grid
axisColor:      '#687B8E'   // tick labels
labelColor:     '#9AADC0'   // tooltip label, legend text
gridColor:      'rgba(154, 173, 192, 0.12)'

// Semantic (score coloring only)
success:        '#6DD48F'   // score ≥ 70
warning:        '#F4AD8F'   // score 40–69
danger:         '#F8285A'   // score < 40

// Element sizing
lineWidth:      2
dotRadius:      4
barRadius:      4            // top corners only
barSize:        24
areaOpacity:    0.15
```
