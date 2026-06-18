# Good Sort Server — WebSocket Handlers

## Tổng quan

Server sử dụng WebSocket để giao tiếp real-time. Mỗi handler xử lý một lệnh (`cmd`) cụ thể.

### Hai loại handler

| Loại                  | Mô tả                                                               |
|-----------------------|---------------------------------------------------------------------|
| **BaseSocketHandler** | Không yêu cầu xác thực. Dùng cho `login`.                           |
| **BaseAuthHandler**   | Yêu cầu session hợp lệ. Server tự kiểm tra session trước khi xử lý. |

---

## Cấu trúc Request / Response

### Request (Client → Server)

```json
{
  "rid": {
    "id": 1,
    "cmd": "login"
  },
  "data": "<JSON string hoặc null>"
}
```

### Response (Server → Client)

```json
{
  "rid": {
    "id": 1,
    "cmd": "login"
  },
  "data": "<JSON string hoặc null>",
  "errCode": 0,
  "errMsg": null
}
```

---

## Mã lỗi (ErrorCode)

| Mã (Code) | Tên Lỗi (Enum)           | Mô tả chi tiết                                                            |
|:---------:|:-------------------------|:--------------------------------------------------------------------------|
|   **0**   | `NO_ERROR`               | Thao tác thành công.                                                      |
|   **1**   | `GENERAL_ERROR`          | Lỗi chung hệ thống (không xác định).                                      |
|   **2**   | `INVALID_REQUEST_DATA`   | Dữ liệu yêu cầu gửi lên không hợp lệ hoặc thiếu trường bắt buộc.          |
|   **3**   | `NOT_FOUND`              | Không tìm thấy tài nguyên yêu cầu.                                        |
|   **4**   | `INVALID_REQUEST_CMD`    | Lệnh hoặc hành động (Command) không hợp lệ.                               |
|   **5**   | `LOGIN_ERROR`            | Đăng nhập thất bại (Sai tài khoản hoặc mật khẩu).                         |
|   **6**   | `REGISTER_ERROR`         | Lỗi trong quá trình đăng ký tài khoản.                                    |
|   **7**   | `INVALID_SESSION`        | Phiên làm việc không hợp lệ hoặc đã hết hạn (Chưa đăng nhập).             |
|   **9**   | `CANNOT_JOIN_ROOM`       | Không thể tham gia vào phòng chat.                                        |
|  **10**   | `GET_CHAT`               | Lỗi khi lấy lịch sử/thông tin đoạn chat.                                  |
|  **11**   | `ERROR_CREATE_CHAT_ROOM` | Lỗi không thể tạo phòng chat mới.                                         |
|  **12**   | `ERROR_ADD_CHAT_MESSAGE` | Lỗi không thể gửi hoặc thêm tin nhắn vào đoạn chat.                       |
|  **13**   | `MAX_CAPACITY`           | Vượt quá dung lượng hoặc số lượng giới hạn cho phép (ví dụ: phòng đầy).   |
|  **14**   | `EXISTING_REQUEST`       | Yêu cầu này đã tồn tại hoặc đang được xử lý, không thể gửi lại trùng lặp. |
|  **15**   | `PERMISSION_DENIED`      | Bị từ chối thực hiện do không đủ đặc quyền.                               |
|  **16**   | `UNAUTHORIZED`           | Không có quyền truy cập (Yêu cầu xác thực tài khoản).                     |
|  **17**   | `FORBIDDEN`              | Bị cấm truy cập vào tài nguyên này.                                       |

---

## Danh sách Handler

| Lệnh (`cmd`)             | File                                                          | Xác thực | Mô tả                                       |
|--------------------------|---------------------------------------------------------------|----------|---------------------------------------------|
| `login`                  | [login.md](login.md)                                          | Không    | Đăng nhập / tạo tài khoản                   |
| `ping`                   | [ping.md](ping.md)                                            | Có       | Kiểm tra kết nối                            |
| `account/can_link`       | [account-can-link.md](accounts/account-can-link.md)           | Có       | Kiểm tra khả năng liên kết tài khoản        |
| `account/link`           | [account-link.md](accounts/account-link.md)                   | Có       | Liên kết tài khoản Google                   |
| `account/delete_link`    | [account-delete-link.md](accounts/account-delete-link.md)     | Có       | Gỡ liên kết Google                          |
| `account/delete`         | [account-delete.md](accounts/account-delete.md)               | Có       | Đánh dấu xóa tài khoản                      |
| `account/cancel_delete`  | [account-cancel-delete.md](accounts/account-cancel-delete.md) | Có       | Hủy xóa tài khoản                           |
| `game_data/get`          | [game-data-get.md](game_data/game-data-get.md)                | Có       | Lấy dữ liệu game                            |
| `game_data/save`         | [game-data-save.md](game_data/game-data-save.md)              | Có       | Lưu dữ liệu game                            |
| `cheat/delete_user_data` | [cheat-delete-user-data.md](cheat/cheat-delete-user-data.md)  | Có       | Xóa toàn bộ dữ liệu người dùng *(dev only)* |
| `leaderboard/get_leaderboard`       | [leaderboard-get.md](leaderboard/leaderboard-get.md)                               | Có       | Lấy bảng xếp hạng (all-time hoặc tuần)      |
| `leaderboard/get_previous_weekly_leaderboard` | [leaderboard-get-last-week.md](leaderboard/leaderboard-get-last-week.md) | Có       | Lấy bảng xếp hạng tuần chỉ định theo nhóm   |
| `leaderboard/get_remaining_time`    | [leaderboard-remaining-time.md](leaderboard/leaderboard-remaining-time.md)         | Có       | Thời gian còn lại của tuần xếp hạng         |
| `cheat/reset_leaderboard`           | [cheat-reset-leaderboard.md](cheat/cheat-reset-leaderboard.md)                     | Có       | Reset bảng xếp hạng tuần *(dev only)*       |
