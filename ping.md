# Ping — `ping`

**File:** `handler/PingHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Kiểm tra kết nối WebSocket. Client gửi ping, server phản hồi "Pong".

---

## Request

```json
{
  "rid": { "id": 2, "cmd": "ping" },
  "data": null
}
```

Không có request data.

---

## Response

### Response Data (thành công)

```json
{
  "message": "Pong"
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `message` | `String` | Luôn là `"Pong"` |

## Notes
- Server sẽ ngắt các kết nối khi không sử dụng. Vì vậy để tránh xuâất hiện lỗi **INVALID_SESSION** thì sử dụng handler này để liên tục giữ kết nối