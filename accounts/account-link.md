# Liên kết tài khoản — `account/link`

**File:** `handler/accounts/LinkAccountHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Liên kết tài khoản guest (theo `deviceId`) với tài khoản Google (`googleUniqueId`). Sau khi liên kết, dữ liệu game của tài khoản guest được gắn với tài khoản Google.

---

## Request

```json
{
  "rid": { "id": 4, "cmd": "account/link" },
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
  "message": "Accounts linked successfully"
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `result` | `Boolean` | `true` nếu liên kết thành công |
| `message` | `String?` | Thông báo kết quả |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `1` (`GENERAL_ERROR`) | Lỗi hệ thống khi thực hiện liên kết |
