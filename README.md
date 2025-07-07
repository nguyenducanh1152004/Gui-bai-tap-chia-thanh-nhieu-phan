# Truyền file an toàn – Đề 6: Gửi bài tập chia làm nhiều phần

## Giới thiệu

 Đây là một hệ thống bảo mật cho quá trình truyền bài tập dạng file văn bản, trong đó nội dung file được chia nhỏ và mã hóa bằng DES để đảm bảo tính bảo mật. Danh tính của người gửi và người nhận được xác thực bằng chữ ký số RSA 1024-bit, đồng thời hệ thống sử dụng hàm băm SHA-512 để kiểm tra tính toàn vẹn của từng phần dữ liệu.

 Hệ thống mô phỏng theo mô hình thực tế khi giảng viên cần gửi bài tập đến hệ thống chấm điểm qua mạng có băng thông hạn chế, đảm bảo bí mật – toàn vẹn – xác thực trong toàn bộ quá trình truyền tải.

---

## Các kỹ thuật sử dụng

| Thành phần | Kỹ thuật |
|------------|----------|
| Mã hóa      | DES (CBC Mode) |
| Ký số       | RSA 1024-bit (PKCS#1 v1.5 + SHA-512) |
| Toàn vẹn    | SHA-512 |
| Giao diện   | Flask + Bootstrap |
| Trao đổi khóa | RSA Public/Private Key |

---

## Cấu trúc thư mục
![image](https://github.com/user-attachments/assets/c0f9637e-257c-4e8e-8c3d-272c026db9ec)


---

## Luồng xử lý

### 1. Handshake
- Người gửi gửi `"Hello!"`
- Người nhận phản hồi `"Ready!"`

### 2. Ký số & trao khóa
- Người gửi ký metadata `{filename | timestamp | số phần}` bằng **RSA + SHA-512**
- Session Key dùng để mã hóa DES được mã hóa bằng **khóa công khai của người nhận**

### 3. Mã hóa và kiểm tra toàn vẹn
- File `assignment.txt` được chia thành **3 phần**
- Mỗi phần bao gồm:
```json
{
  "iv": "<Base64>",            
  "cipher": "<Base64>",        
  "hash": "<SHA-512 hex>",     
  "sig": "<Base64 chữ ký số>"  
}
```
### 4. Nhận và xác thực
- Người nhận kiểm tra: Chữ ký metadata, Chữ ký từng phần và Hash toàn vẹn

- Nếu hợp lệ: Giải mã từng phần, Ghép và lưu lại thành assignment.txt và Gửi ACK

- Nếu không hợp lệ: Gửi NACK, chỉ rõ lỗi (hash sai, chữ ký sai,...)

---

## Hướng dẫn sử dụng

### 1. Cài đặt môi trường
 Yêu cầu: Python 3.10+

 Cài đặt thư viện cần thiết:

```bash
python -m pip install flask
python -m pip install pycryptodome
```

### 2. Tạo khóa RSA cho người gửi và người nhận

```bash
python generate_keys.py
```

### 3. Chạy ứng dụng

```bash
python app.py
```
 Truy cập trình duyệt tại: http://127.0.0.1:5000


### 4. Gửi bài tập (Người gửi)
 Truy cập tab Người gửi 

 Tải file lên, bao gồm 2 file:
- File bài tập (ví dụ: assignment.txt)
- Khóa công khai của người nhận (receiver_public.pem)

 Sau khi tải lên xong thì nhấn `Gửi`

### 5. Nhận bài tập (Người nhận)
 Truy cập tab Người nhận 

 Tải file lên, bao gồm 3 file: 
- File đã mã hóa (packet.json)
- Khóa riêng của người nhận (receiver_private.pem)
- Khóa công khai của người gửi (sender_public.pem)

 Sau khi tải lên xong thì nhấn `Nhận`

### 6. Phản hồi và Thông báo
 Nếu giải mã thành công: responses/ack.json sẽ được tạo, kèm nội dung phản hồi.

 Nếu phát hiện lỗi: responses/nack.json sẽ ghi rõ nguyên nhân (sai chữ ký, hash mismatch, khóa không hợp lệ...).

---

## Giao diện ứng dụng

 Ứng dụng sử dụng Flask với giao diện hiện đại từ Bootstrap.


### 1. Người gửi

![image](https://github.com/user-attachments/assets/45a96c78-6afe-4be9-986a-b9ffed11b61a)

---
### 2. Người nhận

![image](https://github.com/user-attachments/assets/b83f6385-42cd-452d-b81f-94d4e3b9531f)

---


## Thành viên thực hiện

Trường: Đại học Đại Nam

Lớp: Công nghệ thông tin 16-04

Nhóm: Nhóm 3

Danh sách thành viên:
- Nguyễn Trọng Đàn
- Nguyễn Đức Anh
- Phạm Thành Hưng
