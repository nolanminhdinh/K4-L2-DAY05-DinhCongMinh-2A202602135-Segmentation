# Phiếu quy tắc gán nhãn Day 5 (Guideline Mini-Sheet)

Tài liệu tóm tắt quy tắc kỹ thuật, taxonomy lớp và checklist kiểm tra chất lượng (QC) cho toàn bộ 9 task trong bài lab Segmentation Day 5.

---

## 1. Chọn đúng loại bài toán trước khi vẽ

| Loại bài toán | Câu hỏi bản chất | Task áp dụng trong bài | Nguyên tắc phân định |
| :--- | :--- | :--- | :--- |
| **Semantic Segmentation** | Pixel này thuộc **loại vùng nào**? | `easy_semantic`, `cp3_thin`, `cp4_curb`, `cp6_coverage` | Gán nhãn theo vùng bề mặt (stuff) hoặc chủng loại pixel; không phân biệt cá thể riêng lẻ. |
| **Instance Segmentation** | Pixel này thuộc **vật thể (cá thể) nào**? | `medium_instance`, `cp1_holes`, `cp2_slice`, `cp5_occlusion` | Chỉ gán nhãn các đối tượng đếm được (thing). Mỗi cá thể là một mask/object riêng biệt. |
| **Panoptic Segmentation** | Pixel thuộc loại vùng nào **và** thuộc cá thể đếm được nào? | `hard_panoptic` | Kết hợp cả hai: phủ kín toàn bộ vùng nền (stuff) và tách riêng từng cá thể vật thể (thing #1, thing #2). |

> [!IMPORTANT]
> - Tên lớp (label name) phải **chính xác từng chữ cái và dấu cách** theo file `classes.json` của từng task.
> - Ví dụ: `traffic sign` có dấu cách, khác với `traffic_sign`. Không dùng chung danh sách nhãn của task này cho task khác.

---

## 2. Bảng tra cứu 9 Task, Danh sách lớp & Format Export

| Nhóm | Mã Task | Số ảnh | Danh sách lớp chuẩn (`classes.json`) | Format Export từ CVAT | File nộp trong `submissions/` |
| :--- | :--- | :---: | :--- | :--- | :--- |
| **Tier 1** | `easy_semantic` | 3 | `road`, `sidewalk`, `building`, `vegetation`, `sky` | **Segmentation mask 1.1** | `easy_semantic.zip` |
| **Tier 2** | `medium_instance` | 3 | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | **COCO 1.0** | `medium_instance.zip` |
| **Tier 3** | `hard_panoptic` | 2 | **Stuff (5):** `road`, `sidewalk`, `building`, `vegetation`, `sky`<br>**Things (7):** `person`, `car`, `bus`, `truck`, `motorcycle`, `bicycle`, `traffic light` | **COCO 1.0** | `hard_panoptic.zip` |
| **CP 1** | `cp1_holes` | 1 | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | **COCO 1.0** | `cp1_holes.zip` |
| **CP 2** | `cp2_slice` | 1 | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | **COCO 1.0** | `cp2_slice.zip` |
| **CP 5** | `cp5_occlusion` | 1 | `person`, `bicycle`, `car`, `motorcycle`, `bus`, `truck` | **COCO 1.0** | `cp5_occlusion.zip` |
| **CP 3** | `cp3_thin` | 1 | `pole`, `traffic sign`, `sky`, `road` | **Segmentation mask 1.1** | `cp3_thin.zip` |
| **CP 4** | `cp4_curb` | 1 | `road`, `sidewalk` | **Segmentation mask 1.1** | `cp4_curb.zip` |
| **CP 6** | `cp6_coverage` | 1 | `road`, `sidewalk`, `building`, `vegetation`, `sky`, `car`, `person` | **Segmentation mask 1.1** | `cp6_coverage.zip` |

---

## 3. Quy tắc hình học cốt lõi & Xử lý biên (Geometry Rules)

1. **Quy tắc phần nhìn thấy (Visibility Principle)**:
   - Chỉ vẽ bám sát các pixel **thực sự nhìn thấy được** trên ảnh.
   - Tuyệt đối **không tự suy đoán** hoặc kéo mask xuyên qua phần bị che khuất bởi vật cản phía trước.
2. **Quy tắc bóng đổ (Shadows)**:
   - Bóng đổ của xe/người trên mặt đường thuộc về mặt đường (`road` hoặc nền), **không gộp bóng đổ vào mask vật thể**.
3. **Quy tắc kính và khe hở (`cp1_holes`)**:
   - Kính chắn gió, cửa sổ, các khe hở cơ học trên thân xe nằm trọn trong cấu trúc của xe — **giữ nguyên trong mask xe, KHÔNG khoét lỗ rỗng**.
4. **Quy tắc tách hai vật thể sát nhau (`cp2_slice`)**:
   - Khi hai xe hoặc hai người cùng loại đứng sát hoặc đỗ liền kề nhau, phải tạo **hai object/mask riêng biệt**. Không vẽ gộp thành một mảng lớn.
5. **Quy tắc vật bị che cắt đôi (`cp5_occlusion`)**:
   - Một chiếc xe bị cột điện hoặc biển báo che cắt ngang thành 2 phần rời rạc: vẫn tính là **một instance duy nhất** (vẽ 2 polygon thuộc cùng một object ID).
6. **Quy tắc nét mảnh (`cp3_thin`)**:
   - Cột điện (`pole`), cột biển báo (`traffic sign`): Phóng to (zoom in 300% - 500%), điều chỉnh Brush nhỏ (2–3px) để vẽ đúng độ dày thực tế.
7. **Quy tắc bó vỉa hè (`cp4_curb`)**:
   - Ranh giới giữa `road` và `sidewalk` xác định theo **chức năng và gờ bó vỉa**, không dựa vào màu sắc (dù vỉa hè bị rải nhựa cùng màu với lòng đường vẫn tính là `sidewalk`).
8. **Quy tắc độ phủ kín (`cp6_coverage`)**:
   - Phủ kín 100% các pixel thuộc về danh mục các lớp đã cho, không để lại các khe hở vô danh giữa các mảng tiếp giáp.

---

## 4. Phím tắt & Mẹo thao tác nhanh trên CVAT

- **`N`**: Bắt đầu vẽ một hình mới (New shape/mask).
- **`B`**: Chọn công cụ cọ vẽ (Brush).
- **`[` / `]`**: Thu nhỏ / Phóng to kích thước đầu cọ Brush.
- **`Shift` + Kéo**: Vẽ đường thẳng nối liền các điểm trong chế độ Brush/Eraser.
- **`Space` + Kéo chuột** (hoặc nhấn giữ chuột giữa): Kéo di chuyển vùng nhìn ảnh (Pan).
- **Lăn chuột (Scroll wheel)**: Phóng to / Thu nhỏ ảnh (Zoom in / Zoom out).
- **Thanh Opacity (Độ mờ)** ở thanh bên trái: Kéo về mức 20%–40% để vừa thấy màu mask vừa thấy rõ viền thật của ảnh gốc bên dưới.
- **`F` / `D`**: Chuyển nhanh sang ảnh kế tiếp / lùi về ảnh trước.
- **`Ctrl + S`**: Lưu tiến độ ngay lập tức sau mỗi ảnh.

---

## 5. Checklist tự kiểm tra (QC) trước khi Export

1. **Đúng ảnh và đúng số lượng ảnh**: Đã nạp đủ ảnh trong thư mục `images/` của task chưa?
2. **Đúng nhãn**: Đã copy từ file `cvat-labels.json` vào ô **Labels → Raw** để tránh sai tên lớp chưa?
3. **Đúng loại bài toán**:
   - Semantic: Các vùng cùng loại có bị chia nhỏ thành nhiều object không cần thiết không?
   - Instance: Đã đếm đủ từng người, từng xe chưa? Có bị gộp 2 vật thành 1 hoặc tách sai không?
   - Panoptic: Đã vẽ đủ cả stuff (trời, đường, nhà, cây) và things (xe, người) chưa?
4. **Kiểm tra ranh giới**: Phóng to kiểm tra mask có bị ăn tràn ra nền, dính bóng đổ hoặc bỏ sót chi tiết không.
5. **Đúng định dạng Export**:
   - Task Semantic: Chọn format **`Segmentation mask 1.1`**.
   - Task Instance & Panoptic: Chọn format **`COCO 1.0`**.
6. **Đặt tên file ZIP nộp**:
   - Tên file zip phải chính xác: `<mã_task>.zip` (ví dụ: `easy_semantic.zip`, `medium_instance.zip`, `hard_panoptic.zip`).
   - Đặt file zip vào đúng thư mục `submissions/`.

---

## 6. Xử lý các ca nghi ngờ / mơ hồ (Edge Cases)

Nếu gặp vùng ảnh chất lượng mờ, bị che khuất nặng hoặc ranh giới không rõ ràng:
1. **Không đoán mò**: Ưu tiên gán nhãn theo phần nhìn thấy tin cậy nhất.
2. **Ghi lại vị trí và lý do**: Ghi chú ảnh nào, tọa độ vùng nào, hai cách hiểu là gì và quyết định của bạn.
3. **Điền vào Mục 4 của báo cáo**: Đưa thông tin này vào bảng *“Ba ca chưa chắc hoặc đã cân nhắc”* trong file [REPORT.md](file:///c:/Users/MINH/K4-L2-DAY05-DinhCongMinh-2A202602135-Segmentation/REPORT.md). Coach sẽ dựa vào giải thích hợp lý của bạn để đánh giá điểm.
