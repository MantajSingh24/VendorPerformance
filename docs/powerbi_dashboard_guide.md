# 📊 Vendor Performance Dashboard - Power BI Build Guide

> **Goal**: Create a self-explanatory dashboard where anyone can understand everything at a glance.

---

## 🎨 DESIGN PRINCIPLES

1. **Every number needs context** - Don't just show "7 days", show "7 days avg delay ⚠️ (target: 0)"
2. **Use color strategically** - Green = good, Yellow = warning, Red = problem
3. **Add descriptive titles** - Not "Chart 1", but "Which vendors deliver on time?"
4. **Include benchmarks** - Show targets/goals alongside actuals
5. **Tell a story** - Layout should flow: Overview → Details → Actions

---

## 📐 RECOMMENDED LAYOUT (3 Pages)

### PAGE 1: Executive Overview
```
┌─────────────────────────────────────────────────────────────────────┐
│  🏢 VENDOR PERFORMANCE DASHBOARD                        [Filters]   │
│  "How are our vendors performing? Here's everything at a glance"    │
├─────────────┬─────────────┬─────────────┬─────────────┬─────────────┤
│   💰 TOTAL  │  📦 TOTAL   │  ✅ ON-TIME │  ⏱️ AVG     │  ⭐ AVG     │
│    SPEND    │   ORDERS    │    RATE     │   DELAY     │   RATING    │
│   $74,000   │     3       │    33%      │   3 days    │    4.2      │
│  ▲ vs goal  │  this year  │  ⚠️ below   │  target: 0  │  out of 5   │
├─────────────┴─────────────┴─────────────┴─────────────┴─────────────┤
│                                                                      │
│  📈 MONTHLY SPEND TREND                    🥇 TOP VENDORS BY SPEND   │
│  "Are we spending more or less over time?"  "Where does the $ go?"   │
│  [Area Chart with trend line]               [Donut Chart]            │
│                                                                      │
├──────────────────────────────────┬───────────────────────────────────┤
│  🚚 DELIVERY PERFORMANCE         │  💳 PAYMENT EFFICIENCY            │
│  "Who delivers on time?"         │  "How fast do we pay invoices?"   │
│  [Bar chart: On-time % by vendor]│  [Gauge: Avg days to pay]         │
│                                  │                                   │
└──────────────────────────────────┴───────────────────────────────────┘
```

### PAGE 2: Vendor Scorecard
```
┌─────────────────────────────────────────────────────────────────────┐
│  🏆 VENDOR SCORECARD                                                │
│  "Compare all vendors side-by-side on key metrics"                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  [Matrix/Table Visual]                                               │
│  ┌──────────────┬────────┬──────────┬──────────┬─────────┬────────┐ │
│  │ Vendor       │ Rating │ Spend    │ On-Time% │ Avg Delay│ Orders │ │
│  ├──────────────┼────────┼──────────┼──────────┼─────────┼────────┤ │
│  │ Alpha        │ ⭐4.5  │ $20,000  │ 100% ✅  │ -1 day  │ 2      │ │
│  │ Beta         │ ⭐4.2  │ $54,000  │ 0% ❌    │ 7 days  │ 1      │ │
│  │ Gamma        │ ⭐3.8  │ $0       │ N/A      │ N/A     │ 0      │ │
│  └──────────────┴────────┴──────────┴──────────┴─────────┴────────┘ │
│                                                                      │
├──────────────────────────────────┬───────────────────────────────────┤
│  📊 VENDOR COMPARISON RADAR      │  📋 VENDOR DETAILS               │
│  [Radar chart showing all        │  [Card showing selected vendor   │
│   metrics for selected vendor]   │   details with recommendations]  │
│                                  │                                   │
└──────────────────────────────────┴───────────────────────────────────┘
```

### PAGE 3: Operational Details
```
┌─────────────────────────────────────────────────────────────────────┐
│  🔍 OPERATIONAL DEEP DIVE                                           │
│  "Drill into the details - orders, deliveries, payments"            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  📦 ORDER FULFILLMENT                    💰 INVOICE STATUS           │
│  "Are we receiving what we ordered?"     "Payment tracking"          │
│  [Stacked bar: Ordered vs Received]      [Table with payment days]  │
│                                                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  📋 DETAILED TRANSACTIONS TABLE                                      │
│  [Full table with all POs, filterable by vendor/status/date]        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 DAX MEASURES TO CREATE

Copy these into Power BI (Modeling → New Measure):

### 1️⃣ CORE KPIs

```dax
// Total Spend - What's our total procurement spend?
Total Spend = 
SUM(purchase_orders[total_amount])
```

```dax
// Total Orders - How many purchase orders?
Total Orders = 
COUNTROWS(purchase_orders)
```

```dax
// Average Vendor Rating - Overall vendor quality
Avg Vendor Rating = 
AVERAGE(vendors[rating])
```

### 2️⃣ DELIVERY METRICS

```dax
// On-Time Delivery Rate - % of orders delivered on or before expected date
On-Time Rate = 
VAR TotalDeliveries = COUNTROWS(deliveries)
VAR OnTimeDeliveries = COUNTROWS(FILTER(deliveries, deliveries[delay_days] <= 0))
RETURN
DIVIDE(OnTimeDeliveries, TotalDeliveries, 0)
```

```dax
// Average Delay Days - How late are deliveries on average?
Avg Delay Days = 
AVERAGE(deliveries[delay_days])
```

```dax
// Late Deliveries Count - How many orders were late?
Late Deliveries = 
COUNTROWS(FILTER(deliveries, deliveries[delay_days] > 0))
```

```dax
// Early Deliveries Count - How many arrived early?
Early Deliveries = 
COUNTROWS(FILTER(deliveries, deliveries[delay_days] < 0))
```

### 3️⃣ PAYMENT METRICS

```dax
// Average Payment Days - How long to pay invoices?
Avg Payment Days = 
AVERAGEX(
    FILTER(invoices, NOT(ISBLANK(invoices[paid_date]))),
    DATEDIFF(invoices[invoice_date], invoices[paid_date], DAY)
)
```

```dax
// Total Invoiced Amount
Total Invoiced = 
SUM(invoices[invoice_amount])
```

```dax
// Paid Invoices Count
Paid Invoices = 
COUNTROWS(FILTER(invoices, NOT(ISBLANK(invoices[paid_date]))))
```

### 4️⃣ ORDER FULFILLMENT

```dax
// Fulfillment Rate - % of ordered quantity actually received
Fulfillment Rate = 
DIVIDE(
    SUM(po_line_items[qty_received]),
    SUM(po_line_items[qty_ordered]),
    0
)
```

```dax
// Quantity Shortfall - Units ordered but not received
Qty Shortfall = 
SUM(po_line_items[qty_ordered]) - SUM(po_line_items[qty_received])
```

### 5️⃣ CONTEXTUAL/DISPLAY MEASURES

```dax
// Spend with Currency Label
Spend Display = 
"$" & FORMAT([Total Spend], "#,##0")
```

```dax
// On-Time Rate with Status Emoji
On-Time Display = 
VAR Rate = [On-Time Rate]
RETURN
FORMAT(Rate, "0%") & 
IF(Rate >= 0.9, " ✅", IF(Rate >= 0.7, " ⚠️", " ❌"))
```

```dax
// Delay Status Text
Delay Status = 
VAR AvgDelay = [Avg Delay Days]
RETURN
IF(AvgDelay <= 0, "On Track ✅",
IF(AvgDelay <= 3, "Minor Delays ⚠️",
"Significant Delays ❌"))
```

```dax
// Vendor Performance Score (0-100)
Vendor Score = 
VAR RatingScore = DIVIDE([Avg Vendor Rating], 5, 0) * 40
VAR DeliveryScore = [On-Time Rate] * 40
VAR FulfillmentScore = [Fulfillment Rate] * 20
RETURN
RatingScore + DeliveryScore + FulfillmentScore
```

---

## 🎨 VISUAL CONFIGURATIONS

### KPI Cards (Top Row)
For each KPI card:
1. Use **Card** visual or **Multi-row Card**
2. Add these elements:
   - **Title**: Descriptive question (e.g., "How much did we spend?")
   - **Value**: The main number
   - **Subtitle**: Context (e.g., "Target: $50,000" or "vs. last month")

**Conditional Formatting for KPIs:**
- Go to Format → Data labels → Conditional formatting
- Set rules:
  - On-Time Rate < 70% = Red
  - On-Time Rate 70-90% = Yellow  
  - On-Time Rate > 90% = Green

### Spend Trend Chart
1. Visual: **Area Chart** or **Line Chart**
2. X-axis: `po_date` (set to Month)
3. Y-axis: `[Total Spend]`
4. Add **Trend line**: Analytics pane → Trend line → On
5. Title: "📈 Monthly Spend Trend - Are we spending more over time?"

### Vendor Spend Donut
1. Visual: **Donut Chart**
2. Legend: `vendor_name`
3. Values: `[Total Spend]`
4. Title: "🥇 Spend by Vendor - Where does our money go?"
5. Enable **Detail labels**: Show category + value + percentage

### On-Time by Vendor Bar Chart
1. Visual: **Clustered Bar Chart**
2. Y-axis: `vendor_name`
3. X-axis: `[On-Time Rate]`
4. **Conditional formatting** on bars:
   - < 70% = Red (#E74C3C)
   - 70-90% = Yellow (#F39C12)
   - > 90% = Green (#27AE60)
5. Title: "🚚 Delivery Performance - Who delivers on time?"
6. Add **Constant line** at 90% as target

### Vendor Scorecard Table
1. Visual: **Matrix** or **Table**
2. Columns:
   - vendor_name
   - rating (with data bars)
   - [Total Spend] (with data bars)
   - [On-Time Rate] (with conditional colors)
   - [Avg Delay Days] (with conditional colors)
   - [Total Orders]
3. Enable **Conditional formatting** for each metric

### Payment Gauge
1. Visual: **Gauge**
2. Value: `[Avg Payment Days]`
3. Min: 0
4. Max: 60
5. Target: 30 (payment terms)
6. Title: "💳 Payment Speed - Days to pay invoices (Target: 30)"

---

## 🖌️ STYLING RECOMMENDATIONS

### Color Palette
```
Primary Blue:    #2C3E50 (headers, titles)
Accent Blue:     #3498DB (highlights)
Success Green:   #27AE60 (good performance)
Warning Yellow:  #F39C12 (needs attention)
Danger Red:      #E74C3C (problems)
Background:      #F8F9FA (light gray)
Card Background: #FFFFFF (white)
```

### Fonts
- **Titles**: Segoe UI Semibold, 14pt
- **Values**: Segoe UI Bold, 24-36pt
- **Labels**: Segoe UI, 10pt

### Visual Borders
- Add subtle shadows or borders to cards
- Use consistent padding/margins
- Group related visuals together

---

## 📍 ADDING CONTEXT & ANNOTATIONS

### 1. Text Boxes for Section Headers
Add **Text Box** visuals with:
```
🏢 EXECUTIVE OVERVIEW
Everything you need to know about vendor performance at a glance.
Updated daily • Data from Jan 2024 - Present
```

### 2. Tooltips
For each visual, create custom **Tooltip pages**:
1. Create new page, set to "Tooltip" in Page information
2. Add detailed breakdown that appears on hover
3. Include: trend, comparison to target, recommendations

### 3. Bookmarks for Guided Experience
Create bookmarks for:
- "Overview" - default view
- "Problem Vendors" - filtered to show underperformers
- "Recent Orders" - last 30 days focus

---

## ✅ FINAL CHECKLIST

Before publishing, verify:

- [ ] Every chart has a descriptive title (question format works great)
- [ ] KPIs show targets/benchmarks for context
- [ ] Colors are consistent (green=good, red=bad)
- [ ] Filters are visible and labeled
- [ ] Mobile layout is configured
- [ ] Tooltips provide extra detail
- [ ] No orphan visuals (everything grouped logically)
- [ ] Date filter defaults to relevant period
- [ ] All measures display proper formatting ($, %, days)

---

## 🚀 QUICK START

1. Open `vendor_performance_Dashboard.pbix`
2. Go to **Modeling** → **New Measure**
3. Copy each DAX measure from above
4. Create visuals following the layout
5. Apply conditional formatting
6. Add text boxes for context
7. Test with filters
8. Publish!

---

*This guide will help you build a dashboard that tells the complete vendor performance story without needing any explanation.*
