# Tải file dữ liệu level — `POST /gsort/level/download`

**File:** `httpHandler/level/DownloadLevelHandler.kt`
**Giao thức:** HTTP thuần (không qua WebSocket envelope `rid`/`cmd`)
**Xác thực:** Có — Bearer token, vai trò `GAME_CLIENT`
**Mô tả:** Tải về một hoặc nhiều file dữ liệu level, đóng gói chung thành một file `.zip`. Danh sách tên file được truyền lên trong body dưới dạng mảng JSON.

---

## Request

```http
POST /gsort/level/download HTTP/1.1
Authorization: Bearer <game_client_token>
Content-Type: application/json

["level1.1.json", "level63.2.json"]
```

### Request Body

Một mảng JSON các chuỗi (`List<String>`), mỗi phần tử là tên file level cần tải.

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|:--------:|-------|
| *(body)* | `String[]` | Có | Danh sách tên file cần tải, ví dụ `["level1.1.json", "level63.2.json"]` |

**Quy tắc tên file:** phải khớp regex `^level\d+\.\d+\.json$` (dạng `level<số level>.<số variant>.json`). Có thể lấy tên file thông qua file level config.

- File trùng tên trong mảng sẽ tự động được loại bỏ (dedupe) trước khi xử lý.
- Tất cả file phải tồn tại thật trong thư mục `LEVEL_DATA_DIRECTORY` (không nằm trong thư mục con) thì request mới thành công.

---

## Response

### Thành công — `200 OK`

```http
HTTP/1.1 200 OK
Content-Type: application/zip
Content-Disposition: attachment; filename="levels.zip"

<zip nhị phân, mỗi entry là một file level đã yêu cầu>
```

Mỗi file trong danh sách request (sau khi loại trùng) tương ứng với đúng một entry trong file zip trả về, giữ nguyên tên file gốc.

### Lỗi có thể xảy ra

| Status | errorCode | Tình huống |
|:------:|-----------|------------|
| `400 Bad Request` | `2` (`INVALID_REQUEST_DATA`) | Body rỗng, body không parse được thành mảng JSON string, mảng `files` rỗng sau khi loại trùng, hoặc có tên file không đúng định dạng `level<n>.<n>.json` (kể cả path traversal) |
| `400 Bad Request` | `3` (`NOT_FOUND`) | Một hoặc nhiều file trong danh sách không tồn tại trong `LEVEL_DATA_DIRECTORY` |
| `401 Unauthorized` | `UNAUTHORIZED` | Thiếu header `Authorization`, hoặc token không hợp lệ/hết hạn |
| `403 Forbidden` | `FORBIDDEN` | Token hợp lệ nhưng không thuộc vai trò `GAME_CLIENT` |
| `500 Internal Server Error` | `1` (`GENERAL_ERROR`) | Lỗi hệ thống không xác định khi đọc file / nén zip |

---

## Ghi chú

- **Lưu ý quan trọng:** file không tồn tại trả về HTTP `400`, **không phải** `404` — `NotFoundError` trong hệ thống này được `BaseHandler` map về status `400` (chỉ khác `errorCode = "3"` so với lỗi validate thông thường là `"2"`).
- Regex tên file vừa dùng để validate định dạng, vừa là cơ chế chống path traversal chính; server còn kiểm tra thêm `file.parentFile == baseDir` sau khi resolve canonical path để chắc chắn file nằm đúng trong `LEVEL_DATA_DIRECTORY`.
- Đây là API HTTP thuần, không đi qua giao thức WebSocket như các handler `cmd` khác.