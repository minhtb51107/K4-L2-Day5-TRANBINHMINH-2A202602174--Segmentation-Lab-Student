# Báo cáo Day 5 — tiến độ thực tế

- Mã học viên: 2A202602174
- Ngày: 17/09/2026.
- Công cụ: CVAT local (Polygon, Brush), Python hỗ trợ kiểm tra định dạng và tự đánh giá với reference tiers.
- Kiểm tra: 9 file ZIP trong `submissions/`, 14 ảnh; tên lớp/ảnh đúng task, RLE hợp lệ, không chồng mask. Đã kiểm chuẩn cấu trúc qua `inspect_submissions.py` và chạy tự đánh giá qua `scoring/score.py`.

## 1. Bài đã nộp

Đã hoàn thành vẽ, lưu trên CVAT và xuất đầy đủ 9/9 tasks dưới dạng các gói ZIP đúng hợp đồng trong thư mục `submissions/`.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh trên CVAT | Điểm tối đa |
| --- | --- | --- | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- **Ảnh, vị trí và object Medium đầu tiên tự vẽ:** Task `medium_instance`, ảnh `000000458325.jpg`, chiếc xe ô tô màu đỏ/sẫm ở tiền cảnh phía dưới bên phải.
- **Class và quy tắc dùng để chọn biên:** Class `car`. Quy tắc: chỉ vẽ bao quanh phần thân xe nhìn thấy rõ, bám sát mép bánh xe và gầm xe tiếp xúc mặt đường; không kéo mask lan sang vùng bóng đổ dưới gầm hoặc mặt đường xung quanh.
- **Đối chiếu gợi ý sau đó:** Khi bật gợi ý/kiểm tra, thấy mô hình nhận diện tốt thân xe nhưng phần đuôi hơi lấn sang bóng nền, tôi giữ nguyên quyết định vẽ tay bám sát viền xe thật, tỉa gọn viền tiếp giáp mặt đường để không bị tính là sai biên.

## 3. Một lỗi tôi tìm thấy và sửa

- **Task/ảnh/vùng:** Task `easy_semantic`, ảnh `7ee6d192-89e2408b.jpg`, dải bê tông bên phải cao tốc.
- **Lỗi thuộc loại:** Sai lớp (misclassification).
- **Bằng chứng nhìn thấy:** Vùng dải chắn bê tông ngăn cách làn đường cao tốc bị nhận diện nhầm thành `sidewalk`, trong khi thực tế đây là rào chắn/dải phân cách cứng, không có người đi bộ hay chức năng vỉa hè.
- **Quy tắc và hành động sửa:** Đã loại bỏ nhãn `sidewalk` sai này, điều chỉnh lại ranh giới giữa `road` và phần lề đúng chức năng.
- **Sau sửa:** Đã Save và export lại file `easy_semantic.zip` chuẩn `Segmentation mask 1.1`.
- **Kết quả tự đánh giá:** Chạy `scoring/score.py` với ground truth `easy_semantic` đạt mIoU **0.803** (17.9 / 20 điểm), trong đó lớp `road` đạt IoU **0.966** và `sky` đạt **0.964**.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp6, `7daa6479-67988f3f`, xe buýt giữa ảnh | Ép sang class `car` để tăng coverage hay bỏ ngoài phạm vi (unlabeled) | Task `cp6_coverage` danh sách lớp chỉ có `car`, không có `bus`. Quy tắc lab: không gán sai bản chất để ép tăng % coverage | Giữ ngoài phạm vi (unlabeled), tuân thủ đúng taxonomy lớp của task |
| cp3, `839f7736-abe28069`, khung cột bảng bên phải | Tô liền một mảng to hay dùng brush nhỏ tỉa nét mảnh tách khe trời | Khung sắt có các khe rỗng nhìn thấy nền trời xanh phía sau. Quy tắc: chỉ tô phần vật thể nhìn thấy | Dùng brush nhỏ tỉa nét mảnh `pole` / `traffic sign`, chừa khoảng trống cho `sky` |
| Easy, `81ae7cbb-6bc63a4a`, cành cây thưa phía trên trái | Tô kín cả tán lá hay chừa các khe trời xen kẽ | Các khoảng trời xanh xen giữa cành lá thưa. Quy tắc: trời thấy được vẫn thuộc class `sky` | Tỉa viền tán cây `vegetation` ở mức hợp lý, chừa khoảng hở lớn của `sky` |

## Bằng chứng bổ sung

- Đã chạy kiểm tra tự đánh giá nội bộ với bộ tham chiếu groundtruth 3 tier (`data/tiers/*/groundtruth`), tổng điểm 3 tier đạt **27.2 / 82**.
- Toàn bộ 9/9 task exports đều vượt qua kiểm tra cấu trúc với `scripts/inspect_submissions.py` (0 lỗi hợp đồng).
