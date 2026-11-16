# Quy tắc làm bài

## Hình thức

**Bài nhóm 2–5 người**, nhưng **mỗi thành viên có sản phẩm riêng** (guideline cá nhân, nhãn cá nhân, ghi chú cá nhân, commit của mình).
Nhóm tự sắp xếp. Mỗi nhóm một repo public; cách đặt tên repo xem [SUBMISSION.md](SUBMISSION.md).

## Độc lập và hợp tác

- **Pha 1 và Pha 3 làm độc lập.** Không xem guideline hay nhãn của nhau, không hỏi nhau "ảnh này bạn gán gì". Độ đồng thuận (IAA) chỉ có nghĩa khi hai người gán **không** nhìn nhau.
- Pha 2 và Pha 5 là họp nhóm: cùng thảo luận và chốt quyết định.
- Sau khi chốt `guideline_v1.0_agreed.md`, **không đổi guideline** cho tới hết Pha 3. Ca nào mơ hồ thì tick `can_review` rồi đem ra calibration, đừng tự đoán.
- Không sao chép bài của nhóm khác (guideline, nhãn, báo cáo).

## Sử dụng AI

- Được sử dụng dùng AI

## Tính trung thực của số liệu

- Không tự tạo nhãn, số đo, dòng QC hay quyết định chưa thực sự làm. Điểm IAA thấp hay kết quả **Rework** vẫn được điểm nếu phân tích trung thực. Báo Release khi số liệu không đạt thì bị trừ điểm.
- Không sửa tay `iaa_summary.*`, `calibration_candidates.csv`, `consensus_draft.json`, `qc_report_auto.md`: đó là đầu ra của script.
- Không đổi mục `rules` (các ngưỡng) trong `submission/taxonomy.json` để "đạt" số đo. Chỉ đổi `version`, `classes`, `attributes`.
- Được sửa lỗi của script nếu tìm thấy bug; ghi rõ sửa gì và vì sao trong báo cáo QC.
- Không sửa hay thêm/bớt ảnh trong `data/images/`.

## Dữ liệu và giấy phép

- 40 ảnh lấy từ Wikimedia Commons theo giấy phép mở; giữ nguyên `data/ATTRIBUTION.csv`. Không đưa ảnh/dữ liệu riêng tư vào repo.
- Không commit thư mục `work/`, API key, token, hay file ngoài phạm vi bài (đã có `.gitignore`).

## Git và nộp bài

- Repo **public**. Mọi thành viên phải có **commit mang tên mình** chứa sản phẩm của mình.
- Bài được chấm theo commit cuối cùng trước hạn; commit sau hạn tính là nộp muộn.
- Thời hạn và kênh nộp do giảng viên/coach công bố; nộp muộn bị trừ điểm.
