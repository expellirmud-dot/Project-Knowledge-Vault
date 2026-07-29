---
type: work_order
work_order_id: WO-OBSIDIAN-023
title: Project Registry Foundation
status: READY_FOR_CONTROLLER
project: Project-Knowledge-Vault
owner: Toto
created_at: 2026-07-29
base_branch: main
base_head: 1a80406d28fc508071602b66b90b058374c4cdbd
risk: medium
---

# WO-OBSIDIAN-023 — Project Registry Foundation

## 1. เป้าหมาย

เตรียม `Project-Knowledge-Vault` ให้เป็นทะเบียนกลางของทุกโปรเจกต์ โดยเชื่อมข้อมูลที่มนุษย์อ่านใน Obsidian กับ Repository Truth ที่ตรวจจาก Git ได้ และวางฐานสำหรับ Repository Scanner กับ SQLite Project Registry ในระยะถัดไป

งานนี้เป็นจุดเริ่มต้นของ Master Design ต่อไปนี้:

```text
Git และ Tests
→ Repository Scanner
→ Snapshot / Inventory
→ SQLite Project Registry
→ Obsidian Dashboard
```

Vault มีหน้าที่เป็น Project Knowledge Interface ไม่ใช่แหล่ง Runtime Truth เพียงแห่งเดียว

## 2. Repository Truth ณ ตอนออกใบงาน

```text
Repository: expellirmud-dot/Project-Knowledge-Vault
Base branch: main
Base HEAD: 1a80406d28fc508071602b66b90b058374c4cdbd
Latest closed work: WO-OBSIDIAN-022
```

พบ Control-document drift ที่ต้องตรวจใน Task แรก:

- `04 Work Orders/CURRENT_WORK_ORDER.md` ยังชี้ `WO-OBSIDIAN-011`
- `04 Work Orders/Work Order Index.md` ยังแสดงลำดับถึง `WO-OBSIDIAN-011`
- Git history มีการปิดงานถึง `WO-OBSIDIAN-022`

ห้ามตีความว่าเอกสาร pointer เก่าเป็นสถานะ Runtime ปัจจุบันโดยไม่ตรวจ Git history

## 3. หลักสถาปัตยกรรม

```text
Obsidian / Markdown
= ข้อมูลสำหรับเจ้าของโปรเจกต์และ Agent อ่าน

Git
= ความจริงของ Source, Branch, Commit และ Diff

Snapshot JSON
= หลักฐานรายละเอียดจากการ Scan

SQLite Project Registry
= ดัชนีและสถานะที่ระบบ Query อัตโนมัติ

Tests / Validators
= หลักฐานความสามารถที่ผ่านการตรวจ
```

AI Summary ต้องไม่ถูกยกระดับให้มีอำนาจเหนือ Git, Tests หรือ Owner Decision

## 4. ขอบเขต Goal

Goal นี้ประกอบด้วยงานย่อยตามลำดับ โดยทำทีละ Task และ Commit ทีละงาน

### Task 23.1 — Control Pointer Reconciliation

เป้าหมาย:

- ตรวจ Git history และ Work Order ปัจจุบัน
- ทำให้ `CURRENT_WORK_ORDER.md` และ `Work Order Index.md` สอดคล้องกับงานที่ปิดถึง WO-OBSIDIAN-022
- ห้ามแก้ข้อมูลสถานะโปรเจกต์อื่น

### Task 23.2 — Canonical Project Metadata

เป้าหมาย:

- สร้าง Project Note metadata specification
- สร้าง `TEMPLATE-Project.md`
- กำหนด enum มาตรฐานสำหรับ verification, snapshot และ work status
- Unknown value ต้องเป็นค่าว่างหรือ `not_verified`; ห้ามเดา

Required fields:

```text
type
project_id
project_name
system_role
repository_path
repository_remote
default_branch
verification_status
verified_head
verified_at
snapshot_status
current_goal
current_work_order
work_status
last_reviewed
```

### Task 23.3 — Apply Metadata Without Rewriting Knowledge

เป้าหมาย:

- เพิ่ม metadata ที่ขาดใน Project Notes เดิม
- รักษาเนื้อหา ลิงก์ และหลักฐานเดิมทั้งหมด
- ไม่เปลี่ยนสถานะ verified เว้นแต่มี Repository evidence

Project Notes เริ่มต้น:

```text
01 Projects/llm-agents.md
01 Projects/AI Worker Harness.md
01 Projects/STT Typing.md
01 Projects/Utility Disbursement App.md
01 Projects/Adobe Stock Upload Assistant.md
```

### Task 23.4 — Master Architecture Baseline

เป้าหมาย:

- เพิ่มเอกสารสถาปัตยกรรมรวมของ Controller, BAAR, Project Registry และ SQLite
- แยก Development Control Plane ออกจาก Runtime Execution Plane
- ระบุ Authority, Data Flow, State Machine, Failure Taxonomy และ Model Routing

### Task 23.5 — Read-only Repository Scanner

เป้าหมายรุ่นแรก:

```text
repository path
branch
HEAD
dirty state
tracked file count
top-level entries
test files
README / AGENTS / Roadmap
recent commits
```

ข้อห้าม:

- ห้ามแก้ Source Repository
- ห้าม cleanup, reset, stash หรือ commit
- ห้ามให้ AI ประกาศ `verified` เอง

### Task 23.6 — Managed Obsidian Sections

ใช้ขอบเขตอัตโนมัติ:

```markdown
<!-- AUTO:START -->
ข้อมูลจาก Scanner
<!-- AUTO:END -->
```

Scanner แก้ได้เฉพาะส่วน AUTO และต้องรักษา Human Notes

### Task 23.7 — Dashboard Upgrade

Dashboard ต้องแสดงอย่างน้อย:

```text
All Projects
Needs Verification
Active Work
Blocked Projects
Recently Reviewed
```

และมีข้อมูล:

```text
Project
System Role
Verified HEAD
Work Status
Verification Status
Snapshot Freshness
```

### Task 23.8 — SQLite Project Registry

เริ่มหลัง Scanner schema นิ่งแล้วเท่านั้น

ตารางขั้นต่ำ:

```text
projects
project_snapshots
components
capabilities
documents
test_suites
scan_runs
```

ไฟล์ฐานข้อมูลต้องอยู่นอก Git repository และห้ามสร้างสำเนาต่อ Worktree

## 5. ข้อห้ามตลอด Goal

- ห้ามสร้าง Vault ใหม่
- ห้ามย้ายหรือเปลี่ยนชื่อโฟลเดอร์เดิมโดยไม่มี bounded Work Order
- ห้ามลบหรือเขียนทับ Human Notes
- ห้ามใช้ Markdown เป็น Queue หรือ Transaction Store
- ห้ามใช้ SQLite แทน Git truth
- ห้ามเก็บ Source Code, Diff ขนาดใหญ่, stdout/stderr หรือ Secret ใน SQLite
- ห้ามเริ่ม Runtime Database ของ BAAR ใน Goal นี้
- ห้าม Onboard ทุกโปรเจกต์ใหม่พร้อมกัน
- ห้าม Push หรือ Merge โดยไม่มี Owner authorization

## 6. วิธีดำเนินงานต่อ Task

```text
Inspect repository truth
→ Select one bounded task
→ Declare exact allowed files
→ Implement smallest safe change
→ Validate links/schema/content
→ Review diff
→ Stage exact files
→ Commit exactly one task
→ Update Goal status
```

Controller ต้องไม่รายงานสถานะเดิมซ้ำเมื่อไม่มี evidence ใหม่

## 7. Acceptance Criteria ของ Goal

Goal ถือว่าพร้อมปิดเมื่อ:

1. Work Order pointers และ indexes สอดคล้องกับ Git history
2. Project Notes ใช้ metadata schema เดียวกัน
3. ข้อมูลที่ไม่ทราบไม่ถูกแต่งขึ้น
4. Repository Scanner เป็น read-only และมี tests
5. Snapshot ผูกกับ project ID และ commit hash
6. Scanner ไม่เขียนทับ Human Notes
7. Dashboard บอกได้ว่าแต่ละโปรเจกต์มีอะไรและข้อมูลสดหรือเก่า
8. SQLite Registry ถูกเพิ่มหลัง data contract นิ่งแล้ว
9. Git ยังคงเป็น Source Code truth
10. ทุก Task มี Diff, Validation และ Commit แยกกัน

## 8. Task แรกที่อนุญาต

```text
NEXT_TASK: 23.1 — Control Pointer Reconciliation
```

Allowed initial files:

```text
04 Work Orders/CURRENT_WORK_ORDER.md
04 Work Orders/Work Order Index.md
```

Validation ขั้นต่ำ:

- Pointer ไม่อ้างงานที่ปิดเก่าเป็น Active
- Index แสดง WO-OBSIDIAN-012 ถึง WO-OBSIDIAN-023 ตาม Git historyหรือระบุว่า index ยังไม่ครบอย่างตรงไปตรงมา
- ลิงก์ Work Order ที่อ้างถึงมีไฟล์จริง
- ไม่มี Project Note หรือ Architecture Note ถูกแก้ใน Task 23.1

## 9. สถานะ

```text
WORK_ORDER: WO-OBSIDIAN-023
STATUS: READY_FOR_CONTROLLER
BASE_HEAD: 1a80406d28fc508071602b66b90b058374c4cdbd
NEXT_TASK: 23.1
COMMIT_POLICY: ONE_TASK_ONE_COMMIT
PUSH_POLICY: OWNER_AUTHORIZATION_REQUIRED
```
