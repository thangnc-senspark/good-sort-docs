# Hủy liên kết tài khoản — `account/delete_link`

**File:** `handler/accounts/DeleteLinkAccountHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Gỡ bỏ liên kết Google khỏi tài khoản của người dùng hiện tại (lấy từ session).

---

## Request

```json
{
  "rid": { "id": 5, "cmd": "account/delete_link" },
  "data": null
}
```

Không có request data. Handler dùng UID từ session hiện tại.

---

## Response

### Response Data (thành công)

```json
{
  "result": true,
  "message": "Account link deleted successfully"
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `result` | `Boolean` | `true` nếu xóa liên kết thành công, `false` nếu không tìm thấy liên kết |
| `message` | `String?` | Thông báo kết quả |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `1` (`GENERAL_ERROR`) | Lỗi hệ thống khi xóa liên kết |
