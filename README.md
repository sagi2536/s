# s — ระบบย่อลิงก์ (URL Shortener)

ระบบย่อ URL ที่ host หน้าเว็บบน **GitHub Pages** และใช้ **Google Sheet เป็นฐานข้อมูล** ผ่าน Google Apps Script เป็น API

- ลิงก์ย่อ: `https://sagi2536.github.io/s/<รหัส>` เช่น `https://sagi2536.github.io/s/swu`
- หน้าจัดการ: `https://sagi2536.github.io/s/`

## โครงสร้าง

| ไฟล์ | หน้าที่ |
|------|---------|
| `index.html` | หน้าจัดการลิงก์ (เพิ่ม / ลบ / ดูจำนวนคลิก) |
| `404.html` | ตัว redirect — รับรหัสจาก path แล้วพาไป URL ปลายทาง |
| `Code.gs` | โค้ด Google Apps Script (API + จัดการชีต) — อยู่ในโปรเจกต์ Apps Script ที่ผูกกับชีต ไม่ได้อยู่ใน repo นี้ |

## สถาปัตยกรรม

```
ผู้ใช้เปิด /s/abc
      │
      ▼
GitHub Pages 404.html  ──fetch(?c=abc)──►  GAS Web App  ──►  Google Sheet
      │                                     (คืน url + นับคลิก)
      ▼
redirect ไป URL ปลายทาง
```

หน้า `index.html` ก็คุยกับ GAS API ตัวเดียวกัน — เพิ่ม/ลบลิงก์แล้วบันทึกลงชีตทันที

## วิธีใช้งาน

**เพิ่มลิงก์** ทำได้ 2 ทาง
1. เปิดหน้าจัดการ `https://sagi2536.github.io/s/` → กรอก URL + รหัส → กด "เพิ่มลิงก์" (เซฟลงชีตทันที)
2. เปิด Google Sheet แล้วพิมพ์แถวใหม่ในชีต `links` เองตรง ๆ

**โครงสร้างชีต** `links`

| code | url | note | clicks | created |
|------|-----|------|--------|---------|
| swu | https://www.swu.ac.th | เว็บหลัก | 12 | 2026-07-02 10:30 |

## การตั้งค่า (ครั้งเดียว)

1. สร้าง Google Sheet → `ส่วนขยาย > Apps Script` → วาง `Code.gs`
2. `Deploy > New deployment > Web app` — Execute as: **Me**, Who has access: **Anyone**
3. คัดลอก URL `/exec` มาวางในตัวแปร `API` ทั้งใน `index.html` และ `404.html`
4. push ขึ้น repo นี้ แล้วเปิด GitHub Pages (`Settings > Pages` → branch `main` / root)

> แก้โค้ด `Code.gs` ภายหลัง ต้อง `Manage deployments > Edit > New version` ทุกครั้ง ลิงก์เดิมถึงจะอัปเดต

## การอัปเดตหน้าเว็บ

```bash
git add -A
git commit -m "อธิบายว่าแก้อะไร"
git push
```

GitHub Pages อัปเดตเองใน 1–2 นาที

## หมายเหตุ

- redirect วิ่งผ่าน GAS จึงมีดีเลย์เล็กน้อย (~0.5 วินาที) แลกกับการนับคลิกอัตโนมัติ
- อยากได้ลิงก์สั้นกว่านี้ ผูก custom domain ได้ (เพิ่มไฟล์ `CNAME` + ตั้ง DNS)
