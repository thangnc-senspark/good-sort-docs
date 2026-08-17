# Quy trình lấy & đồng bộ level cho client

**Mô tả:** Tài liệu này mô tả luồng client cần thực hiện để lấy và cập nhật dữ liệu level (`level*.json`), manifest (`manifest.json`) và file cấu hình level (`level config`) từ server, chia làm 2 giai đoạn: **lúc build/publish app** và **lúc app chạy** (mỗi lần mở app).

---

## Tổng quan 2 loại dữ liệu

| Loại dữ liệu | File | Nguồn "chuẩn" |
|---|---|---|
| Dữ liệu level | `level<level>.<variant>.json` (ví dụ `level1.1.json`) | Server (`LEVEL_DATA_DIRECTORY`) |
| Manifest | `manifest.json` — map tên file level → mã MD5 nội dung | Server, dùng để client biết level nào đã đổi |
| Cấu hình level (test) | `test.json` | Server (`LEVEL_CONFIG_DIRECTORY`), qua API `GET /gsort/level/config` |
| Cấu hình level (prod) | — | **Firebase Remote Config**, phục vụ A/B test — **không** lấy qua API này |


---

## Bước 1 — Trước khi publish app (build-time, thực hiện bởi dev)

Trước khi đóng gói bản build để publish, dev cần tải sẵn và nhúng vào app:

1. `manifest.json` — gọi [`GET /gsort/level/manifest`](get-level-manifest.md)
2. Toàn bộ file level hiện có — gọi [`POST /gsort/level/download`](download-level.md) với danh sách tên file lấy từ `manifest.json` vừa tải
3. File cấu hình level (`test.json`, hoặc bản tương ứng) — gọi [`GET /gsort/level/config`](get-level-config.md)

Ba file/thư mục này được đóng gói kèm theo app (bundled local), để lần đầu cài đặt người chơi có thể vào chơi ngay khi không có mạng hoặc trong khi đang tải bản cập nhật mới nhất ở Bước 2.

```
1. GET  /gsort/level/manifest        → lưu thành manifest.json (local, bundled)
2. POST /gsort/level/download        → giải nén, lưu toàn bộ level*.json (local, bundled)
   body: toàn bộ tên file có trong manifest.json vừa tải
3. GET  /gsort/level/config          → lưu thành level config (local, bundled)
```

---

## Bước 2 — Khi mở app (runtime, mỗi lần app khởi động)

### 2.1. Lấy manifest mới nhất từ server

Gọi [`GET /gsort/level/manifest`](get-level-manifest.md) để lấy manifest hiện tại của server:

```
GET /gsort/level/manifest
→ { "level1.1.json": "<md5>", "level63.2.json": "<md5>", ... }
```

### 2.2. So sánh với manifest local, tìm level cần tải

So sánh manifest vừa nhận với `manifest.json` đang lưu local (theo từng cặp `tên file` → `md5`):

| Trường hợp | Kết luận                                                            |
|---|---------------------------------------------------------------------|
| Tên file có trong manifest server nhưng **không có** trong manifest local | Level mới → cần tải                                                 |
| Tên file có ở cả 2 bên nhưng **md5 khác nhau** | Level đã thay đổi → cần tải lại                                     |
| Tên file có ở cả 2 bên và **md5 giống nhau** | Không đổi → bỏ qua, giữ bản local                                   |
| Tên file có trong manifest local nhưng **không còn** trong manifest server | Level đã bị gỡ bỏ → client tự quyết định xóa file local hay giữ lại |

Gom tất cả tên file "cần tải" (mới hoặc thay đổi) thành một danh sách.

### 2.3. Tải các level đã thay đổi

Nếu danh sách cần tải không rỗng, gọi [`POST /gsort/level/download`](download-level.md) với danh sách đó:

```
POST /gsort/level/download
body: ["level5.1.json", "level12.3.json", ...]   // chỉ những file mới/đổi ở bước 2.2
→ file .zip chứa các level*.json tương ứng
```

Giải nén và **ghi đè** các file level tương ứng vào local storage.

> Chỉ nên gửi những file thật sự cần (mới/đổi), không gửi lại toàn bộ danh sách mỗi lần — tiết kiệm băng thông.

### 2.4. Cập nhật manifest local

Sau khi tải và ghi đè xong toàn bộ level cần thiết, **ghi đè `manifest.json` local** bằng manifest mới nhận được ở bước 2.1 (kể cả các entry không đổi), để lần mở app kế tiếp so sánh đúng trạng thái mới nhất.


---

## Bước 3 — Lấy cấu hình level (test)

Gọi [`GET /gsort/level/config`](get-level-config.md) để lấy cấu hình level hiện tại từ server


So sánh với bản level config đang lưu local. Nếu khác, ghi đè bản local bằng nội dung mới nhận.

> **Lưu ý:** API này chỉ dùng cho môi trường **test**. Ở bản build **prod**, cấu hình level chạy A/B test được lấy từ **Firebase Remote Config**, không qua API này — bước 3 chỉ áp dụng cho client đang trỏ vào server test/local.

---

## Tóm tắt luồng đầy đủ

```
[Build-time — dev]
  GET  /gsort/level/manifest  ──▶ manifest.json (bundled)
  POST /gsort/level/download  ──▶ toàn bộ level*.json (bundled)
  GET  /gsort/level/config    ──▶ level config (bundled)

[Runtime — mỗi lần mở app]
  GET  /gsort/level/manifest  ──▶ manifest mới nhất
       │
       ▼
  So sánh với manifest.json local (theo md5 từng file)
       │
       ├─ Không có gì đổi ──▶ bỏ qua, dùng level local hiện có
       │
       └─ Có level mới/đổi ──▶ POST /gsort/level/download (chỉ file đổi)
                                ──▶ ghi đè level*.json local
                                ──▶ ghi đè manifest.json local

  GET  /gsort/level/config    ──▶ so sánh, ghi đè level config local nếu khác
                                   (chỉ áp dụng server test; prod dùng Firebase)
```