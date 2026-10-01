# codex-feature-suggest-skill

Skill สำหรับให้ Codex อ่าน codebase จริงแล้วเสนอว่าควรเพิ่ม feature อะไรต่อ

ใช้ `$suggest-feature` ตอนคิดไม่ออกว่าจะทำอะไรต่อ แล้ว Codex จะสำรวจโปรเจกต์จริง วิเคราะห์สิ่งที่มีอยู่ และสรุปข้อเสนอเป็นไฟล์ใหม่ใน `note/suggest-feature/`

ใช้คู่กับ dev log skill ได้ดี:

- `$update-log` → บันทึกว่าทำอะไรไปแล้ว
- `$suggest-feature` → วิเคราะห์ว่าควรทำอะไรต่อ

## ติดตั้ง

สร้าง Skill สำหรับ Codex ที่:

```text
.codex/skills/suggest-feature/SKILL.md
```

หรือหากต้องการติดตั้งแบบ global เพื่อใช้กับหลายโปรเจกต์ ให้ติดตั้งไว้ใน Codex skills directory ของผู้ใช้แทน

จากนั้นใส่เนื้อหาต่อไปนี้ใน `SKILL.md`

```md
---
name: suggest-feature
description: วิเคราะห์ codebase จริงแล้วเสนอ feature ที่ควรเพิ่ม โดยบันทึกผลเป็นไฟล์ใหม่ใน note/suggest-feature/
---

วิเคราะห์โปรเจกต์นี้แล้วเสนอ feature ที่ควรเพิ่ม

ถ้าผู้ใช้ระบุ scope เช่น:

$suggest-feature seo
$suggest-feature performance
$suggest-feature auth
$suggest-feature ux

ให้โฟกัสเฉพาะด้านนั้น

ถ้าไม่มี scope ให้มองทั้งโปรเจกต์

## ขั้นตอน

### 1. ตรวจสอบวันที่

รัน:

date +%Y-%m-%d

ใช้วันที่จริงจาก command เท่านั้น ห้ามเดาวันที่เอง

### 2. สำรวจ codebase จริงก่อนเสนอ feature

ห้ามเดาจากชื่อโปรเจกต์หรือ README อย่างเดียว

ตรวจสอบอย่างน้อย:

- `package.json`
- `README.md`
- `.env.example` ถ้ามี
- routes / pages
- components
- API / server routes
- config ที่เกี่ยวข้อง

ใช้การค้นหาไฟล์และ source code เพื่อทำความเข้าใจว่า feature อะไรมีอยู่แล้ว

ตรวจสอบ dependencies ใน `package.json` เพื่อดู:

- stack ที่ใช้อยู่
- library ที่เกี่ยวข้อง
- dependency ที่ติดตั้งไว้แต่ดูเหมือนยังไม่ได้ใช้
- capability ที่ project รองรับอยู่แล้ว

ค้นหา:

- `TODO`
- `FIXME`
- `@ts-ignore`
- การใช้ `any`

สิ่งเหล่านี้เป็นสัญญาณของ technical debt หรืองานที่อาจยังไม่เสร็จ

### 3. ตรวจสอบ Git history

รัน:

git log --oneline -30

ใช้ commit ล่าสุดเพื่อดูว่าช่วงนี้ project กำลังพัฒนาเรื่องอะไร

อย่าเสนอ feature จาก git history เพียงอย่างเดียว ต้องตรวจสอบ source code ปัจจุบันด้วย

### 4. อ่าน Dev Log ถ้ามี

ถ้ามี directory:

log/

ให้อ่านเฉพาะ 2-3 ไฟล์ล่าสุด

ใช้เป็นบริบทว่า:

- เพิ่งทำอะไรไป
- มีอะไรตั้งใจยังไม่ทำ
- มีอะไรติดปัญหา
- มีอะไรที่กำลังพัฒนาต่อ

ไม่ต้องอ่าน log ทั้งหมด

### 5. ตรวจสอบ Feature Suggestions เก่า

ถ้ามี:

note/suggest-feature/

ให้ list ไฟล์ก่อน

จากนั้นอ่านเฉพาะ 2 ไฟล์ล่าสุด

ห้ามอ่านทั้ง directory เพราะเปลือง context และข้อมูลเก่าอาจไม่ตรงกับ codebase ปัจจุบันแล้ว

ใช้ข้อมูลนี้เพื่อหลีกเลี่ยงการเสนอของซ้ำ

ถ้าข้อเสนอเก่า implement ไปแล้ว ให้ตรวจสอบจาก source code และห้ามเสนอซ้ำ

### 6. ตรวจสอบปัญหาที่ควรแก้ก่อน

ถ้าพบปัญหาที่สำคัญกว่าการเพิ่ม feature เช่น:

- security issue
- architecture issue
- broken flow
- critical bug
- duplicated logic รุนแรง
- configuration ที่ผิด
- dependency หรือ implementation ที่เสี่ยง

ให้สร้างหัวข้อ:

## ควรแก้ก่อนเพิ่ม Feature

ไว้บนสุดของรายงาน

อธิบายสั้นๆ พร้อมอ้างอิงไฟล์จริง

### 7. เสนอ Feature

เสนอประมาณ 5-8 ข้อ

แบ่งเป็น 3 กลุ่ม:

## Quick Win

งานขนาดเล็ก โดยประมาณไม่เกินครึ่งวัน

## Impact สูง

Feature ที่มีผลต่อ UX, business value, product capability, reliability หรือ developer workflow อย่างมีนัยสำคัญ

## Nice to Have

สิ่งที่มีประโยชน์แต่ยังไม่จำเป็นต้องทำทันที

แต่ละ feature ต้องมี:

- Feature คืออะไร
- ทำไม project นี้ควรมี
- Evidence จาก codebase
- ไฟล์หรือ directory ที่น่าจะต้องแก้
- Effort โดยประมาณ: `S` / `M` / `L`

ตัวอย่าง:

### Saved Search

- เพิ่มความสามารถให้ผู้ใช้บันทึก search/filter ที่ใช้บ่อย
- เหตุผล: พบ filter state อยู่ใน `src/components/search/Filter.tsx` แต่ยังไม่มี persistence หรือ saved configuration
- ไฟล์ที่เกี่ยวข้อง:
  - `src/components/search/`
  - `src/app/api/search/`
  - database schema
- Effort: M

## กติกาการเสนอ

ห้ามเสนอ feature ที่มีอยู่แล้วใน codebase

ถ้าไม่แน่ใจว่ามีหรือไม่ ให้ค้นหา source code ก่อน

ห้ามเสนอ generic recommendation เช่น:

- เพิ่ม dark mode
- เพิ่ม test
- เพิ่ม caching
- เพิ่ม SEO
- เพิ่ม authentication
- เพิ่ม loading state

เว้นแต่สามารถอธิบายจาก codebase จริงได้ว่า project นี้มี gap ตรงไหน และ feature นั้นแก้ปัญหาอะไร

ทุกข้อควรมี evidence จากไฟล์จริง

ข้อเสนอเป็น recommendation ไม่ใช่ roadmap

อย่าแก้ source code ใดๆ

Skill นี้มีสิทธิ์สร้างเฉพาะไฟล์ใหม่ภายใน:

note/suggest-feature/

## การสร้างไฟล์

การรันหนึ่งครั้งต้องสร้างไฟล์ใหม่หนึ่งไฟล์เสมอ

ห้าม append

ห้าม overwrite ไฟล์เก่า

### Step 1

สร้าง directory ถ้ายังไม่มี:

note/suggest-feature/

### Step 2

ตรวจสอบไฟล์ที่มีอยู่ใน:

note/suggest-feature/

### Step 3

ใช้วันที่จากขั้นตอนแรกในรูปแบบ:

YYYY-MM-DD

หาเลขลำดับของวันนี้

ตัวอย่าง:

ถ้ายังไม่มี:

2026-08-13-idea-1.md

ให้สร้าง:

2026-08-13-idea-1.md

ถ้ามี:

2026-08-13-idea-1.md
2026-08-13-idea-2.md

ให้สร้าง:

2026-08-13-idea-3.md

เลขเริ่มใหม่จาก 1 ทุกวัน

ถ้าชื่อไฟล์เป้าหมายมีอยู่แล้ว ห้าม overwrite

ให้เพิ่มเลขไปเรื่อยๆ จนพบชื่อที่ยังไม่มี

### Step 4

หัวไฟล์ใช้:

# Feature Ideas YYYY-MM-DD #N

ถ้ามี scope ให้เพิ่ม:

# Feature Ideas YYYY-MM-DD #N — <scope>

ตัวอย่าง:

# Feature Ideas 2026-08-13 #2 — seo

## รูปแบบผลลัพธ์

ใช้ภาษาไทย

เขียนกระชับ

เน้น bullet

ทุก feature ต้องอ้างอิง evidence จาก codebase

โครงสร้างโดยประมาณ:

# Feature Ideas YYYY-MM-DD #N

## ควรแก้ก่อนเพิ่ม Feature

ถ้ามี

## Quick Win

### Feature Name

- Feature:
- เหตุผล:
- Evidence:
- ไฟล์ที่เกี่ยวข้อง:
- Effort: S

## Impact สูง

...

## Nice to Have

...

## สรุป

- Feature A
- Feature B
- Feature C

## ข้อจำกัด

ห้ามแก้ code

ห้ามแก้ config

ห้าม install package

ห้าม refactor

ห้าม commit

ห้าม overwrite หรือ append ไฟล์ suggestion เดิม

การเปลี่ยนแปลงเดียวที่อนุญาตคือการสร้าง directory `note/suggest-feature/` ถ้ายังไม่มี และสร้างไฟล์ suggestion ใหม่ภายใน directory นี้

เมื่อสร้างไฟล์เสร็จ:

1. สรุปชื่อ feature ที่เสนอแบบสั้นๆ ในแชท
2. แจ้ง path ของไฟล์ที่สร้าง
```

## วิธีใช้

เรียก Skill จาก Codex:

```text
$suggest-feature
```

เพื่อวิเคราะห์ทั้ง project

หรือระบุ scope:

```text
$suggest-feature seo
```

```text
$suggest-feature performance
```

```text
$suggest-feature auth
```

```text
$suggest-feature ux
```

Codex จะใช้ scope นั้นเป็นแนวทางในการสำรวจ codebase และเสนอ feature

## มันทำอะไร

1. อ่าน codebase จริง เช่น `package.json`, README, routes, components และ API
2. ค้นหา `TODO`, `FIXME`, `@ts-ignore`, `any` และ technical debt ที่เกี่ยวข้อง
3. อ่าน `git log --oneline -30` เพื่อเข้าใจทิศทางการพัฒนาล่าสุด
4. อ่าน dev log ล่าสุด 2-3 ไฟล์ ถ้ามี
5. อ่าน feature suggestion ล่าสุดเพียง 2 ไฟล์ เพื่อป้องกันการเสนอซ้ำ
6. ตรวจสอบ source code ว่าข้อเสนอเก่าถูก implement ไปแล้วหรือยัง
7. เสนอ 5-8 feature แยกเป็น **Quick Win / Impact สูง / Nice to Have**
8. ระบุ evidence, ไฟล์ที่เกี่ยวข้อง และ effort (`S / M / L`)
9. สร้างรายงานใหม่ใน `note/suggest-feature/`

## Convention

```text
note/
└── suggest-feature/
    ├── 2026-08-12-idea-1.md
    ├── 2026-08-13-idea-1.md
    ├── 2026-08-13-idea-2.md
    └── 2026-08-13-idea-3.md
```

หนึ่งไฟล์ = หนึ่งรอบการวิเคราะห์

ไม่ append ต่อไฟล์เดิม เพราะ:

- **ไฟล์ไม่บวม** — วิเคราะห์กี่ครั้งก็แยกเป็นแต่ละ snapshot
- **ใช้ context น้อยกว่า** — Skill อ่านข้อเสนอเก่าเพียง 2 ไฟล์ล่าสุด
- **ลบทิ้งง่าย** — รอบไหนข้อเสนอไม่ดีสามารถลบไฟล์นั้นได้
- **เทียบข้ามรอบได้** — สามารถ diff ข้อเสนอก่อนและหลัง refactor ได้
- **เก็บ history ได้ชัดเจน** — เห็นว่าความต้องการของ project เปลี่ยนไปอย่างไร

ใช้ `YYYY-MM-DD` ตาม ISO 8601 เพื่อให้ไฟล์เรียงตามวันที่ได้อัตโนมัติ

Scope ไม่ใส่ใน filename แต่เก็บไว้ใน heading:

```text
# Feature Ideas 2026-08-13 #2 — seo
```

ทำให้ logic การหาเลข `idea-N` ยังคงเรียบง่าย

## ข้อจำกัด

Skill นี้ต้องอ่าน codebase หลายส่วน จึงสามารถใช้ context ค่อนข้างมาก

สำหรับ codebase ใหญ่ แนะนำให้ระบุ scope:

```text
$suggest-feature auth
```

แทนการวิเคราะห์ทั้งระบบ เพื่อให้ Codex เจาะลึกเฉพาะส่วนและลด context ที่ไม่จำเป็น

ข้อเสนอเป็นเพียง input สำหรับการตัดสินใจ ไม่ใช่ roadmap ที่ต้อง implement ทั้งหมด

ไฟล์ suggestion จะสะสมตามจำนวนครั้งที่รัน สามารถลบไฟล์เก่าที่ไม่ต้องการได้

## ปรับแต่ง

แก้:

```text
.codex/skills/suggest-feature/SKILL.md
```

สามารถปรับ:

- จำนวน feature
- ภาษา
- กลุ่มการจัด priority
- วิธีประเมิน effort
- criteria ด้าน business value
- path ของ output
- จำนวน dev log ที่อ่าน
- จำนวน suggestion เก่าที่ใช้เป็น context

ตัวอย่างเช่นสามารถเปลี่ยนจาก:

```text
Quick Win / Impact สูง / Nice to Have
```

เป็น:

```text
Business Value / UX / Technical / Growth
```

ได้โดยไม่ต้องเปลี่ยน workflow หลัก

## Built by

BSynth

## License

MIT
