# Kiểm tra liên kết tài khoản — `account/can_link`

**File:** `handler/accounts/CanLinkAccountHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Kiểm tra xem tài khoản guest (`deviceId`) có thể liên kết với tài khoản Google (`googleUniqueId`) hay không, mà **không** thực sự thực hiện liên kết.

---

## Request

```json
{
  "rid": { "id": 3, "cmd": "account/can_link" },
  "data": "{\"deviceId\":\"abc123\",\"googleUniqueId\":\"google_xyz\"}"
}
```

### Request Data

```json
{
  "deviceId": "<encoded device UID>",
  "googleUniqueId": "<Google unique ID>"
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `deviceId` | `String` | UID thiết bị đã được mã hóa (HashId) |
| `googleUniqueId` | `String` | ID định danh tài khoản Google |

---

## Response

### Response Data (thành công)

```json
{
  "result": true,
  "message": null
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `result` | `Boolean` | `true` nếu có thể liên kết, `false` nếu không thể |
| `message` | `String?` | Thông báo lỗi chi tiết nếu `result = false` |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `1` (`GENERAL_ERROR`) | Lỗi hệ thống khi kiểm tra |
