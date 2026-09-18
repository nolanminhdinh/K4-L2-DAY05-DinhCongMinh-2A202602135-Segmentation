# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên VLearn. Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202602135
- Ngày / CVAT local: 17/09/2026 / http://localhost:8080
- Công cụ đã dùng: Brush, Polygon

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | chưa có | 0 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, object person (người đi bộ) đứng ở lề đường bên phải (tọa độ x≈555, y≈149).
- Class và quy tắc tôi dùng để chọn biên: Class `person`. Chỉ vẽ theo phần cơ thể và trang phục nhìn thấy thực tế; dừng biên sát mép giày tiếp đất và mép ngoài trang phục, không đoán phần bị che khuất bởi chiếc xe đạp bên cạnh.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Với các xe đạp (`bicycle`), gợi ý tự động thường kéo theo phần bóng đen trên mặt đường và bỏ sót phần căm xe; tôi dùng Brush tẩy phần bóng thừa và chỉnh lại viền khung xe.
- Nếu không dùng gợi ý: không dùng; tôi chủ yếu dùng Brush và Polygon tự chỉnh tay để đảm bảo biên mask khít với phần nhìn thấy của vật thể.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: `hard_panoptic`, ảnh `000000350023.jpg`, khu vực ranh giới giữa mặt đường (`road`) và lề đường (`sidewalk`).
- Lỗi thuộc loại: sai lớp / thiếu-thừa vật / gộp-tách / biên / phủ vùng / khác: Sai lớp và biên.
- Bằng chứng tôi nhìn thấy: Đoạn vỉa hè bị trũng ngang bằng mặt đường và có bóng cây che phủ làm màu sắc tương tự nhựa đường, ban đầu tôi đã tô nhầm cả mảng này thành `road`.
- Quy tắc và hành động sửa: Áp dụng quy tắc phân chia chức năng theo mép bó vỉa, tôi đã tách phần vỉa hè đó ra thành `sidewalk`, điều chỉnh lại đường biên sát mép vỉa và kiểm tra không để hở pixel nào giữa hai lớp.
- Sau sửa đã Save và export lại chưa? Đã Save trên CVAT và export lại ra `hard_panoptic.zip`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): chưa có điểm. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `easy_semantic`, ảnh `81ae7cbb-6bc63a4a.jpg`, thảm cỏ mọc lan qua mép vỉa hè | Gộp vào `vegetation` hay vẫn giữ là `sidewalk` | Nhìn thấy bề mặt cỏ phủ dày che mất mép bê tông của vỉa hè | Quyết định tô phần cỏ phủ dày là `vegetation`, chỉ phần bê tông nhô lên rõ rệt mới gán `sidewalk` theo quy tắc ưu tiên phần nhìn thấy thực tế. |
| 2. `medium_instance`, ảnh `000000373353.jpg`, xe bán tải có cabin phía trước và thùng chở hàng phía sau | Phân loại là `car` hay `truck` | Xe có cabin kín như ô tô con nhưng có thùng chở hàng riêng phía sau | Quyết định gán nhãn `truck` do có thùng chở hàng riêng biệt. Xin coach làm rõ quy tắc phân loại cho dòng xe bán tải (pickup). |
| 3. `hard_panoptic`, ảnh `000000460147.jpg`, các kẽ lá cây nhìn xuyên qua thấy bầu trời | Khoét rỗng thành lỗ để lộ `sky` hay tô phủ kín mảng `vegetation` | Tán cây dày có nhiều kẽ hở li ti xen kẽ màu xanh da trời | Khoảng trống lớn nhìn rõ nền trời thì vẽ `sky`, các kẽ lá nhỏ li ti giữ trọn trong `vegetation` để tránh sinh ra quá nhiều polygon phân mảnh nhiễu. |
