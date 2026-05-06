# DVD Project Manager

Phần mềm quản lý dự án xây dựng cho Chủ đầu tư và Tổng thầu, dạng Excel Add-in.

**Status**: Phase 1 — Sprint 1 (Foundation) ✅

## Cấu trúc giải pháp

```
src/
├── DVDProjectMgr.Core/          Domain entities, interfaces (no external deps)
├── DVDProjectMgr.Data/          EF Core 8 + SQLite, repositories, audit log
├── DVDProjectMgr.Services/      Application logic, use cases
├── DVDProjectMgr.UI/            WPF UserControls + ViewModels (MVVM)
└── DVDProjectMgr.ExcelAddin/    Excel-DNA entry, ribbon, hosting
tests/
└── DVDProjectMgr.Core.Tests/    xUnit + FluentAssertions
```

## Yêu cầu môi trường

* .NET 8 SDK (≥ 8.0.400)
* Visual Studio 2022 17.8+ (Pro hoặc Community) hoặc JetBrains Rider 2024.x
* Microsoft Excel 2016+ (64-bit) — để debug add-in
* Windows 10/11

## Build và chạy lần đầu

```bash
git clone <repo-url>
cd DVDProjectMgr
dotnet restore
dotnet build
dotnet test
```

Nếu test pass → foundation OK.

### Chạy thử trong Excel

1. Build solution ở config Debug.
2. Trong Visual Studio, set `DVDProjectMgr.ExcelAddin` làm startup project.
3. Trong Properties → Debug → "Start external program": `C:\\Program Files\\Microsoft Office\\root\\Office16\\EXCEL.EXE`
4. Command-line arguments: `/x "$(TargetDir)DVDProjectMgr.ExcelAddin-AddIn64.xll"`
5. F5 → Excel mở, ribbon "DVD - Chủ đầu tư" xuất hiện.
6. Click button "Chuyển sang Tổng thầu" → ribbon đổi tab.
7. Database file tạo tại `%LocalAppData%\\DVDProjectMgr\\dvdpm.db`.
8. Log file tại `%LocalAppData%\\DVDProjectMgr\\logs\\dvdpm-YYYYMMDD.log`.

## Kiểm tra DB sau khi chạy lần đầu

Mở `dvdpm.db` bằng DB Browser for SQLite. Phải thấy:

* 15 bảng (14 nghiệp vụ + 1 audit\_log)
* Bảng `users` có 1 record `admin`
* Bảng `audit\_logs` rỗng

## Sprint 1 acceptance checklist

git add .

git commit -m "Sprint 1: Foundation complete - Add-in loads, ribbon switches mode, DB initialized"

* \[x] Solution structure đúng layered architecture
* \[x] Core không reference external libraries
* \[x] EF Core 8 + SQLite hoạt động
* \[x] AppDbContext tự động ghi audit log + soft delete
* \[x] Excel Add-in load được, ribbon hiển thị
* \[x] Switch mode CĐT/TT thay đổi ribbon
* \[x] DI container hoạt động
* \[x] Serilog log ra file
* \[x] xUnit test pass

## Sprint 2 — Tiến độ

* \[x] Tuần 5 — Project CRUD UI (form WPF + Custom Task Pane)
* \[x] Tuần 6 — Party (Database nhà thầu) + validator MST VN
* \[x] Tuần 7 — Contract CRUD + Guarantee sub-form + sheet renderer
* \[x] Tuần 8 — BoQ Engine: template generator + import từ Excel + tree renderer

## Sprint 3 — Tiến độ

* \[x] Tuần 9 — Payment Tracking: milestone CRUD + payment items theo BoQ + summary + sheet renderer
* \[x] Tuần 10 — Schedule + S-curve: WBS tree + Gantt + S-curve weekly/monthly + chart
* \[x] Tuần 11 — Document Control: RFI/Submittal/ShopDrawing + aging + overdue alert + sheet renderer
* \[x] Tuần 12 — Dashboard: KPI cards + alerts + S-curve chart trên 1 sheet

## Sprint 4 — Tiến độ (Module riêng)

CĐT:
* \[x] Tuần 13 — VO Control: full CRUD + cảnh báo vượt dự phòng phí + sheet renderer
* \[x] Tuần 14 — Approval Center: workflow phê duyệt VO/Payment/Document + audit history
* \[x] Tuần 15 — Budget Control: tracking budget vs HĐ vs VO duyệt vs đã TT + theo loại đối tác
* \[x] Tuần 16 — Pháp lý: GPXD/PCCC/ĐTM CRUD + cảnh báo hết hạn ≤30d

Tổng thầu:
* \[x] Tuần 13 — Subcontractor Overview: aggregate HĐ outgoing + tạm ứng + giữ lại + KL nghiệm thu
* \[x] Tuần 14 — Bảo lãnh hai chiều: BL nhận + BL phát hành + 2-tab dialog + sheet renderer
* \[x] Tuần 15 — Internal Cost Tracking: P&L estimate (incoming - outgoing - overhead) + cashflow ròng
* \[x] Tuần 16 — Nhật ký thi công: form daily log (thời tiết, nhân công, ATLĐ) + tracking sự cố

## Sprint 5 — Tiến độ (Reports + Licensing + Installer)

* \[x] Tuần 17 — PDF Export: QuestPDF Community + Project Status Report (header xanh, KPI cards, cost/schedule/docs tables, alerts color-by-severity)
* \[x] Tuần 18 — PPTX Export: OpenXML SDK + Executive Deck 6 slides (Cover, KPI Overview, Cost, Schedule, Docs, Alerts)
* \[x] Tuần 19 — Licensing: file-based offline với HMAC sign + 30-day trial tự bắt đầu khi run lần đầu + activation dialog. Cryptlex roadmap cho Phase 2 (xem `build/INSTALLER.md`)
* \[x] Tuần 20 — Polish: Audit log viewer (filter date/entity/action), About dialog (version + license + system info), Velopack installer build script

## Sprint 6 — Tiến độ (Pilot test + Ship v1.0)

* \[x] Sample data seeder: 1-click sinh dự án demo full data (6 đối tác, 5 HĐ, 12 BoQ, payments, 14 task lịch, 9 docs, 4 VOs, 4 permits, 14 daily logs)
* \[x] Feedback dialog: thu thập bug/feature/question từ pilot, lưu file `.json` local
* \[x] Diagnostic bundle exporter: zip logs + system info + license status (sanitized) cho support
* \[x] What's New dialog + version tracker: tự popup khi user upgrade
* \[x] License key generator console app: `tools/DvdpmKeygen` để admin sinh + verify keys
* \[x] Documentation: USER-GUIDE, QUICK-START, PILOT-PROGRAM, RELEASE-NOTES-v1.0, TROUBLESHOOTING, TRAINING-CHECKLIST (xem `docs/`)

### Tools

```bash
# Sinh license key cho pilot
cd tools/DvdpmKeygen
dotnet run -- generate --edition BUNDLE --expiry 2027-12-31 --name PILOT01

# Verify key
dotnet run -- verify DVDPM-BUNDLE-20271231-PILOT01-AC15CB77
```

## Build installer

```powershell
# Cài Velopack tool (1 lần)
dotnet tool install -g Velopack

# Build installer cho version v1.0.0
.\build\build-installer.ps1 -Version 1.0.0
```

Output: `build\Releases\DVDProjectMgr-1.0.0-Setup.exe`. Xem `build\INSTALLER.md` để biết:
- Cách test trên máy sạch
- Code-sign cho production
- Cryptlex integration roadmap (Phase 2)

## Test status

Build: 6 projects compile thành công qua MSBuild VS 2022. **Tests: 38/38 pass** — không còn pre-existing failure.

## Tuần 7 — Contract & Guarantee

UI và service:

* `ContractManagementDialog` — danh sách + form CRUD + Guarantee sub-form
* `GuaranteeEditDialog` — modal sub-dialog Add/Edit bảo lãnh
* `IContractService.UpdateAsync/DeleteAsync` — full CRUD, validation cấu trúc HĐ
* `IContractService.GetListByProjectAsync` — view-model phẳng có party + guarantee count
* `IGuaranteeService.UpdateAsync/GetExpiringAsync` — support edit và alert sắp hết hạn
* `ContractSheetRenderer` — render danh sách HĐ ra sheet "Hợp đồng", group theo direction (Incoming/Outgoing)
* Ribbon: nút "Danh sách"/"Bảo lãnh" (Owner) + "HĐ chính"/"Thầu phụ"/"Bảo lãnh" (TT) — đều mở cùng dialog

## Tuần 8 — BoQ Engine

UI và service:

* `BoqImportDialog` — chọn HĐ → tải/chọn file Excel → preview → import
* `BoqTemplateGenerator` — sinh file `BoQ_Template.xlsx` chuẩn (header, validation, sample rows)
* `BoqImportParser` — parse file Excel với ClosedXML, trả `BoqParseResult` có warnings
* `IBoqService.ImportAsync` — validate, dedupe, dựng hierarchy theo dotted code (1.1.2 → parent 1.1)
* `IBoqService.GetTreeByContractAsync` — build cây có depth + subtotal rollup
* `BoqSheetRenderer` — render cây ra sheet "BoQ - {ContractNo}" với indent + formula
* Ribbon: nút "Import BoQ" + "Render" trên cả hai mode

## Tuần 9 — Payment Tracking

* `PaymentManagementDialog` — danh sách milestone + form CRUD + summary card (advance, retention, paid, remaining)
* `PaymentItemsDialog` — sub-dialog gắn BoQ items vào milestone (auto compute cumulative qty từ các đợt trước)
* `IPaymentService` — CRUD milestone + `SetItemsAsync` (ghi đè items, validate KL không vượt BoQ) + `GetContractSummaryAsync` (tổng KH/Thực, retention, paid count)
* `PaymentSheetRenderer` — sheet "TT - {ContractNo}" gồm contract info + bảng đợt thanh toán color-by-status + tóm tắt

## Tuần 10 — Schedule + S-curve

* `ScheduleManagementDialog` — DataGrid task tree + form WBS code + slider % thực tế + status color-coded
* `IScheduleService.GetTreeByProjectAsync` — build tree, tự suy parent từ dotted code, status (Delayed/AtRisk/Completed)
* `IScheduleService.GetSCurveAsync(period)` — cộng dồn weighted % theo tuần/tháng, planned vs actual
* `ScheduleSheetRenderer` — 2 sheet: "Gantt" (timeline tuần với bar màu kế hoạch / thực tế) + "S-Curve" (bảng + chart line)

## Tuần 11 — Document Control

* `DocumentManagementDialog` — RFI/Submittal/ShopDrawing trên cùng 1 dialog, có pre-filter type từ ribbon command
* Stats panel: tổng / chờ / approved / rejected / quá hạn / bị giam >14d
* `IDocumentService.GetListByProjectAsync` — view-model có aging tự tính + cờ overdue
* `DocumentSheetRenderer` — sheet "Hồ sơ" highlight quá hạn + bị giam, có row coloring theo status
* Ribbon: nút "RFI" / "Đệ trình" / "Shop dwg" mở cùng dialog với type filter pre-selected

## Tuần 12 — Dashboard

* `IDashboardService.GetDashboardDataAsync` — aggregate cost/schedule/docs + tự sinh alerts cho cost overrun, SPI thấp, doc quá hạn, BL/giấy phép sắp hết hạn, deadline gần
* `DashboardSheetRenderer` — sheet "Dashboard": title bar xanh, 3 section KPI cards (Tài chính, Tiến độ, Hồ sơ), bảng cảnh báo color-by-severity, S-curve chart theo tháng
* Ribbon: nút "Dashboard" trên cả 2 mode

## Documentation

* `docs/architecture.md` — Tổng quan kiến trúc
* `docs/decisions/` — Architecture Decision Records (ADR)
