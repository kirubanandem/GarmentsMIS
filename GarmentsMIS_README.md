# 🏭 GarmentsMIS — Enterprise Garments Production Monitoring System
### Complete Project Blueprint & Development Checklist

---

## 📌 Project Overview

| Item | Detail |
|---|---|
| **System Name** | GarmentsMIS |
| **Type** | Enterprise Production Monitoring System |
| **Industry** | Garments / Apparel Manufacturing |
| **Locations** | 10+ factories (multi-location enterprise) |
| **Database** | MS SQL Server 2014 |
| **Desktop App** | VB.NET WinForms (.NET Framework 4.8) |
| **Web Dashboard** | ASP.NET WebForms 4.8 |
| **Future Mobile** | Android App + Android TV (REST API) |
| **IDE** | Visual Studio 2022 Community |
| **DB Tool** | SQL Server Management Studio 19 |

---

## 🧱 Tech Stack

| Layer | Technology | License |
|---|---|---|
| Database | MS SQL Server 2014 | Commercial |
| Data Access | ADO.NET + Parameterized Stored Procedures | Built-in |
| Desktop UI | VB.NET WinForms .NET Framework 4.8 | Free |
| UI Theme | MaterialSkin2 | MIT Free |
| Charts | LiveCharts2 + ScottPlot | MIT Free |
| Grid Control | ObjectListView (BrightIdeasSoftware) | GPL Free |
| Reports | RDLC + Microsoft ReportViewer | Free |
| Excel Export | EPPlus | MIT Free |
| PDF Export | PdfSharp | MIT Free |
| QR/Barcode | ZXing.Net | Apache 2.0 Free |
| Web Dashboard | ASP.NET WebForms 4.8 | Free |
| Future API | ASP.NET Web API 2 | Free |
| Security | SHA256 + Salt (.NET built-in) | Built-in |
| Logging | NLog | MIT Free |

---

## 🗂️ Solution Structure

```
GarmentsMIS.sln
├── GarmentsMIS.DB/                  → SQL Scripts (Tables, SPs, Views, Seed)
├── GarmentsMIS.Core/                → VB.NET Class Library (Models, Helpers, Enums)
├── GarmentsMIS.DataAccess/          → VB.NET Class Library (Repositories, SP Callers)
├── GarmentsMIS.WinApp/              → VB.NET WinForms Desktop App
├── GarmentsMIS.Web/                 → ASP.NET WebForms Dashboard
└── GarmentsMIS.API/                 → ASP.NET Web API 2 (for Android, future)
```

---

## 📊 Data Hierarchy

```
Company (Group)
  └── Location (Factory)
        └── Department (Cutting / Printing / Embroidery / Sewing / Checking / Packing / Shipping + custom)
              └── Production Line (Line 1, Line 2 ... n)
                    └── Shift (flexible per location)
                          └── Operator (multi-skilled, can move lines/operations)
                                └── Operation (with SMV per style+product type)

Customer (Buyer)
  └── Size Range (buyer-specific: XS-XXL / 2Y-14Y / 34-46 EU ...)
        └── Order (PO — string code)
              └── Style (string code)
                    └── Product Type (Polo Shirt / Trouser / Jacket ... multi per style)
                          └── Colour × Size Qty Matrix
                                └── Operations (sequence + SMV per product type)
                                      └── Dept Flow (template or custom per order)

Bundle (physical unit)
  └── Created at Cutting (QR printed)
        └── Scanned at each department entry/exit
              └── Status: Created→Issued→In-Process→Completed→QC-Hold→Passed→Packed→Shipped
```

---

## 🗄️ DATABASE — 47 Tables

### Group 1 — System & Security (8 tables)
| # | Table | Description |
|---|---|---|
| 1 | tbl_Companies | Holding company / group master |
| 2 | tbl_Locations | Factory locations (10+) |
| 3 | tbl_Users | All system users |
| 4 | tbl_Roles | Role definitions |
| 5 | tbl_RolePermissions | Form/feature permissions per role |
| 6 | tbl_UserLocationMap | User ↔ allowed locations |
| 7 | tbl_AuditLog | Every insert/update/delete logged |
| 8 | tbl_SystemConfig | Global + per-location config keys |

### Group 2 — Language & Currency (4 tables)
| # | Table | Description |
|---|---|---|
| 9 | tbl_Languages | EN, TA, HI, etc. |
| 10 | tbl_Translations | Key-value pairs per language |
| 11 | tbl_Currencies | USD, INR, EUR, GBP... |
| 12 | tbl_CurrencyRates | Daily exchange rates |

### Group 3 — Factory Masters (8 tables)
| # | Table | Description |
|---|---|---|
| 13 | tbl_Departments | Dept master (cutting to shipping + custom) |
| 14 | tbl_DeptFlowTemplates | Default flow templates per product type |
| 15 | tbl_DeptFlowSteps | Steps within each flow template |
| 16 | tbl_Shifts | Shifts per location (flexible) |
| 17 | tbl_ShiftBreaks | Break slots within each shift |
| 18 | tbl_Lines | Production lines per department |
| 19 | tbl_Machines | Machines per line with barcode |
| 20 | tbl_DefectCategories | Defect types per department |

### Group 4 — Operator Masters (4 tables)
| # | Table | Description |
|---|---|---|
| 21 | tbl_Operators | Operator master with badge/photo |
| 22 | tbl_OperatorSkills | Multi-skill mapping with level |
| 23 | tbl_LineAssignments | Daily operator ↔ line ↔ operation assignment |
| 24 | tbl_OperatorMovements | Intraday line/operation movement log |

### Group 5 — Order & Style Masters (13 tables)
| # | Table | Description |
|---|---|---|
| 25 | tbl_ProductTypes | Polo / Trouser / Jacket ... (user-extendable) |
| 26 | tbl_Customers | Buyer master with currency + language |
| 27 | tbl_BuyerReportFormats | Custom report templates per buyer |
| 28 | tbl_SizeRanges | Size range sets per buyer |
| 29 | tbl_SizeRangeDetails | Individual sizes within each range |
| 30 | tbl_Colours | Global colour master with hex code |
| 31 | tbl_Orders | PO master (string order codes) |
| 32 | tbl_Styles | Style master (string style codes) |
| 33 | tbl_OrderStyleProducts | Style + ProductType combo per order |
| 34 | tbl_OrderStyleColours | Colours assigned per style+product |
| 35 | tbl_OrderQtyMatrix | Colour × Size quantity grid |
| 36 | tbl_Operations | Operations per style+product with SMV |
| 37 | tbl_DeptFlowOverrides | Custom dept flow per order+style |

### Group 6 — Production Transactions (8 tables)
| # | Table | Description |
|---|---|---|
| 38 | tbl_TargetSettings | Targets per line/shift/date (all 4 methods) |
| 39 | tbl_Attendance | Daily operator attendance per shift |
| 40 | tbl_HourlyProduction | Core hourly output entry per line |
| 41 | tbl_OperatorProduction | Operator-level hourly breakdown |
| 42 | tbl_DefectLog | Defects per entry, category, operator |
| 43 | tbl_DowntimeLog | Downtime reason + duration per line |
| 44 | tbl_WIPRegister | Opening/closing WIP per dept/line/date |
| 45 | tbl_QualityAudit | Inline / end-line / final audit records |

### Group 7 — Bundle & Shipping (5 tables)
| # | Table | Description |
|---|---|---|
| 46 | tbl_BundleTypes | Bundle size configurations |
| 47 | tbl_Bundles | Each physical bundle with QR/barcode |
| 48 | tbl_BundleMovements | Full scan history dept-to-dept |
| 49 | tbl_PackingList | Carton packing details |
| 50 | tbl_ShippingLog | Final dispatch + shipping records |

### Group 8 — Alerts (2 tables)
| # | Table | Description |
|---|---|---|
| 51 | tbl_AlertRules | Configurable alert thresholds |
| 52 | tbl_AlertLog | Alert trigger history |

---

## 📋 STORED PROCEDURES — 65+ SPs

### Security & Users
- sp_User_Login
- sp_User_Create / Update / Deactivate
- sp_Role_GetPermissions
- sp_AuditLog_Insert

### Masters
- sp_Company_CRUD
- sp_Location_CRUD
- sp_Department_CRUD
- sp_Shift_CRUD / sp_ShiftBreak_CRUD
- sp_Line_CRUD
- sp_Machine_CRUD
- sp_DefectCategory_CRUD
- sp_ProductType_CRUD
- sp_Customer_CRUD
- sp_SizeRange_CRUD / sp_SizeRangeDetail_CRUD
- sp_Colour_CRUD
- sp_Order_CRUD
- sp_Style_CRUD
- sp_OrderStyleProduct_CRUD
- sp_OrderQtyMatrix_Save
- sp_Operation_CRUD
- sp_DeptFlowTemplate_CRUD

### Operators
- sp_Operator_CRUD
- sp_OperatorSkill_Save
- sp_LineAssignment_Save
- sp_OperatorMovement_Log

### Production Entry
- sp_Attendance_Save
- sp_Target_Calculate (all 4 methods)
- sp_Target_Override
- sp_HourlyProduction_Save
- sp_HourlyProduction_Validate (over-production check)
- sp_OperatorProduction_Save
- sp_DefectLog_Save
- sp_Downtime_Save
- sp_WIP_Update
- sp_QualityAudit_Save

### Bundle Tracking
- sp_Bundle_Create
- sp_Bundle_Scan
- sp_Bundle_Move
- sp_Bundle_StatusUpdate
- sp_Bundle_Override (supervisor)

### Reports
- sp_rpt_HourlyProduction
- sp_rpt_HourlyComparison
- sp_rpt_ShiftSummary
- sp_rpt_DailyProductionSummary
- sp_rpt_LineEfficiency
- sp_rpt_OperatorEfficiency
- sp_rpt_OperatorPerformance
- sp_rpt_OperatorSkillMatrix
- sp_rpt_OperationEfficiency
- sp_rpt_MachineUtilization
- sp_rpt_DowntimeAnalysis
- sp_rpt_OrderProgress
- sp_rpt_StyleWiseProduction
- sp_rpt_ProductTypeWise
- sp_rpt_SMVAnalysis
- sp_rpt_SAHReport
- sp_rpt_CutToShip
- sp_rpt_OrderForecast
- sp_rpt_DeliveryPerformance
- sp_rpt_DHU
- sp_rpt_DefectPareto
- sp_rpt_QualityAudit
- sp_rpt_AlterationRejection
- sp_rpt_WIPStatus
- sp_rpt_WIPMovement
- sp_rpt_BundleTracking
- sp_rpt_LineFeedingReport
- sp_rpt_PackingShipping
- sp_rpt_AttendanceSummary
- sp_rpt_ManpowerPlanning
- sp_rpt_LabourCost
- sp_rpt_LearningCurve
- sp_rpt_CMCost
- sp_rpt_CurrencyConversion
- sp_rpt_FactoryKPI
- sp_rpt_MultiLocationComparison
- sp_rpt_BuyerSummary
- sp_rpt_MonthlyMIS
- sp_rpt_BuyerCustomFormat

### Alerts & Sync
- sp_Alert_Check
- sp_Alert_Dismiss
- sp_Sync_GetPending
- sp_Sync_MarkComplete

---

## 🖥️ WINFORMS MODULES — Development Checklist

---

### 🔐 PHASE 1A — Database Foundation
- [ ] **1A.01** Create database `GarmentsMIS`
- [ ] **1A.02** Create all 52 tables with constraints + indexes
- [ ] **1A.03** Create all foreign key relationships
- [ ] **1A.04** Create database views (20+ reporting views)
- [ ] **1A.05** Create all stored procedures (65+)
- [ ] **1A.06** Create seed data (roles, languages, currencies, default config)
- [ ] **1A.07** Create default admin user
- [ ] **1A.08** Test all SPs with sample data

---

### 🏗️ PHASE 1B — Solution Scaffold & Core Libraries
- [ ] **1B.01** Create Visual Studio solution with 5 projects
- [ ] **1B.02** Install NuGet packages (MaterialSkin2, LiveCharts2, ScottPlot, ZXing.Net, EPPlus, PdfSharp, NLog, ObjectListView)
- [ ] **1B.03** GarmentsMIS.Core — Model classes (52 models)
- [ ] **1B.04** GarmentsMIS.Core — Enums (Status, AlertType, EntryType, SkillLevel...)
- [ ] **1B.05** GarmentsMIS.Core — SecurityHelper (SHA256+Salt)
- [ ] **1B.06** GarmentsMIS.Core — LanguageHelper (runtime translation engine)
- [ ] **1B.07** GarmentsMIS.Core — CurrencyHelper (conversion engine)
- [ ] **1B.08** GarmentsMIS.Core — TargetCalculator (all 4 methods)
- [ ] **1B.09** GarmentsMIS.Core — EfficiencyCalculator (SMV-based)
- [ ] **1B.10** GarmentsMIS.Core — BarcodeHelper (ZXing.Net wrapper)
- [ ] **1B.11** GarmentsMIS.DataAccess — DBConnection (central connection manager)
- [ ] **1B.12** GarmentsMIS.DataAccess — StoredProcCaller (parameterized executor)
- [ ] **1B.13** GarmentsMIS.DataAccess — All Repository classes

---

### 🔑 PHASE 1C — WinForms Shell & Security
- [ ] **1C.01** frmSplash — Splash screen with version info
- [ ] **1C.02** frmLogin — Multi-location login + language selector
- [ ] **1C.03** frmMain — MDI shell with sidebar navigation + toolbar
- [ ] **1C.04** frmChangePassword — Forced change on first login
- [ ] **1C.05** frmUserManagement — Create/edit users, assign roles + locations
- [ ] **1C.06** frmRolePermissions — Configure role access per module
- [ ] **1C.07** Session management — Timeout + auto-lock
- [ ] **1C.08** Navigation helper — Role-based menu visibility

---

### 🏭 PHASE 1D — Master Setup Forms
- [ ] **1D.01** frmCompany — Company / group master
- [ ] **1D.02** frmLocation — Factory locations
- [ ] **1D.03** frmDepartment — Departments + sequence + custom add
- [ ] **1D.04** frmDeptFlowTemplate — Flow templates per product type
- [ ] **1D.05** frmShift — Shifts per location + break slots
- [ ] **1D.06** frmLine — Production lines per department
- [ ] **1D.07** frmMachine — Machines per line + barcode assign
- [ ] **1D.08** frmDefectCategory — Defect types per department
- [ ] **1D.09** frmProductType — Product type master (user-extendable)
- [ ] **1D.10** frmCustomer — Buyer master + currency + size range link
- [ ] **1D.11** frmSizeRange — Size range sets per buyer + size details
- [ ] **1D.12** frmColour — Global colour master
- [ ] **1D.13** frmOrder — PO entry (string codes) + order lines
- [ ] **1D.14** frmStyle — Style master + product type assignment
- [ ] **1D.15** frmOrderQtyMatrix — Colour × Size qty grid entry
- [ ] **1D.16** frmOperation — Operations per style+product + SMV
- [ ] **1D.17** frmOperator — Operator master + badge + photo
- [ ] **1D.18** frmOperatorSkills — Multi-skill assignment with level
- [ ] **1D.19** frmBundleType — Bundle size master
- [ ] **1D.20** frmBuyerReportFormat — Custom report templates per buyer

---

### 📥 PHASE 2A — Data Entry Forms
- [ ] **2A.01** frmAttendance — Daily attendance per line/shift
- [ ] **2A.02** frmTargetSetting — Set/override line targets
- [ ] **2A.03** frmLineAssignment — Assign operators to lines/operations daily
- [ ] **2A.04** frmHourlyEntry — Core hourly production entry (manual)
- [ ] **2A.05** frmBundleScan — QR/barcode scan terminal
- [ ] **2A.06** frmDefectEntry — Defect logging per hour/operator
- [ ] **2A.07** frmDowntimeEntry — Downtime reason + duration
- [ ] **2A.08** frmWIPEntry — WIP count per department
- [ ] **2A.09** frmQualityAudit — Inline / end-line / final audit
- [ ] **2A.10** frmShippingEntry — Dispatch + carton + weight details
- [ ] **2A.11** Over-production validation popup + supervisor override flow

---

### 📊 PHASE 2B — WinForms Dashboards (7 Dashboards)
- [ ] **2B.01** dashFactory — Factory overview: all lines KPI cards + alerts
- [ ] **2B.02** dashLine — Line performance: hourly bar chart + operator grid
- [ ] **2B.03** dashOrder — Order progress: completion bars + delivery countdown
- [ ] **2B.04** dashQuality — DHU trend + defect Pareto + audit heatmap
- [ ] **2B.05** dashHQOverview — Multi-location comparison (HQ view)
- [ ] **2B.06** dashHourlyComparison — Today vs yesterday vs last week
- [ ] **2B.07** dashWIPFlow — Visual WIP pipeline dept-by-dept

---

### 📄 PHASE 2C — Reports (30+ Reports)

#### Time & Efficiency Reports
- [ ] **2C.01** rptHourlyProduction — Output vs target per hour per line
- [ ] **2C.02** rptHourlyComparison — Today vs yesterday vs same day last week
- [ ] **2C.03** rptShiftSummary — Total output + efficiency per shift
- [ ] **2C.04** rptDailyProductionSummary — All lines, all depts, one page
- [ ] **2C.05** rptLineEfficiency — Efficiency trend per line (daily/weekly/monthly)
- [ ] **2C.06** rptOperatorEfficiency — Individual output vs SMV target
- [ ] **2C.07** rptOperationEfficiency — Bottleneck analysis per operation
- [ ] **2C.08** rptMachineUtilization — Running vs idle time
- [ ] **2C.09** rptDowntimeAnalysis — Reason-wise downtime hrs + % impact

#### Order & Style Reports
- [ ] **2C.10** rptOrderProgress — Qty cut/sewn/checked/packed vs PO qty
- [ ] **2C.11** rptStyleWiseProduction — Output per style across lines/depts
- [ ] **2C.12** rptProductTypeWise — Comparison across product types
- [ ] **2C.13** rptSMVAnalysis — Earned SMV vs available SMV
- [ ] **2C.14** rptSAH — Standard Allowed Hours consumed per order
- [ ] **2C.15** rptCutToShip — Bundle flow from cutting to dispatch
- [ ] **2C.16** rptOrderForecast — Projected finish vs delivery date
- [ ] **2C.17** rptDeliveryPerformance — On-time vs delayed orders history

#### Operator & HR Reports
- [ ] **2C.18** rptOperatorPerformance — Daily/weekly output + efficiency + DHU
- [ ] **2C.19** rptOperatorSkillMatrix — Operations each operator is trained for
- [ ] **2C.20** rptAttendanceSummary — Present/absent/late per line/dept/location
- [ ] **2C.21** rptManpowerPlanning — Required vs actual headcount per line
- [ ] **2C.22** rptLabourCost — Cost per unit based on wages + SMV
- [ ] **2C.23** rptLearningCurve — New operator efficiency progression

#### Quality Reports
- [ ] **2C.24** rptDHU — Defects per Hundred Units per line/style/operator
- [ ] **2C.25** rptDefectPareto — Top 10 defect types (Pareto chart)
- [ ] **2C.26** rptInlineAudit — Hourly inline check results
- [ ] **2C.27** rptFinalAudit — Final inspection pass/fail
- [ ] **2C.28** rptAlterationRejection — Rework qty + reason + time lost

#### WIP & Flow Reports
- [ ] **2C.29** rptWIPStatus — WIP qty at each department (current)
- [ ] **2C.30** rptWIPMovement — Bundle flow history dept to dept
- [ ] **2C.31** rptBundleTracking — Single bundle full journey scan log
- [ ] **2C.32** rptLineFeedingReport — Bundles issued to each line per day
- [ ] **2C.33** rptPackingShipping — Packed cartons, weights, dispatch log

#### Management & Buyer Reports
- [ ] **2C.34** rptFactoryKPI — Efficiency + DHU + OTD + Attendance in one view
- [ ] **2C.35** rptMultiLocationComparison — All factories side by side
- [ ] **2C.36** rptBuyerSummary — All orders per buyer, status + delivery
- [ ] **2C.37** rptBuyerCustomFormat — Per-buyer template reports
- [ ] **2C.38** rptMonthlyMIS — Full month summary for management
- [ ] **2C.39** rptCMCost — Cut & Make cost per style/order
- [ ] **2C.40** rptCurrencyConversion — Order value in buyer vs base currency

#### Export Engine
- [ ] **2C.41** Export to Excel (EPPlus) — all reports
- [ ] **2C.42** Export to PDF (PdfSharp) — all reports
- [ ] **2C.43** Print via RDLC ReportViewer — all reports

---

### 🔔 PHASE 2D — Alert Engine
- [ ] **2D.01** frmAlertRules — Configure thresholds per location/line
- [ ] **2D.02** frmAlertDashboard — Live alert viewer in main shell
- [ ] **2D.03** Alert: Low Efficiency (< 60% for 2 consecutive hours)
- [ ] **2D.04** Alert: Target Miss (output < 80% of hourly target)
- [ ] **2D.05** Alert: High DHU (DHU > configurable threshold)
- [ ] **2D.06** Alert: WIP Buildup (WIP > defined limit between depts)
- [ ] **2D.07** Alert: Low Attendance (< 85% of line strength)
- [ ] **2D.08** Alert: Bundle Stuck (not moved for > configurable hours)
- [ ] **2D.09** Alert: Delivery Risk (completion % behind schedule)
- [ ] **2D.10** Background alert checker (timer-based, configurable interval)

---

### 🌐 PHASE 3A — Web Dashboard (ASP.NET WebForms)
- [ ] **3A.01** Default.aspx — HQ overview: all factories KPI cards
- [ ] **3A.02** Factory.aspx — Single factory live view
- [ ] **3A.03** Line.aspx — Line-level hourly chart (optimized for TV display)
- [ ] **3A.04** Orders.aspx — Order tracker across locations
- [ ] **3A.05** Quality.aspx — DHU heatmap per line/style
- [ ] **3A.06** Attendance.aspx — Today's manpower across factories
- [ ] **3A.07** Alerts.aspx — Live alerts feed (auto-refresh)
- [ ] **3A.08** Reports.aspx — Filterable report viewer
- [ ] **3A.09** Sync.aspx — Sync status per location (HQ only)
- [ ] **3A.10** Auto-refresh engine (SignalR or meta-refresh configurable)

---

### 🌍 PHASE 3B — Multi-Language & Multi-Currency
- [ ] **3B.01** Language manager — runtime label translation engine
- [ ] **3B.02** frmLanguageEditor — Add/edit translations in-app
- [ ] **3B.03** Seed translations: English (EN) base labels
- [ ] **3B.04** Seed translations: Tamil (TA)
- [ ] **3B.05** Seed translations: Hindi (HI)
- [ ] **3B.06** Currency conversion engine
- [ ] **3B.07** frmCurrencyRates — Daily rate entry / update
- [ ] **3B.08** Apply currency conversion in all commercial reports
- [ ] **3B.09** Language switcher in login + settings (no restart needed)

---

### 🔄 PHASE 3C — HQ Sync Engine
- [ ] **3C.01** frmSyncConfig — Configure local ↔ HQ connection
- [ ] **3C.02** Sync service: push local transactions to HQ DB
- [ ] **3C.03** Sync service: pull master data updates from HQ
- [ ] **3C.04** Conflict resolution rules (last-write-wins / HQ-wins for masters)
- [ ] **3C.05** Sync scheduler (configurable interval: 15min / 30min / 1hr)
- [ ] **3C.06** Sync log viewer + retry failed records
- [ ] **3C.07** Offline mode indicator in WinForms shell
- [ ] **3C.08** HQ sync status dashboard (Sync.aspx)

---

### 📱 PHASE 4 — Android App & Android TV (Future)
- [ ] **4.01** GarmentsMIS.API — ASP.NET Web API 2 project setup
- [ ] **4.02** API authentication (JWT token)
- [ ] **4.03** API endpoints: Login, Dashboard data, Hourly entry
- [ ] **4.04** API endpoints: Reports (JSON format)
- [ ] **4.05** API endpoints: Bundle scan
- [ ] **4.06** API endpoints: Alerts
- [ ] **4.07** Android Mobile App — Data entry + reports (separate project)
- [ ] **4.08** Android TV App — Dashboard display read-only (separate project)

---

## 🚦 Alert Rules Reference

| Alert | Condition | Default Threshold |
|---|---|---|
| Low Efficiency | Line efficiency below X% for N consecutive hours | 60% / 2 hours |
| Target Miss | Hourly output below X% of target | 80% |
| High DHU | DHU exceeds X% | 2.5% |
| WIP Buildup | WIP qty exceeds X units at any dept | Configurable |
| Low Attendance | Attendance below X% of line strength | 85% |
| Bundle Stuck | Bundle not scanned for X hours | 4 hours |
| Delivery Risk | Order completion % behind schedule by X days | 2 days |

---

## 🔐 User Roles Reference

| Role | Access Level |
|---|---|
| Super Admin | Full access: all locations, all modules, user management |
| Factory Manager | All modules for assigned locations |
| Production Manager | Production entry + reports for assigned location |
| Line Supervisor | Entry for assigned lines + own line reports |
| Quality Inspector | Quality audit entry + quality reports |
| Data Entry Operator | Assigned entry forms only, no reports |
| Viewer | Reports and dashboards only, read-only |

---

## 📐 Key Business Logic Reference

### Efficiency Formula
```
Efficiency % = (Output × SMV) ÷ (Manpower × Working Minutes) × 100
```

### DHU Formula
```
DHU = (Total Defects ÷ Total Pieces Inspected) × 100
```

### Target Calculation (Priority Order)
```
1. System auto: (Manpower × Shift Minutes × 60) ÷ SMV × Efficiency%
2. SMV-based: (Working minutes × Manpower) ÷ SMV
3. Manual override by supervisor (with reason)
4. Fallback: Daily target ÷ number of working hours
```

### Over-Production Control
```
On save: if (cumulative output + new entry) > approved PO qty
  → Warn user with overage amount
  → Require supervisor login to override
  → Mandatory reason entry
  → Log to AuditLog
```

### Bundle Lifecycle
```
Created → Issued → In-Process → Completed → QC-Hold → Passed → Packed → Shipped
```

---

## 📅 Development Phase Timeline

| Phase | Scope | Estimated Sessions |
|---|---|---|
| **1A** | Database schema + stored procs + seed data | 3-4 sessions |
| **1B** | Solution scaffold + Core + DataAccess libraries | 2-3 sessions |
| **1C** | WinForms shell + login + security | 2 sessions |
| **1D** | All master setup forms (20 forms) | 4-5 sessions |
| **2A** | Data entry forms (11 forms) | 3-4 sessions |
| **2B** | 7 WinForms dashboards with charts | 4-5 sessions |
| **2C** | 40+ reports + export engine | 6-8 sessions |
| **2D** | Alert engine | 2 sessions |
| **3A** | Web dashboard (10 pages) | 3-4 sessions |
| **3B** | Multi-language + multi-currency | 2-3 sessions |
| **3C** | HQ sync engine | 3-4 sessions |
| **4** | Android API + apps | Future |

---

## 💡 Session Strategy (Token Efficiency)

> Each development session should focus on ONE phase item only.
> Start each session by stating: **"Continue GarmentsMIS — Phase X.XX"**
> Reference this README at the start of each new session.

### Recommended Session Order
```
Session 01 → Phase 1A: Tables Group 1-3 (System + Language + Factory)
Session 02 → Phase 1A: Tables Group 4-8 (Operators + Orders + Transactions + Bundles)
Session 03 → Phase 1A: Stored Procedures (Security + Masters)
Session 04 → Phase 1A: Stored Procedures (Production + Reports)
Session 05 → Phase 1A: Views + Seed Data + Test
Session 06 → Phase 1B: Solution scaffold + Core models + Helpers
Session 07 → Phase 1B: DataAccess layer + Repositories
Session 08 → Phase 1C: WinForms shell + Login + Security
Session 09 → Phase 1D: Master forms Part 1 (Company→Order)
Session 10 → Phase 1D: Master forms Part 2 (Style→Operator)
... and so on
```

---

## 📝 Notes & Decisions Log

| Date | Decision | Reason |
|---|---|---|
| Session 01 | SQL Server 2014 | Existing infrastructure |
| Session 01 | .NET Framework 4.8 + WinForms | Stability + compatibility |
| Session 01 | RDLC + ReportViewer | Free, lightweight, cross-device |
| Session 01 | MaterialSkin2 + LiveCharts2 | Free, MIT licensed, modern UI |
| Session 01 | Order/Style codes are strings | Buyer-defined alphanumeric codes |
| Session 01 | Multi-product-type per style | Same style can have Polo + Trouser |
| Session 01 | Buyer-specific size ranges | Each buyer has own size set |
| Session 01 | Fully flexible operator model | Multi-skill, multi-line, floating |
| Session 01 | Flexible dept flow | Per product type or per order override |
| Session 01 | Over-production: warn + override | Supervisor override with reason |
| Session 01 | Enterprise: 10+ locations | Local DB + HQ sync architecture |
| Session 01 | Future: Android app + TV | REST API layer planned from start |

---

*GarmentsMIS — Designed for enterprise garments manufacturing. Built session by session.*
*Last Updated: Session 01 — Blueprint & Planning Complete*
