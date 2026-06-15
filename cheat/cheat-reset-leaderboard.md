# Reset bảng xếp hạng tuần — `cheat/reset_leaderboard`

**File:** `handler/leaderboard/ResetWeeklyLeaderboardHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Xóa toàn bộ điểm tuần hiện tại và chuyển ngay sang tuần mới. Dùng để test chu kỳ tuần mà không cần chờ đến thứ Hai.

> **Cảnh báo:** Chỉ dùng trong môi trường phát triển hoặc testing. Thao tác này **không thể hoàn tác**.

---

## Request

```json
{
  "rid": { "id": 13, "cmd": "cheat/reset_leaderboard" },
  "data": null
}
```

Không có request data.

---

## Response

Không có dữ liệu (`data: null`). Server chỉ trả về `errCode: 0` nếu thành công.

```json
{
  "rid": { "id": 13, "cmd": "cheat/reset_leaderboard" },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

---

## Ghi chú

- Xóa tất cả bản ghi trong `player_weekly_scores` có `week_start` = tuần hiện tại.
- Sau đó tính lại `currentWeekStart` và `currentWeekEnd` từ thứ Hai UTC hiện tại (giống như server khởi động lại).
- Cache tuần (`weeklyTopCache`) bị xóa ngay lập tức.
- Điểm **all-time** (`player_leaderboard_scores`) **không bị ảnh hưởng**.
