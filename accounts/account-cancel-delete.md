# Hủy xóa tài khoản — `account/cancel_delete`

**File:** `handler/accounts/CancelDeleteAccountHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Hủy yêu cầu xóa tài khoản đã được đánh dấu trước đó bằng [`account/delete`](account-delete.md).

---

## Request

```json
{
  "rid": { "id": 7, "cmd": "account/cancel_delete" },
  "data": null
}
```

Không có request data. Handler dùng UID từ session hiện tại.

---

## Response

### Response Data (thành công)

```json
{
  "success": true
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `success` | `Boolean` | `true` nếu hủy xóa thành công |
