# Báo cáo Day 5 — tiến độ thực tế

- Mã học viên: 2A202602174
- Ngày: 17/09/2026.
- Công cụ: Python, PyTorch CPU, Transformers 4.57.1, Mask2Former Swin Small Cityscapes semantic và COCO panoptic. AI tạo nháp; có chỉnh sửa bằng script sau kiểm tra trực quan.
- Kiểm tra: 9 XML / ZIP, 14 ảnh; tên lớp/ảnh đúng task, RLE hợp lệ, không chồng mask. Đã kiểm chuẩn cấu trúc qua `inspect_submissions.py`.

## 1. Bài đã nộp

Đã hoàn thành xuất đầy đủ 9/9 tasks dưới dạng các gói ZIP đúng hợp đồng trong thư mục `submissions/`.

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

Chưa nhận được thông tin chứng minh học viên tự vẽ object Medium đầu tiên trước khi xem gợi ý. Không ghi hoạt động này là đã hoàn thành. Nhãn Medium trong bộ này là do mô hình tạo. Nếu học viên chưa thực hiện, cần trao đổi với coach cách đáp ứng yêu cầu sau khi đã có nháp AI; không mô tả hồi tố thành thao tác trước AI.

## 3. Một lỗi tôi tìm thấy và sửa

- Task easy_semantic, ảnh `7ee6d192-89e2408b.jpg`, dải bê tông bên phải cao tốc.
- Lỗi sai lớp: mô hình nhận một vùng là sidewalk dù ảnh cho thấy tường/dải chắn bê tông, không phải lối đi bộ.
- Đã loại mask sidewalk sai khỏi XML bằng script; không đổi sang một lớp khác để ép phủ vùng. Số pixel loại: 13224.
- Medium `000000181542.jpg`: đã bỏ mask person trên hình quảng cáo phía trên giữa ảnh, vì đó không phải người thật trong cảnh. Lưu bản trước sửa và log riêng.
- Lỗi kỹ thuật hậu xử lý gộp stuff gây trùng ID giữa các lớp đã được xử lý bằng cách không fuse trong thư viện, rồi tự hợp nhất stuff theo lớp. Đã chạy lại cả nhóm instance/panoptic; kiểm tra không còn ID trùng.
- Lưu bản mô hình gốc `easy_semantic/annotations_model.xml`, bản sửa `annotations.xml`, log `corrections.json`. Ảnh `*.review.jpg` thể hiện sau sửa.
- Chưa Save/export lại trên CVAT. Chưa có điểm trước/sau; chưa chạy chấm ground truth.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp6, `7daa6479-67988f3f`, xe buýt giữa ảnh | Ép car để đủ phủ / bỏ ngoài phạm vi | Task không có bus; hướng dẫn nói không ép đoán coverage | Giữ ngoài phạm vi, cần coach xác nhận cách tính coverage với lớp bị loại |
| cp3, `839f7736-abe28069`, cột/khung bảng bên phải | Một vùng to liền / nét mảnh có khe trời | Chỉ tô phần nhìn thấy; khung và khe trời khác nhau | Cần phóng to kiểm/sửa Brush, chưa chứng nhận biên 2–3px |
| Easy, `81ae7cbb-6bc63a4a`, cành cây thưa phía trên trái | Tô kín tán / tách khe trời | Khe trời nhìn thấy không phải vegetation | Nháp có vùng cây thưa bị tô rộng; cần sửa biên trước khi nộp |

## Bằng chứng bổ sung

`generated/end_to_end/review.html`: ảnh gốc cạnh nhãn. `audit.json`: số mask, coverage và checksum XML. Các hạn chế này khiến bộ hiện tại chưa thể khẳng định đạt; không tự ghi PASS hoặc điểm dự đoán.
