# Lấy bảng xếp hạng tuần trước — `leaderboard/get_previous_weekly_leaderboard`

**File:** `handler/leaderboard/GetPreviousWeeklyLeaderboardHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lấy bảng xếp hạng `WEEKLY_PLAYER` của một tuần cụ thể theo `weekStart`. Trả về leaderboard theo nhóm của
người chơi trong tuần đó.

---

## Request

```json
{
  "rid": {
    "id": 11,
    "cmd": "leaderboard/get_previous_weekly_leaderboard"
  },
  "data": "{\"weekStart\":1748736000000}"
}
```

### Request Data (`GetPreviousWeeklyLeaderboardRequest`)

| Trường      | Kiểu   | Bắt buộc | Mô tả                                                       |
|-------------|--------|:--------:|-------------------------------------------------------------|
| `weekStart` | `Long` |    Có    | Timestamp (ms) thứ Hai 00:00:00 UTC của tuần muốn truy vấn. |

---

## Response

### Response Data (thành công) — `PlayerLeaderboardData`

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
  "weekEndDate": 1749340799000,
  "groupId": 7
}
```

| Trường            | Kiểu                           | Mô tả                                                               |
|-------------------|--------------------------------|---------------------------------------------------------------------|
| `leaderboardType` | `LeaderboardType`              | Luôn là `WEEKLY_PLAYER`                                             |
| `entries`         | `List<PlayerLeaderboardEntry>` | Top người chơi trong nhóm của tuần đó (tối đa 20)                   |
| `myEntry`         | `PlayerLeaderboardEntry?`      | Vị trí của người chơi trong nhóm. `null` nếu không tham gia tuần đó |
| `weekStartDate`   | `Long`                         | Timestamp (ms) bắt đầu tuần                                         |
| `weekEndDate`     | `Long`                         | Timestamp (ms) kết thúc tuần                                        |
| `groupId`         | `Int?`                         | ID nhóm leaderboard của người chơi trong tuần đó                    |

Xem chi tiết các trường trong `PlayerLeaderboardEntry` tại [leaderboard-get.md](leaderboard-get.md).

### Lỗi có thể xảy ra

| errCode                      | Tình huống                                                     |
|------------------------------|----------------------------------------------------------------|
| `2` (`INVALID_REQUEST_DATA`) | `data` là `null` hoặc JSON không hợp lệ hoặc thiếu `weekStart` |
| `7` (`INVALID_SESSION`)      | Session không hợp lệ hoặc đã hết hạn                           |

---

## Ghi chú

- `weekStart` phải là timestamp ms của thứ Hai 00:00:00 UTC của tuần muốn truy vấn.
- Dữ liệu các tuần trước **không bị xóa** khi tuần mới bắt đầu.
- Nếu người chơi không hoạt động trong tuần đó, `myEntry` là `null`.
- Kết quả trả về theo nhóm (`groupId`) — không phải top toàn cục.
