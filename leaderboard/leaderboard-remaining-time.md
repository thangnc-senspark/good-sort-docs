# Thời gian còn lại của tuần — `leaderboard/get_remaining_time`

**File:** `handler/leaderboard/GetLeaderboardRemainingTimeHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lấy thông tin thời gian của tuần xếp hạng hiện tại: thời điểm bắt đầu, kết thúc tuần và thời điểm hiện tại (server time). Dùng để client hiển thị đếm ngược hoặc xác định tuần đang chạy.

---

## Request

```json
{
  "rid": { "id": 12, "cmd": "leaderboard/get_remaining_time" },
  "data": null
}
```

Không có request data.

---

## Response

### Response Data (thành công) — `RemainingTimeData`

```json
{
  "weekStartMs": 1749340800000,
  "weekEndMs": 1749945599000,
  "nowMs": 1749500000000
}
```

| Trường        | Kiểu   | Mô tả                                                        |
|---------------|--------|--------------------------------------------------------------|
| `weekStartMs` | `Long` | Timestamp (ms) thứ Hai 00:00:00 UTC của tuần hiện tại        |
| `weekEndMs`   | `Long` | Timestamp (ms) Chủ Nhật 23:59:59 UTC của tuần hiện tại       |
| `nowMs`       | `Long` | Timestamp (ms) thời điểm hiện tại theo đồng hồ server (UTC)  |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `7` (`INVALID_SESSION`) | Session không hợp lệ hoặc đã hết hạn |

---

## Ghi chú

- Thời gian còn lại của tuần = `weekEndMs − nowMs`.
- Tuần bắt đầu vào **thứ Hai 00:00:00 UTC** và kết thúc vào **Chủ Nhật 23:59:59 UTC** (7 ngày đúng − 1 giây).
- Server tự động chuyển tuần vào lúc thứ Hai 00:00 UTC mà không cần client trigger.
