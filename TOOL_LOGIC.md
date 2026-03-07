# DOMAIN CHECKING - TOOL LOGIC

## 1) Muc tieu
Tool dung de kiem tra domain va cac link tracking (`a=mswl`) tren landing page:
- Phat hien domain/link chua cap nhat domain moi.
- Phat hien link bi boc (link trung gian) nhung van dan toi tracking link.
- Bao cao theo nhom loi de QA/dev de theo doi.

## 2) Thanh phan chinh
- `ui_app.py`: server HTTP local/cloud, API `/run`, `/status`, `/notify`.
- `check_domains.py`: engine phan tich domain va link.
- `ui.html` / `index.html`: giao dien nhap domain, xem bang ket qua, copy summary, gui Google Chat.

## 3) Dau vao
Nguon dau vao:
- Paste text/domain vao UI (1 dong 1 domain, hoac text co domain xen ghi chu).
- Hoac file `LIST_DOMAIN.csv` khi chay CLI.

Xu ly domain input:
- Tach domain bang regex (khong can input phai "sach").
- Loai bo domain trung.

## 4) Luong kiem tra tung domain
1. Mo homepage:
- Thu `https://domain` truoc, neu khong duoc thi thu `http://domain`.
- Luu `page_status`, `page_url`, `page_final_url`.

2. Parse HTML:
- Dung `html.parser` de lay link.
- Lay thong tin context (text, class, id, parent...) de truy vet CTA/banner.

3. Tim tracking link truc tiep:
- Bat cac URL chua `a=mswl`.
- Khong chi `href`, ma con quet attr khac (`data-*`, `onclick`, ...).

4. Boc link trung gian (wrapper):
- Quet cac gia tri co the chua URL trong query (`url`, `redirect`, `next`, `target`, ...).
- Decode URL-encoded nhieu lop (`unquote`) de lay URL that.
- Probe endpoint trung gian:
  - request khong follow redirect de doc `Location`.
  - request co follow redirect de lay `final_url`.
  - quet tiep body tra ve de tim `a=mswl`.

## 5) Cach danh gia tracking link
Voi moi tracking link tim duoc:
- Kiem tra redirect chain o muc HTTP.
- So sanh domain nguon va domain dich.

Quy tac chinh:
- Redirect sang domain khac: tinh vao `tracking_error`.
- Redirect cung domain: tinh `tracking_ok`.
- Khong redirect: tinh `tracking_ok`.

Option `Ignore HTTP->HTTPS redirect`:
- Bat: case chi doi scheme `http -> https` (cung host/path/query) duoc xem la OK.
- Tat: case nay duoc xep nhom loi `Thieu https`.

## 6) Nhom loi va summary
Tool group loi theo 3 nhom:
1. `Loi truy cap domain`
2. `Chua doi domain link`
3. `Chua doi domain link (boc link)`
4. (Neu tat ignore) `Thieu https`

Noi dung dong loi da rut gon:
- Chi giu `domain` va cap redirect `(old -> new)` neu can.
- Khong lap lai text dai dong cho tung item.

## 7) Cot ket qua trong bang
Cot chinh:
- `domain`
- `page_url`
- `page_status`
- `page_final_url`
- `tracking_total`
- `tracking_ok`
- `tracking_error`
- `notes`

Chi tiet (de hover/click):
- Danh sach `tracking_ok`
- Danh sach `tracking_error`
- Moi item co `link`, `final_url`, `reason`, `context`, `error_type`.

## 8) UI/UX hanh vi
- Run theo job bat dong bo (`/run` -> `/status` poll).
- Co progress (`Processed X/Y`).
- Co copy summary loi.
- Co copy full table de paste Google Sheets.
- Hover/click vao so o cot tracking de xem danh sach link chi tiet.

## 9) Gui Google Chat
Thong qua webhook:
- UI gui len backend `/notify`.
- Backend relay payload den Google Chat webhook.

Noi dung gui:
- Card `QC Report (dd/mm/yyyy)`.
- Section `Summary`: Total/Pass/Fail.
- Section `Issues`: tach theo nhom loi (chi hien nhom co du lieu).

## 10) Gioi han va luu y
- Ket qua phu thuoc network/IP/VPN cua moi truong chay.
- Chay tren server datacenter co the gap 403 nhieu hon local.
- Co case JS redirect phuc tap van co the can browser automation de bat chinh xac hon.
- Heuristic wrapper da mo rong, nhung van co kha nang miss voi flow dac thu.

## 11) De xuat nang cap tiep
- Luu full redirect chain theo tung link khi can debug.
- Them mode Playwright cho domain kho.
- Them concurrency control/caching de toi uu toc do.
- Them che do "probe rong" cho link noi bo de giam miss wrapper pattern la.
