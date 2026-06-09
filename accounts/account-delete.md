# Xóa tài khoản — `account/delete`

**File:** `handler/accounts/DeleteAccountHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Đánh dấu tài khoản của người dùng hiện tại để xóa. Có thể hủy bằng [`account/cancel_delete`](account-cancel-delete.md) trước khi xóa hoàn toàn.

---

## Request

```json
{
  "rid": { "id": 6, "cmd": "account/delete" },
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
| `success` | `Boolean` | `true` nếu đánh dấu xóa thành công |
