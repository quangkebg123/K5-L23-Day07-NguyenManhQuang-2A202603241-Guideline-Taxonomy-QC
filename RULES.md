# Quy tắc làm bài

## Hình thức

- **Nhóm 2–5 người**, mỗi thành viên có guideline, nhãn và ghi chú riêng. Mọi người làm cùng phạm vi (40 ảnh) và cùng tiêu chí QC.
- Dùng **mã thành viên** ba chữ số (ví dụ `101`) trong tên file, Task và nhật ký.

## Độc lập

- Viết guideline cá nhân và gán nhãn **độc lập**. Gửi riêng từng bản cho tới khi cả nhóm xong chặng đó.
- Lúc gán dùng đúng v1.0 đã khóa. Chưa chắc thì tick `can_review` hoặc ghi vấn đề cấp ảnh; không xem nhãn của bạn để hỏi.
- Họp nhóm ở Pha 2 và Pha 5.

## AI

- Được dùng AI để giải thích khái niệm (IoU, IAA…), gợi ý viết script, sửa chính tả. Ghi công cụ và cách bạn kiểm tra trong ghi chú cá nhân.
- **Không dùng AI hay mô hình để gán box thay bạn** (pre-label sẽ học ở Day 08) và không dùng AI để "nhìn ảnh rồi cho biết nên gán gì". Nhãn của bạn là phán đoán của bạn.
- Guideline cá nhân phải phản ánh các ca khó **bạn tự tìm ra** trong 40 ảnh. Bạn phải giải thích được từng quyết định trong file mình nộp.

## Số liệu trung thực

- Giữ nguyên artifact do script sinh. Phát hiện bug thì ghi thay đổi và bằng chứng chạy lại.
- Giữ nguyên mục `rules` (ngưỡng) trong `taxonomy.json`.
- Không tạo nhãn, số đo, dòng QC hay quyết định chưa thực sự làm. Hold hoặc Rework có căn cứ là kết quả hợp lệ.

## Dữ liệu và nộp bài

- Giữ ghi công ảnh ([data/README.md](data/README.md)). Không đưa thêm thông tin cá nhân hay thông tin đăng nhập vào gói nộp.
- Nộp **riêng tư** theo [SUBMISSION.md](SUBMISSION.md). Thời hạn và kênh do coach công bố; nộp muộn theo quy định của lớp.
