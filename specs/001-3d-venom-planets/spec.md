# Feature Specification: Hệ Thống 5 Hành Tinh 3D Tương Tác & Hiệu Ứng Biến Hình "Venom" (Scroll Morphing)

**Feature Branch**: `001-3d-venom-planets`  
**Created**: 2026-11-11  
**Status**: Approved for Step-by-Step Execution  

---

## 1. User Stories & Priorities

### User Story 1 - Dựng Khối Hành Tinh 3D Thực Thụ & Xoay 360° (Priority: P1 - MVP)
Người dùng nhìn thấy 5 thiên hà/hành tinh ở màn hình tổng và khi bấm vào bất kỳ thiên hà nào sẽ bay tới trước một **khối hành tinh 3D thực thụ** (thay vì ảnh 2D `THREE.Sprite` phẳng như cũ), có thể nhấn giữ chuột kéo xoay 360° để quan sát địa hình nổi khối, khí quyển phát sáng và cấu trúc 3D đặc trưng của từng chương.

**Acceptance Scenarios**:
1. **Given** người dùng đang ở màn hình tổng (`Universe Overview`), **When** quan sát 5 thiên hà, **Then** tâm của mỗi thiên hà là một khối cầu hành tinh 3D có chiều sâu, tự quay quanh trục cùng cấu trúc phụ 3D riêng biệt.
2. **Given** người dùng bấm vào 1 thiên hà để vào chế độ khám phá (`Interior Mode`), **When** nhấn giữ và kéo chuột trên vùng không gian 3D, **Then** toàn bộ khối hành tinh 3D, lớp địa hình, khung vòm/vành đai và đám mây hạt xoay mượt 360° theo tay người dùng.

---

### User Story 2 - Hiệu Ứng Biến Hình "Venom Symbiote" Bằng Custom Shader (Priority: P1)
Khi chuyển đổi giữa hành tinh hiện tại và hành tinh kế tiếp, thay vì cắt cảnh đột ngột, một lớp vật chất 3D (màu sắc + địa hình + mạch năng lượng) của hành tinh mới sẽ **bám vào và bò lan ra dạng xúc tu (Venom style)** trùm lên bề mặt hành tinh cũ.

**Acceptance Scenarios**:
1. **Given** biến tiến trình chuyển giao `uMorphProgress` tăng từ `0.0` lên `1.0`, **When** shader render từng khung hình, **Then** đường ranh giới giữa 2 hành tinh không cắt thẳng mà uốn lượn dạng xúc tu hữu cơ (3D Simplex Noise tendrils), có viền phát sáng năng lượng (glowing seam) và hơi phồng nổi khối (vertex displacement) ngay tại mép đang bò tới.
2. **Given** mỗi hành tinh trong 5 thiên hà có cấu trúc 3D đi kèm (như khung vòm Geodesic 3D ở Thiên hà 03, vành đai Sao Thổ ở Thiên hà 05), **When** lớp Venom lan tới đâu, **Then** cấu trúc 3D phụ trợ của hành tinh mới cũng mọc/mở rộng đồng bộ tới đó.

---

### User Story 3 - Lăn Chuột / Vuốt Lên Xuống Xuyên Suốt 5 Hành Tinh (Priority: P2)
Khi đang ở bên trong một thiên hà (`Interior Mode`), người dùng không bị "nhốt" cứng ở mục đó mà có thể lăn chuột (scroll) hoặc vuốt lên/xuống để điều khiển trực tiếp hiệu ứng Venom biến đổi liên hoàn qua lại giữa 5 hành tinh (`01 ↔ 02 ↔ 03 ↔ 04 ↔ 05`).

**Acceptance Scenarios**:
1. **Given** người dùng đang ở trong Thiên hà 01 và đặt chuột ở vùng không gian 3D, **When** lăn chuột xuống (hoặc vuốt lên trên màn hình cảm ứng), **Then** lớp Venom của Thiên hà 02 lập tức bò lan dần lên hành tinh theo đúng nhịp lăn chuột, và khi hoàn tất sẽ cập nhật nội dung bảng thông tin bên phải sang Thiên hà 02.
2. **Given** người dùng đang đặt chuột bên trong bảng thông tin chữ (`interior-console`), **When** lăn chuột, **Then** bảng thông tin cuộn đọc chữ bình thường; khi đã cuộn chạm đáy (hoặc đỉnh) mà tiếp tục lăn thêm, hệ thống sẽ kích hoạt chuyển sang hành tinh kế tiếp (hoặc trước đó).

---

## 2. Functional Requirements

- **FR-001 (3D Planet Core)**: Thay thế các layer `THREE.Sprite` hành tinh 2D bằng khối cầu 3D đa giác mịn (`THREE.IcosahedronGeometry(1.15, 32)`) sử dụng Custom `THREE.ShaderMaterial`.
- **FR-002 (5 Distinct Planet Profiles)**: Định nghĩa 5 bộ thông số Shader & Geometry riêng biệt cho 5 thiên hà:
  - **01 - ALPHA (Định vị)**: Bề mặt đá Hổ Phách (`#e8c99b` / `#ffe5b4`), rãnh năng lượng nham thạch tím (`#9d4edd`), vòng la bàn quỹ đạo mảnh.
  - **02 - BETA (Mô hình Show)**: Bề mặt xoáy chất lỏng Cyan (`#64dfdf` / `#e0fbfc`), lõi hố đen xanh thẫm (`#3a0ca3`), đĩa bồi tụ phát sáng.
  - **03 - DOME 360° (Không gian vòm)**: Bề mặt Tím Magenta (`#c77dff`), bao bọc bên ngoài bởi **Khung lưới vòm cầu Geodesic 3D** (`IcosahedronGeometry` wireframe phát sáng).
  - **04 - DELTA (Đại sứ DOMI)**: Bề mặt Băng Tinh Thể Hồng/Tím (`#ff47b6` / `#ffd6ff`), các vệ tinh ánh sáng quay quanh quỹ đạo.
  - **05 - OMEGA (Marketing & Liên hệ)**: Bề mặt Hoàng Kim (`#fbbf24` / `#fff3b0`), bao quanh bởi **Hệ vành đai Sao Thổ 3D** nghiêng đa lớp.
- **FR-003 (Venom Symbiote GLSL Shader)**:
  - Kết hợp toạ độ 3D chuẩn hoá của đỉnh với hàm nhiễu 3D (*3D Simplex Noise* đa tầng - FBM) để tạo mặt nạ lan truyền dạng xúc tu (`venomMask`).
  - Tạo phần viền phát sáng (`seamGlow`) và độ phồng bề mặt (`seamDisplacement`) tại vùng giao thoa giữa hành tinh gốc và lớp Venom đang bò tới.
- **FR-004 (Scroll/Swipe Morph Controller)**:
  - Hỗ trợ lăn chuột liên tục để điều khiển tiến trình biến hình giữa các hành tinh liền kề, có lực hút mềm (smooth snap) về trạng thái hoàn chỉnh khi người dùng ngừng lăn chuột.

---

## 3. Success Criteria & Edge Cases

- **SC-001**: Duy trì tốc độ khung hình ổn định (30–60 FPS) nhờ tạo hình hoàn toàn bằng Procedural Geometry + GPU Shader, không phải tải file `.glb` nặng hàng chục MB.
- **SC-002**: Người dùng có thể đi một mạch từ Thiên hà 01 đến Thiên hà 05 chỉ bằng thao tác lăn chuột/vuốt mà không cần bấm nút chuyển trang.
- **Edge Case 1 (Cuộn trong bảng chữ vs. Cuộn ngoài 3D)**: Phân tách rõ vùng sự kiện `wheel` trên `#interiorConsole` và vùng `#webgl` để không làm mất khả năng đọc nội dung dài của từng chương.
- **Edge Case 2 (Ranh giới đầu/cuối)**: ✅ Chốt 13/11: đi vòng khép kín (`05 → 01` khi lăn xuống, `01 → 05` khi lăn lên).

---

## 4. Quyết định đã chốt với người dùng (cập nhật 13/11)

- **Venom**: tới = trào từ dưới lên, lùi = trùm từ trên xuống; mép lượn sóng mượt + viền neon mảnh (phong cách 2), không gờ cao; thời lượng 0.8s (đã duyệt); viền sáng và tinh vân nền giữ nguyên như hiện tại.
- **Bấm chuyển thiên hà khi đang ở trong** = biến hình tại chỗ; chỉ khi thoát ra mới về bản đồ 5 hành tinh.
- **Lăn chuột / vuốt (thay FR-004 bản đầu)**: KHÔNG bám theo tay. Mỗi cú lăn / vuốt = sang đúng 1 thiên hà, chạy xong hiệu ứng mới nhận cú tiếp theo. Quán tính trackpad được gộp vào cùng một cú lăn (các sự kiện cách nhau < 200ms). Thêm phím ↑ ↓ và 2 nút trên màn hình.
- **Trong bảng chi tiết** (thay US3 kịch bản 2): lăn / vuốt chỉ để cuộn đọc chữ, KHÔNG BAO GIỜ chuyển thiên hà, kể cả khi đã cuộn chạm đáy. Chỉ lăn ở bên ngoài bảng mới chuyển.
- **Hình đi kèm**: icon nét mảnh tối giản 1A micro (01), 2A ba vòng tròn (02), 3A vòm lưới (03), 5A hộp quà (05); 04 giữ DOMI.
- **Hiệu năng**: phải chạy được trên máy yếu → tự hạ độ phân giải khi đo thấy chậm.
- **Cỡ hành tinh khi bay vào**: đã vừa mắt. **DOMI ở 04**: giữ nguyên (đang chờ chọn ý tưởng nâng cấp).
- **Hình đi kèm (companion) cho 01, 02, 03, 05** giống cách DOMI đứng cạnh hành tinh 04: đang chờ người dùng chọn.

