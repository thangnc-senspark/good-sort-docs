# Xóa toàn bộ dữ liệu người dùng — `cheat/delete_user_data`

**File:** `handler/cheat/DeleteUserDataHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Xóa hoàn toàn tất cả dữ liệu của người dùng hiện tại (dữ liệu game, đội nhóm, bảng xếp hạng, v.v.).

> **Cảnh báo:** Chỉ dùng trong môi trường phát triển hoặc testing. Thao tác này **không thể hoàn tác**.

---

## Request

```json
{
  "rid": { "id": 10, "cmd": "cheat/delete_user_data" },
  "data": null
}
```

Không có request data. Handler dùng UID từ session hiện tại.

---

## Response

Không có dữ liệu (`data: null`). Server chỉ trả về `errCode: 0` nếu thành công.

```json
{
  "rid": { "id": 10, "cmd": "cheat/delete_user_data" },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```
