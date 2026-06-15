# Lấy bảng xếp hạng — `leaderboard/get_leaderboard`

**File:** `handler/leaderboard/GetLeaderboardHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lấy bảng xếp hạng người chơi theo loại và phạm vi. Trả về danh sách top và vị trí của người chơi hiện tại.

---

## Request

```json
{
  "rid": { "id": 10, "cmd": "leaderboard/get_leaderboard" },
  "data": "{\"type\":\"ALL_PLAYER\",\"scope\":\"WORLD\"}"
}
```

### Request Data (`GetLeaderboardRequest`)

| Trường  | Kiểu              | Bắt buộc | Mô tả                             |
|---------|-------------------|:--------:|-----------------------------------|
| `type`  | `LeaderboardType` | Có       | Loại bảng xếp hạng (xem bên dưới) |
| `scope` | `LeaderboardScope`| Không    | Phạm vi xếp hạng. Mặc định: `WORLD` |

### `LeaderboardType`

| Giá trị          | Mô tả                                                                 | 
|------------------|-----------------------------------------------------------------------|
| `ALL_PLAYER`     | Xếp hạng tất cả người chơi theo điểm tổng (level cao nhất đạt được)  | 
| `WEEKLY_PLAYER`  | Xếp hạng theo số level đạt được trong tuần hiện tại                  | 
| `ALL_TEAM`       | Xếp hạng theo tổng điểm của đội                                       | 

> `ALL_TEAM` chưa được triển khai, trả về danh sách rỗng.

### `LeaderboardScope`

| Giá trị  | Mô tả                                      |
|----------|--------------------------------------------|
| `WORLD`  | Toàn cầu — top 200 người chơi cao điểm nhất |
| `NATION` | Quốc gia — top 200 người cùng quốc gia      |

---

## Response

### Response Data (thành công) — `PlayerLeaderboardData`

```json
{
  "leaderboardType": "ALL_PLAYER",
  "entries": [
    {
      "rank": 1,
      "playerId": "abc123",
      "playerName": "Nguyen Van A",
      "playerNation": "VN",
      "playerScore": 250,
      "playerAvatar": "avatar_01",
      "teamId": null,
      "teamName": null
    }
  ],
  "myEntry": {
    "rank": 42,
    "playerId": "xyz789",
    "playerName": "Tran Thi B",
    "playerNation": "VN",
    "playerScore": 130,
    "playerAvatar": "avatar_05",
    "teamId": null,
    "teamName": null
  },
  "weekStartDate": null,
  "weekEndDate": null
}
```

| Trường           | Kiểu                          | Mô tả                                                          |
|------------------|-------------------------------|----------------------------------------------------------------|
| `leaderboardType`| `LeaderboardType`             | Loại bảng xếp hạng được trả về                                 |
| `entries`        | `List<PlayerLeaderboardEntry>`| Danh sách người chơi trên bảng (tối đa 200 với ALL_PLAYER, 20 với WEEKLY_PLAYER) |
| `myEntry`        | `PlayerLeaderboardEntry?`     | Vị trí của người chơi hiện tại. `null` nếu chưa có điểm       |
| `weekStartDate`  | `Long?`                       | Timestamp (ms) bắt đầu tuần. Chỉ có với `WEEKLY_PLAYER`       |
| `weekEndDate`    | `Long?`                       | Timestamp (ms) kết thúc tuần. Chỉ có với `WEEKLY_PLAYER`      |

### `PlayerLeaderboardEntry`

| Trường        | Kiểu      | Mô tả                                                                    |
|---------------|-----------|--------------------------------------------------------------------------|
| `rank`        | `Int`     | Thứ hạng (bắt đầu từ 1)                                                  |
| `playerId`    | `String`  | ID người chơi (đã mã hóa bằng HashId)                                    |
| `playerName`  | `String`  | Tên người chơi                                                            |
| `playerNation`| `String`  | Mã quốc gia (ISO 3166-1 alpha-2, ví dụ: `"VN"`, `"US"`)                 |
| `playerScore` | `Int`     | Điểm số (level tổng với ALL_PLAYER; level đạt được trong tuần với WEEKLY) |
| `playerAvatar`| `String`  | ID avatar người chơi                                                      |
| `teamId`      | `String?` | ID đội nhóm (hiện tại luôn là `null`)                                     |
| `teamName`    | `String?` | Tên đội nhóm (hiện tại luôn là `null`)                                    |

### Lỗi có thể xảy ra

| errCode | Tình huống                                  |
|---------|---------------------------------------------|
| `2` (`INVALID_REQUEST_DATA`) | `data` là `null` hoặc JSON không hợp lệ |
| `7` (`INVALID_SESSION`) | Session không hợp lệ hoặc đã hết hạn |

---

## Ghi chú

- **Điểm số (ALL_PLAYER):** Bằng giá trị `CurrentLevel` cao nhất người chơi từng đạt được. Điểm chỉ tăng, không bao giờ giảm.
- **Điểm số (WEEKLY_PLAYER):** Bằng số level người chơi đạt được trong tuần (= level hiện tại − level tại thời điểm bắt đầu tuần). Tuần tính từ **thứ Hai 00:00 UTC** đến **Chủ Nhật 23:59:59 UTC**.
- **Caching:** Danh sách top được cache **5 phút**. Vị trí của bản thân (`myEntry`) luôn được tính theo thời gian thực nếu không nằm trong top cache.
- Nếu người chơi chưa bao giờ lưu dữ liệu game, `myEntry` sẽ là `null`.
