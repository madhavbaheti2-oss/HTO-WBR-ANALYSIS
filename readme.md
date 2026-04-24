# HTO Business Framework — Analytics Repository

> **Live BI analysis and weekly reporting system for Lenskart's Home Try-On (HTO) channel,
> built on the Power BI Fabric semantic model via XMLA endpoint.**

---

## 📋 Overview

This repository contains the documentation, prompt templates, session memory, and report files for analysing the **HTO Business Framework** Power BI semantic model — the core analytics model for Lenskart's Home Try-On and Power Follow-Up (PFU) business.

The analysis was conducted by connecting directly to the live semantic model via Claude's Power BI MCP server, querying all 898 DAX measures across 113 tables, and producing structured Excel reports and BI documentation.

---

## 🏗️ Repository Structure

```
├── readme.md                          ← This file
├── memory.md                          ← Persistent session context (connection, terminology, benchmarks)
├── prompt.md                          ← 13 reusable prompt templates for future analysis sessions
├── reports/
│   ├── HTO_Weekly_v6.xlsx             ← Main weekly report workbook (6 tabs)
│   ├── HTO_6M_Performance_Report.xlsx ← 6-month rolling avg + Circle Head + Zone analysis
│   └── HTO_Dashboard_BI_Analysis.docx ← Full 12-tab Power BI dashboard documentation
```

---

## 📊 Weekly Report — HTO_Weekly_v6.xlsx

The main deliverable. Six tabs, all transposed (metrics = rows, dates = columns, latest week first).

| Tab | What It Shows |
|---|---|
| 📌 **Summary** | Executive summary comparing last 4 weeks vs prior 4 weeks. Wins, attention items, 3-tier action table (Immediate / Improve / Sustain) |
| 📋 **HTO Weekly** | 13 HTO KPIs across 26 weeks with ▲▼ vs prior 4WA and WoW % change |
| 📊 **HTO + PFU Overall** | Combined HTO + PFU measures (Overall Gross Revenue, blended ATV, Conversion with PFU) |
| 🔄 **PFU Only** | 9 PFU-specific KPIs — appointments, attendance, revenue (50% HTO share), cancel rate |
| ⭐ **NPS Tracking** | 10 NPS KPIs weekly + RM-level promoter/detractor breakdown vs overall benchmark |
| 📂 **Booking Category** | 4 appointment KPIs split by Organic / Affiliate / Insurance categories |

### Report Design Conventions
- **Rows = Metrics**, **Columns = Weeks** (latest first)
- Each week = **2 columns**: `Current ▲▼ vs 4WA` | `WoW Change` (absolute metrics only)
- **4WA** = rolling average of the prior 4 weeks (current week excluded)
- **▲ Green** = better than prior 4WA | **▼ Red** = worse than prior 4WA
- **WoW shown only for count / ₹ metrics** — not for %, rate, or ATV metrics
- Each tab ends with a **6M summary table**: Avg | Best Week (with date) | Worst Week (with date)

---

## 🔌 Connection Details

| Property | Value |
|---|---|
| Workspace | `Assisted Sales & HTO` |
| Semantic Model | `HTO Business Framework` |
| Connection Method | `ConnectFabric` |
| XMLA Endpoint | `powerbi://api.powerbi.com/v1.0/myorg/Assisted%20Sales%20%26%20HTO` |

> ⚠️ **Important:** Workspace GUIDs do **not** work with ConnectFabric. Always use the exact workspace display name string.

---

## 📖 Key Business Concepts

### HTO (Home Try-On)
A field visit model where a Field Executive (FE/Refractionist) visits the customer's home to conduct an eye test and assist with product selection. The FE is the primary conversion driver.

### PFU (Power Follow Up)
1. Customer places an online order for an eyeframe **without adding prescription power**
2. Customer self-books a home visit via the app at order time
3. The same HTO FE visits to conduct an eye test and add power
4. **Revenue is recorded only after power is added** — split 50% Online / 50% HTO
5. **Critical:** If the appointment is not attended, the order auto-cancels permanently — no recovery

### Booking Categories
| Category | Share | Attended % |
|---|---|---|
| Organic | 73.5% | 67.4% |
| Affiliate | 25.7% | 63.5% ⚠️ |
| Insurance | 0.9% | 85.3% ✓ |

### HTO Field Hierarchy
```
Circle Head → RM (Regional Manager) → AOM (Area Ops Manager) → FE (Field Executive)
```

---

## 📐 KPI Reference

### Core HTO Metrics
| KPI | Measure | Definition |
|---|---|---|
| Total Appointments | `[Total_appointments]` | All booked slots |
| Attended | `[att_appointments]` | FE successfully visited customer |
| Attended % | `[Attended_perc]` | Attended / Total |
| Conversion Rate | `[Conversion_perc]` | Gross Orders / Attended |
| Gross Revenue | `[Gross Revenue]` | Total order value (exchange-flag controlled) |
| Net Revenue | `[Net Sales]` | Gross Revenue − Return Revenue |
| ATV | `[Gross ATV]` | Gross Revenue / Gross Orders |
| Return Rev % | `[Return Rev %]` | Return Revenue / Gross Revenue |
| Revenue / Lead | `[Revenue_per_lead]` | Net Sales / Total Appointments |
| Cancellation % | `[Cancellation %]` | Non-attended / Total Appointments |

### Combined Metrics (HTO + PFU)
| KPI | Measure |
|---|---|
| Overall Gross Revenue | `[Overall Gross Revenue]` = HTO Rev + PFU HTO Rev (50%) |
| Overall Gross Orders | `[Overall Gross Orders]` = HTO Orders + PFU Attended |
| Blended ATV | `[ATV (PFU + HTO)]` |
| Conversion with PFU | `[Conversion (with PFU)]` |

### NPS Metrics
| KPI | Notes |
|---|---|
| NPS Score | `[NPS_1]` = Promoter% − Detractor%. Requires `USERELATIONSHIP(nps_date, Date)` |
| Response Rate | Valid responses / Gross Orders |
| Detractor % | Detractors / Total Responses |

---

## 🏆 FE Scorecard

| KPI | Score 4 | Score 3 | Score 2 | Score 1 | Weight |
|---|---|---|---|---|---|
| Attended % | ≥ 78% | ≥ 74% | ≥ 70% | < 70% | 25% |
| Conversion % (with PFU) | ≥ 83% | ≥ 80% | ≥ 77% | < 77% | 25% |
| Gross ATV | ≥ ₹4,500 | ≥ ₹4,200 | ≥ ₹3,900 | < ₹3,900 | 25% |
| Return Rev % | ≤ 9% | ≤ 10% | ≤ 11% | > 11% | 15% |
| Completed on Time % | ≥ 90% | ≥ 87.5% | ≥ 85% | < 85% | 10% |

**Grading:** Weighted Score ≥ 3.3 → Grade 4 | ≥ 3.0 → Grade 3 | ≥ 2.75 → Grade 2 | < 2.75 → Grade 1

---

## 📅 6-Month Performance Benchmarks (Oct 2025 – Mar 2026)

| Metric | 6M Average | Best Week | Worst Week |
|---|---|---|---|
| Total Appointments | ~6,781/week | 7,833 (22-Feb-26) | 4,380 (05-Oct-25) |
| Attended % | 66.9% | 68.5% (18-Jan-26) | 63.9% (05-Oct-25) |
| Conversion Rate | 53.9% | 61.1% (05-Oct-25) | 46.7% (22-Feb-26) |
| Gross Revenue | ~₹12.5 Cr/week | ₹13.8 Cr (07-Dec-25) | ₹8.4 Cr (05-Oct-25) |
| ATV | ₹5,120 | ₹5,315 (26-Oct-25) | ₹4,746 (09-Nov-25) |
| Return Rev % | 9.96% | 8.1% (05-Oct-25) | 11.3% (11-Jan-26) |
| Cancellation % | 33.1% | 31.5% (18-Jan-26) | 36.1% (05-Oct-25) |
| NPS Score | 83.38 | 84.6 (19-Oct-25) | 81.8 (30-Nov-25) |
| PFU Attended % | 76.0% | 82.4% (22-Mar-26) | 71.4% (28-Dec-25) |

---

## 🗂️ Available Categorisations

The semantic model supports slicing all metrics by the following dimensions:

| Category | Key Dimensions |
|---|---|
| 🏠 HTO Field Ops | HTO Zone, Circle Head, RM, AOM, FE, HTO Status, HTO Format (COCO/FOFO) |
| 📅 Appointments | Booking Category (Organic/Affiliate/Insurance), Cancellation Reason, Reschedule Count |
| 💰 Orders | Channel (HTO/Online/Offline), Order Type, Classification, Return Reason |
| 🛍️ Product | Brand, Category, Power Type (SV/PROG/BIFOCAL), Lens Package, Frame Material |
| 👤 Customer | New vs Repeat, LK Gold Tag, Valuable Customer Flag |
| 📍 Geography | State, City Tier, Market Tier, Metropolitan, Store Town Class |
| ⭐ NPS | NPS Scale, NPS Experience L1/L2, Sentiment |
| 🏪 Store | Format (COCO/FOFO), Store Maturity, Size Bucket |
| 📆 Time | Week, Month, Quarter, Fiscal Year, Weekday, Weekday Group |

---

## ⚠️ Known Data Quirks

| Issue | Detail |
|---|---|
| Coimbatore circle | Conversion% > 100% — data anomaly, always exclude |
| 25-Jan-26 week | Only 1,104 appointments — partial ISO week boundary |
| ` aom table` | Table name has a leading space — include it in all references |
| Revenue + booking_category | Revenue measures return totals regardless of booking_category filter |
| NPS date | Always use `USERELATIONSHIP(nps_date, Date)` — not default relationship |
| LY measures | Must include `Dim_Date_TimeLine[Filter] = TRUE()` to prevent double-counting |

---

## 🚀 Getting Started (Future Sessions)

```
1. Connect:
   "Connect to the semantic model HTO Business Framework in the Assisted Sales & HTO workspace."

2. Quick health check:
   "What was Attended%, Conversion%, and Gross Revenue for the last 4 weeks vs prior 4 weeks?"

3. Full report refresh:
   "Refresh HTO_Weekly_v6.xlsx with data up to [date]."

4. Deep dive:
   "Why has Conversion% changed in [period]? Break it down by zone and booking category."
```

See `prompt.md` for 13 detailed prompt templates covering all common analysis scenarios.

---

## 📝 Notes

- All reports use **live DAX queries** against the Fabric semantic model — no hardcoded revenue figures
- Booking category revenue analysis is **not supported** by the model's current measure design
- The 6-month analysis window is **Oct 2025 – Mar 2026** (26 ISO weeks)
- All week dates refer to the **Monday start date** of that ISO week
