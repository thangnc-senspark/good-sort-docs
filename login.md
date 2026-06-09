# Login — `login`

**File:** `handler/LoginHandler.kt`  
**Xác thực:** Không yêu cầu  
**Mô tả:** Đăng nhập hoặc tạo tài khoản mới. Phải gọi trước tất cả các lệnh cần xác thực.

---

## Request

```json
{
  "rid": { "id": 1, "cmd": "login" },
  "data": "{\"uniqueId\":\"abc123\",\"accountType\":\"guest\",\"platform\":\"android\"}"
}
```

### Request Data

```json
{
  "uniqueId": "<device ID hoặc Google UID>",
  "accountType": "device | google",
  "platform": "android | ios"
}
```

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `uniqueId` | `String` | ID định danh duy nhất của thiết bị hoặc tài khoản Google |
| `accountType` | `String` | Loại tài khoản: `device` hoặc `google` |
| `platform` | `String` | Nền tảng: `android` hoặc `ios` |

---

## Response

### Response Data (thành công)

```json
{
  "uid": "<encoded user ID>",
  "secretKey": "null",
  "credentials": "null",
  "accountState": 0,
  "serverInfo": {
    "supportUploadVersion": 1,
    "supportDownloadVersion": 1,
    "timeInterval": 90 // Thời gian tối thiểu mỗi lần ping
  }
}
```

| Trường | Kiểu | Mô tả                                     |
|--------|------|-------------------------------------------|
| `uid` | `String` | ID người dùng đã được mã hóa (HashId)     |
| `secretKey` | `String` | Dự phòng, hiện trả về `"null"`            |
| `credentials` | `String` | Dự phòng, hiện trả về `"null"`            |
| `accountState` | `Int` | Trạng thái tài khoản(0-NORMAL,1-DELETING) |
| `serverInfo` | `Object` | Thông tin server                          |

### Lỗi có thể xảy ra

| errCode | Tình huống |
|---------|------------|
| `5` (`LOGIN_ERROR`) | Dữ liệu không hợp lệ hoặc lỗi hệ thống khi tạo/cập nhật tài khoản |
