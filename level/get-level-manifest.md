# Lấy manifest level — `GET /gsort/level/manifest`

**File:** `httpHandler/level/GetLevelManifestHandler.kt`
**Giao thức:** HTTP thuần (không qua WebSocket envelope `rid`/`cmd`)
**Xác thực:** Có — Bearer token, vai trò `GAME_CLIENT`
**Mô tả:** Trả về manifest rút gọn của toàn bộ level, dạng map tên file → mã MD5 nội dung (`contentMd5`). Dùng để client so sánh và biết file level nào cần tải lại (thay đổi so với bản đã cache).

---

## Request

```http
GET /gsort/level/manifest HTTP/1.1
Authorization: Bearer <game_client_token>
```

Không có query string, không có request body.

---

## Response

### Thành công — `200 OK`

```json
{
  "level1.1.json": "a49413a025a70040546e934a274a610b",
  "level63.2.json": "26f356d7297e6cefe2b5edf49cd1a509"
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| *(key)* | `String` | Tên file level, ví dụ `level1.1.json` |
| *(value)* | `String` | Mã MD5 nội dung file (`contentMd5`) lấy từ `manifest.json` |


### Lỗi có thể xảy ra

| Status | errorCode | Tình huống |
|:------:|-----------|------------|
| `400 Bad Request` | `3` (`NOT_FOUND`) | File `manifest.json` không tồn tại trong `LEVEL_DATA_DIRECTORY` |
| `401 Unauthorized` | `UNAUTHORIZED` | Thiếu header `Authorization`, hoặc token không hợp lệ/hết hạn |
| `403 Forbidden` | `FORBIDDEN` | Token hợp lệ nhưng không thuộc vai trò `GAME_CLIENT` |
| `500 Internal Server Error` | `1` (`GENERAL_ERROR`) | `manifest.json` tồn tại nhưng không phải JSON hợp lệ (parse lỗi) |

---

## Ghi chú
- Nếu entry nào trong `entries` thiếu trường `contentMd5`, entry đó sẽ bị bỏ qua âm thầm trong kết quả trả về (không gây lỗi).
- Nếu `entries` không tồn tại trong manifest gốc, API trả về object rỗng `{}` thay vì lỗi.
- Đây là API HTTP thuần, không đi qua giao thức WebSocket như các handler `cmd` khác.