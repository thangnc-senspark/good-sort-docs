# Lấy dữ liệu game — `game_data/get`

**File:** `handler/game_data/GetGameDataHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lấy toàn bộ dữ liệu game của người dùng hiện tại dưới dạng key-value.

---

## Request

```json
{
  "rid": { "id": 8, "cmd": "game_data/get" },
  "data": null
}
```

Không có request data.

---

## Response

### Response Data (thành công)

```json
{
  "userdata": {
    "level": "10",
    "score": "9500"
  }
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `userdata` | `Map<String, String>` | Toàn bộ dữ liệu game của người dùng |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `1` (`GENERAL_ERROR`) | Lỗi hệ thống khi truy xuất dữ liệu |
