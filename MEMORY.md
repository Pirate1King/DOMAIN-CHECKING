# DOMAIN-CHECKING2 Memory

## Muc tieu hien tai

Tool nay da vuot qua muc "domain checker" co ban. Hien tai no co 4 module UI chinh:

1. `Tracking Scan`
2. `Page Audit`
3. `Geo Audit`
4. `Viewport Gallery`

Context su dung chinh:
- team marketing
- nhieu landing page
- can check tracking, redirect, responsive, screenshot, network environment

## Kien truc tong quan

Backend:
- `ui_app.py`: HTTP server, job runner, audit jobs, geo audit, viewport gallery
- `check_domains.py`: tracking engine, wrapped-link scan, subpage scan, redirect analysis

Frontend:
- `ui.html`
- `index.html`

Luu y:
- `ui.html` va `index.html` can duoc giu dong bo

## 3 mode scan tracking

Trong `Tracking Scan` co 3 mode logic:

1. `direct`
- quet tracking link truc tiep tren homepage

2. `wrapped`
- tim link boc / redirect trung gian roi moi ra tracking

3. `subpage`
- di vao trang con roi quet tracking trong subpage

Luu y:
- `subpage` se uu tien hon `wrapped` trong mot lan chay

## Cac thay doi da lam quan trong

### 1. UI / copy / visual
- UI da doi theo vibe hacker / terminal
- bo subtitle du thua
- chuan hoa ngon ngu:
  - control / diagnostic labels: English
  - status / summary / report: Vietnamese
- tooltip da rut gon theo style technical

### 2. Tracking popup details
- co note `mobile` khi chac chan
- neu la cap duplicate responsive nhung khong chac mobile thi note `responsive`
- neu `UI link` va `intermediate link` trung nhau thi chi hien 1 dong `wrapped link`

### 3. 403 handling
- `403` khong con vao summary/pass-fail
- van hien trong detail
- UI co note:
  - `403 co the bi anh huong boi IP/VPN, moi truong chay hoac co che chong bot/rate-limit.`

### 4. Wrapped recheck flow
- sau scan thuong, neu co row `no_tracking_links` thi UI co popup hoi co rerun bang wrapped mode khong
- neu user chon `Run`, chi rerun subset `no_tracking`
- row da rerun duoc highlight va gan badge `wrapped`

### 5. Page Audit
Da co browser-based audit:
- response/load time
- CTA visible / clickable / blocked
- broken images
- console errors
- request failures
- screenshot desktop/mobile
- heuristic:
  - `overlay_blocking`
  - `hero_media_missing`
  - `hero_media_broken`
  - `blank_risk`

### 6. Geo Audit
Da tach rieng thanh tab khac, khong nam trong `Page Audit`.

Geo Audit cho phep:
- chay browser qua network profile
- doi chieu:
  - observed IP
  - country
  - org
  - ASN

Cau hinh qua env:
- `NETWORK_PROFILES`

### 7. Viewport Gallery
Module nay dung cho 1 landing page moi lan.

Da co:
- 10 viewport pho bien
- show screenshot inline ngay trong page
- `Above the fold` / `Full page`
- `Download All`
- `One-screen review mode`
- `Mobile-first score`
- layout badges:
  - `layout clean`
  - `layout risk`
  - `overflow`
  - `offscreen N`
  - `tiny text N`

Da sua:
- preview screenshot khong crop sai nua
- `object-fit: contain`
- click vao anh mo file goc
- cho render ky hon truoc khi chup

## Van de wrapped mode da gap va da sua

Trieu chung:
- wrapped mode rat cham
- thuong chay duoc vai row dau roi trong nhu dung han

Nguyen nhan chinh:
- wrapped probe build qua nhieu candidates
- khong cat that theo `MAX_WRAPPER_PROBES`
- 1 domain loi co the lam batch dung giua chung

Ban va hien tai:
- commit `9e53d57`
- da cat that:
  - wrapped probes: `18`
  - subpage probes: `24`
- da harden `run_job()`:
  - 1 domain loi se ghi `job_error:...`
  - khong lam dung ca batch

## Viewport gallery fix gan nhat

Commit:
- `c9529f0`

Noi dung:
- improve viewport capture settle
- them `Mobile-first score`
- them `One-screen review mode`
- sua preview screenshot

## Render deploy

Can cau hinh:

Build Command:
```bash
./render-build.sh
```

Neu loi permission:
```bash
chmod +x render-build.sh && ./render-build.sh
```

Start Command:
```bash
python3 ui_app.py
```

Env bat buoc cho Playwright:
```text
PLAYWRIGHT_BROWSERS_PATH=0
```

Neu dung Geo Audit:
```text
NETWORK_PROFILES=[{"name":"vn-viettel","label":"VN / Viettel","country":"VN","carrier":"Viettel","asn":"AS7552","proxy_url":"http://user:pass@proxy.example.com:10001"}]
```

Luu y:
- `proxy_url` phai la proxy that
- khong duoc de `host:port` gia

## Cach chay local

```bash
cd /Users/user/Desktop/DOMAIN-CHECKING2
python3 ui_app.py
```

Mo:
```text
http://127.0.0.1:8000
```

## Cac commit quan trong gan day

- `9e53d57` `Limit wrapped probes and harden job runner`
- `c9529f0` `Improve viewport gallery capture and review mode`
- `5a97357` `Extend viewport gallery with modes and downloads`
- `b744464` `Add viewport gallery tab for landing pages`
- `57c4a21` `Improve geo audit matching and deployment docs`
- `cd96b1a` `Add separate geo audit module with network profiles`
- `a220228` `Add browser-based landing page audit module`
- `195a469` `Add wrapped recheck flow for no-tracking rows`
- `1ba3337` `Simplify scan UI and remove pause controls`
- `2db2da8` `Polish release UI and add pauseable scan jobs`

## Tinh trang repo can nho

Hien co thay doi local ngoai y muon:
- `TOOL_LOGIC.md` dang bi xoa trong working tree

No KHONG duoc dua vao cac commit gan day.

## Neu tiep tuc o turn sau

Neu user bao:
- wrapped mode van cham

Thi lam tiep:
1. log so wrapped candidates theo domain
2. log tong thoi gian probe theo domain
3. log domain nao bi timeout nhieu nhat
4. sieu chat heuristic candidate selection neu can

Neu user bao:
- viewport screenshot van sai thuc te

Thi kiem tra tiep:
1. lazy-load / anti-bot
2. font render delay
3. route `fold` vs `full`
4. can them settle delay hay hero detection khong

## Mo ta ngan gon san pham

Ten phu hop:
- `Landing Page QA and Tracking Validation System for Marketing Operations`

Khong nen mo ta no nhu:
- test platform cho production app truyen thong

Ban chat cua app:
- scanner + QA tool cho landing page marketing
- tracking, redirect, responsive, network profile, screenshot
