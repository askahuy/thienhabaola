# Atomic Tasks Breakdown: Hệ Thống 5 Hành Tinh 3D & Hiệu Ứng "Venom"

Quy tắc làm việc: **Đi từng Chặng (Phase) một. Hoàn thành mỗi Chặng sẽ chụp ảnh thực tế trên trình duyệt và hỏi ý kiến người dùng trước khi sang Chặng tiếp theo.**

---

## Chặng 1 (Phase 1): Dựng Khối Hành Tinh 3D & Phụ Kiện 3D cho 5 Thiên Hà (Chưa bật Venom)
- [x] **T1.1 [US1]**: Tạo hàm sinh 5 cấu hình vật liệu & địa hình 3D cho 5 hành tinh (Alpha Hổ Phách, Beta Xoáy Cyan, DOME 360° Tím Magenta, Delta Băng Hồng, Omega Hoàng Kim) trong [index.html](file:///Users/nguyenhuy/works/thien%20ha%20bao%20la/index.html).
- [x] **T1.2 [US1]**: Dựng 5 cấu trúc phụ 3D đặc trưng (`attachmentGroups`):
  - Thiên hà 01: Vòng la bàn quỹ đạo đôi 3D.
  - Thiên hà 02: Đĩa xoáy năng lượng Wormhole 3D.
  - Thiên hà 03: Khung lưới vòm cầu Geodesic 3D (`Ø35m`) bao quanh hành tinh.
  - Thiên hà 04: Các vệ tinh ánh sáng quay quanh quỹ đạo.
  - Thiên hà 05: Hệ vành đai Sao Thổ 3D đa lớp.
- [x] **T1.3 [US1]**: Cập nhật cơ chế kéo chuột xoay 360° (`pointermove`) để khi ở bên trong thiên hà, người dùng xoay được trực tiếp cả khối hành tinh 3D + phụ kiện + bụi sao ở mọi góc độ.
- [/] **🛑 ĐIỂM DỪNG 1 (Checkpoint 1)**: Dùng `playwright-cli` chụp ảnh 5 hành tinh 3D và hỏi ý kiến người dùng về tạo hình 3D của 5 hành tinh. *(đã chụp, đang chờ ý kiến)*
  - Quyết định đã chốt: giữ tinh vân nền; hành tinh nằm giữa phần trái màn hình (dùng `camera.setViewOffset`).
  - Phát sinh trong lúc làm: ẩn vòng ngắm `.g-reticle`, đưa nhãn xuống dưới hành tinh; sửa lỗi nhãn ẩn chặn chuột trong thiên hà; đường nối chòm sao không ghi depth.

---

## Chặng 2 (Phase 2): Viết "Venom Symbiote Shader" (Hiệu ứng bám & lan truyền trên bề mặt 3D)
- [x] **T2.1 [US2]**: Viết hàm nhiễu 3D (*3D Simplex Noise & FBM*) trong GLSL Vertex & Fragment Shader để tạo các dải xúc tu uốn lượn (`tendrils`) trên bề mặt cầu 3D.
- [x] **T2.2 [US2]**: Lập trình hiệu ứng chuyển giao `uMorph` (`0.0` → `1.0`) giữa 2 hành tinh bất kỳ:
  - Lớp vỏ của hành tinh mới bò lan ra theo hình các xúc tu bám lấy hành tinh cũ.
  - Tại mép ranh giới đang bò tới: đẩy đỉnh 3D nhô cao (`vertex displacement`) và phát sáng năng lượng (`seamGlow`).
- [x] **T2.3 [US2]**: Đồng bộ hiệu ứng mọc/co giãn của các phụ kiện 3D (khung vòm DOME, vành đai Sao Thổ...) theo nhịp lan của lớp Venom.
- [x] **🛑 ĐIỂM DỪNG 2 (Checkpoint 2)**: Chụp ảnh quá trình lớp Venom đang bò 30% - 50% - 80% trên mặt hành tinh và hỏi ý kiến người dùng để tinh chỉnh độ "ngầu" của xúc tu. *(đã duyệt 13/11: tốc độ, viền, nền giữ nguyên)*
  - Quyết định đã chốt (13/11): hướng A (tới = trào từ dưới lên, lùi = trùm từ trên xuống); phong cách 2 (mép lượn sóng mượt + viền neon mảnh, không gờ cao); bấm chuyển thiên hà khi đang ở trong = biến hình tại chỗ ~0.8s, chỉ khi thoát ra mới về bản đồ 5 hành tinh.
  - Cách làm thực tế: 2 khối cầu (cũ + mới) cùng tâm, mặt nạ bổ sung nhau bằng `discard`; lớp mới dày hơn 7% để trùm lên trên. Khi thoát ra, camera dịch sang "nhà" của hành tinh đang xem rồi mới bay ra để hành tinh không đổi đột ngột.

---

## Chặng 3 (Phase 3): Gắn Điều Khiển Lăn Chuột / Vuốt Lên Xuống (Scroll & Swipe Morphing)
- [x] **T3.1 [US3]**: Thiết lập bộ bắt sự kiện `wheel` (lăn chuột) và `touch` (vuốt màn hình) khi đang ở trong chế độ khám phá (`in-galaxy`):
  - Mỗi cú lăn ở vùng 3D (hoặc khi đã cuộn chạm đáy/đỉnh khung chữ bên phải) = sang đúng 1 thiên hà, đi vòng `05 ↔ 01`; đang biến hình thì bỏ qua.
- [-] **T3.2 [US3]**: ~~Thanh HUD tiến trình `#venom-scroll-hud`~~ — không làm: thanh menu `01…05` phía trên đã đánh dấu thiên hà đang xem. Thay bằng dòng gợi ý dưới góc phải đổi theo ngữ cảnh ("LĂN CHUỘT / VUỐT ĐỂ SANG THIÊN HÀ KẾ").
- [x] **T3.3 [US3]**: Bảng thông tin bên phải và tiêu đề bên trái đổi ngay khi bắt đầu biến hình (dùng chung `enterGalaxy`).
- [/] **🛑 ĐIỂM DỪNG 3 (Checkpoint 3)**: Hỏi ý kiến người dùng về cảm giác lăn chuột. *(đã kiểm thử tự động; chờ người dùng thử tay)*

## Chặng 3b: Hình đi kèm & DOMI (phát sinh 13/11)
- [x] Hình đi kèm cho 01, 02, 03, 05: người dùng chê ảnh brand rối → vẽ icon nét mảnh tối giản; chốt **1A micro, 2A 3 vòng tròn, 3A vòm lưới, 5A hộp quà**. Icon nằm trong nhóm phụ kiện nên co/nở theo Venom, đặt phía trên bên trái hành tinh để không đụng bảng chi tiết.
- [ ] Nâng cấp DOMI thành linh vật xuyên suốt (chờ người dùng trả lời: DOMI có đi theo sang mọi thiên hà không).

## Thay đổi theo phản hồi 13/11
- [x] Lăn / vuốt / phím ↑↓ khi đang ở **trong bảng chi tiết** chỉ cuộn chữ, không bao giờ chuyển thiên hà (bỏ cơ chế "chạm đáy thì chuyển").
- [x] Sửa vuốt trackpad Mac (cộng dồn độ vuốt trong 1 cú), thêm phím ↑ ↓ / PageUp / PageDown và 2 nút "↑ Thiên hà trước / ↓ Thiên hà kế".
- [x] Sửa nháy 1 frame hành tinh cũ khi kết thúc Venom.
- [x] Máy yếu: tự đo mỗi 90 khung hình, chậm hơn ~45fps thì hạ độ phân giải 2 nấc (pixelRatio 1 → 0.75).

---

## Chặng 4 (Phase 4): Đánh Bóng Giao Diện (Taste & Impeccable) & Nghiệm Thu Cuối
- [ ] **T4.1**: Kiểm tra hiệu năng FPS, độ tương phản và độ mượt trên cả Desktop & Mobile bằng `playwright-cli`.
- [ ] **🛑 ĐIỂM DỪNG 4 (Final Sign-off)**: Bàn giao hoàn chỉnh.

