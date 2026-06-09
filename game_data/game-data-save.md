# Lưu dữ liệu game — `game_data/save`

**File:** `handler/game_data/SaveGameDataHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lưu dữ liệu game của người dùng lên server. Handler **không trả về response**, client không cần chờ xác nhận.

---

## Request

```json
{
  "rid": { "id": 9, "cmd": "game_data/save" },
  "data": "{\"userdata\":{\"level\":\"10\",\"score\":\"9500\"}}"
}
```

### Request Data

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
| `userdata` | `Map<String, String>` | Dữ liệu game cần lưu |

---

## Response

Không có. Server trả về `null`, client không nhận response cho lệnh này.
