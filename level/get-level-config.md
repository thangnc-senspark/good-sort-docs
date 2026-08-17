# Lấy cấu hình level — `GET /gsort/level/config`

**File:** `httpHandler/level/GetLevelConfigHandler.kt`
**Giao thức:** HTTP thuần (không qua WebSocket envelope `rid`/`cmd`)
**Xác thực:** Có — Bearer token, vai trò `GAME_CLIENT`
**Mô tả:** Trả về nội dung file cấu hình level (level config) dạng JSON. File được chọn dựa theo `APP_STAGE` hiện tại của server: `PROD` → `prod.json`, các stage còn lại (`LOCAL`, `TEST`) → `test.json`. File được đọc từ thư mục `LEVEL_CONFIG_DIRECTORY`.

---

## Request

```http
GET /gsort/level/config HTTP/1.1
Authorization: Bearer <game_client_token>
```

Không có query string, không có request body.

---

## Response

### Thành công — `200 OK`

```http
HTTP/1.1 200 OK
Content-Type: application/json

<nội dung nguyên văn của prod.json hoặc test.json>
```

Body trả về chính là nội dung thô (raw) của file cấu hình tương ứng, cấu trúc JSON tùy theo nội dung file, handler không parse hay biến đổi gì thêm.

### Lỗi có thể xảy ra

| Status | errorCode | Tình huống |
|:------:|-----------|------------|
| `401 Unauthorized` | `UNAUTHORIZED` | Thiếu header `Authorization`, hoặc token không hợp lệ/hết hạn |
| `403 Forbidden` | `FORBIDDEN` | Token hợp lệ nhưng không thuộc vai trò `GAME_CLIENT` (ví dụ: token admin) |
| `500 Internal Server Error` | `1` (`GENERAL_ERROR`) | Không đọc được file cấu hình (ví dụ: file `prod.json`/`test.json` không tồn tại trong `LEVEL_CONFIG_DIRECTORY`) |

---

