# Lấy hồ sơ người chơi — `account/get_profile`

**File:** `handler/profile/GetUserProfileHandler.kt`  
**Xác thực:** Có  
**Mô tả:** Lấy dữ liệu cho màn hình Profile của client: thông tin người chơi (tên, avatar, khung, quốc gia, level), Player ID đã mã hóa và các chỉ số thống kê chung (General Stats). Có thể xem hồ sơ của bất kỳ người chơi nào bằng cách truyền `playerId` của họ.

---

## Request

```json
{
  "rid": { "id": 12, "cmd": "account/get_profile" },
  "data": "abc123"
}
```

### Request Data

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|:--------:|-------|
| `data` | `String` | Có | Player ID đã được mã hóa (HashId) của người chơi cần xem. Truyền trực tiếp dưới dạng chuỗi, **không** phải JSON |

> Phần lớn dữ liệu lấy từ các key game-data `ProfileManager` / `LevelManager` mà client đã lưu; `firstOpenTime` lấy từ thời điểm tạo tài khoản.

---

## Response

### Response Data (thành công) — `GetUserProfileResponse`

```json
{
  "playerId": "abc123",
  "name": "Nguyen Van A",
  "avatarId": "avatar_01",
  "frameId": "frame_02",
  "nationCode": "VN",
  "teamName": null,
  "firstOpenTime": 1710000000000,
  "level": 42,
  "stats": {
    "firstTryWins": 12,
    "longestWinsStreak": 8,
    "weeklyContestTop3": 3,
    "tournamentTrophies": 1
  }
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `playerId` | `String` | Player ID đã mã hóa (HashId). Hiển thị ở góc dưới bên phải màn hình |
| `name` | `String` | Tên người chơi. Rỗng nếu chưa đặt |
| `avatarId` | `String` | ID avatar. Mặc định `"0"` |
| `frameId` | `String` | ID khung avatar. Mặc định `"0"` |
| `nationCode` | `String` | Mã quốc gia (ISO 3166-1 alpha-2, ví dụ `"VN"`). Rỗng nếu chưa đặt |
| `teamName` | `String?` | Tên đội nhóm (hiện tại luôn `null`) |
| `firstOpenTime` | `Long` | Timestamp (ms) tạo tài khoản — thời điểm mở app lần đầu |
| `level` | `Int` | Level hiện tại của người chơi. Mặc định `0` |
| `stats` | `ProfileStats` | Các chỉ số thống kê chung (xem bên dưới) |

### `ProfileStats`

| Trường | Kiểu | Mặc định | Mô tả |
|--------|------|:--------:|-------|
| `firstTryWins` | `Int` | `0` | Số lần thắng ngay lần thử đầu tiên |
| `longestWinsStreak` | `Int` | `0` | Chuỗi thắng dài nhất |
| `weeklyContestTop3` | `Int` | `0` | Số lần lọt top 3 giải tuần |
| `tournamentTrophies` | `Int` | `0` | Số cúp giải đấu |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `2` (`INVALID_REQUEST_DATA`) | `data` (Player ID) là `null` |
| `7` (`INVALID_SESSION`) | Session không hợp lệ hoặc đã hết hạn |

---

## Ghi chú

- **Player ID:** Truyền trong `data` dưới dạng chuỗi HashId đã mã hóa, không bọc trong JSON. Server giải mã ra `uid` để tra cứu dữ liệu.
- **Nguồn dữ liệu:** `name`, `avatarId`, `frameId`, `nationCode`, `stats` lấy từ key `ProfileManager`; `level` lấy từ `currentLevel` trong key `LevelManager`; `firstOpenTime` lấy từ thời điểm tạo tài khoản.
- Nếu người chơi chưa từng lưu các key tương ứng, các trường trả về giá trị mặc định (chuỗi rỗng, `"0"` hoặc `0`) thay vì gây lỗi.