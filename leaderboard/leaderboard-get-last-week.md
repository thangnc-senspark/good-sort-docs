# Lấy bảng xếp hạng tuần trước — `leaderboard/get_last_week_leaderboard`

**File:** `handler/leaderboard/GetLastWeekLeaderboardHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lấy bảng xếp hạng `WEEKLY_PLAYER` của tuần trước (tuần tính từ thứ Hai đến Chủ Nhật UTC).

---

## Request

```json
{
  "rid": { "id": 11, "cmd": "leaderboard/get_last_week_leaderboard" },
  "data": null
}
```

Không có request data.

---

## Response

### Response Data (thành công) — `PlayerLeaderboardData`

Cấu trúc giống hệt response của [`leaderboard/get_leaderboard`](leaderboard-get.md) với `type = WEEKLY_PLAYER`.

```json
{
  "leaderboardType": "WEEKLY_PLAYER",
  "entries": [
    {
      "rank": 1,
      "playerId": "abc123",
      "playerName": "Nguyen Van A",
      "playerNation": "VN",
      "playerScore": 35,
      "playerAvatar": "avatar_01",
      "teamId": null,
      "teamName": null
    }
  ],
  "myEntry": {
    "rank": 15,
    "playerId": "xyz789",
    "playerName": "Tran Thi B",
    "playerNation": "VN",
    "playerScore": 10,
    "playerAvatar": "avatar_05",
    "teamId": null,
    "teamName": null
  },
  "weekStartDate": 1748736000000,
  "weekEndDate": 1749340799000
}
```

| Trường           | Kiểu                          | Mô tả                                       |
|------------------|-------------------------------|---------------------------------------------|
| `leaderboardType`| `LeaderboardType`             | Luôn là `WEEKLY_PLAYER`                     |
| `entries`        | `List<PlayerLeaderboardEntry>`| Top 20 người chơi của tuần trước            |
| `myEntry`        | `PlayerLeaderboardEntry?`     | Vị trí của người chơi hiện tại tuần trước. `null` nếu không tham gia |
| `weekStartDate`  | `Long`                        | Timestamp (ms) bắt đầu tuần trước           |
| `weekEndDate`    | `Long`                        | Timestamp (ms) kết thúc tuần trước          |

Xem chi tiết các trường trong `PlayerLeaderboardEntry` tại [leaderboard-get.md](leaderboard-get.md).

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `7` (`INVALID_SESSION`) | Session không hợp lệ hoặc đã hết hạn |

---

## Ghi chú

- "Tuần trước" được tính tự động: `weekStart = currentWeekStart − 7 ngày`.
- Dữ liệu tuần trước **không bị xóa** khi tuần mới bắt đầu, chỉ bảng tuần hiện tại được reset khi dùng cheat reset.
- Nếu người chơi không hoạt động tuần trước, `myEntry` là `null`.
