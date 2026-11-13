# Technical Implementation Plan: 5 Hành Tinh 3D & Hiệu Ứng Biến Hình "Venom"

## 1. Technical Context
- **Engine**: Three.js (`r128`, đã nhúng sẵn trong [index.html](file:///Users/nguyenhuy/works/thien%20ha%20bao%20la/index.html#L1249)).
- **Shader Language**: GLSL (`THREE.ShaderMaterial` với Vertex Shader biến dạng địa hình 3D + Fragment Shader pha trộn chất liệu kiểu Venom).
- **Verification Tooling**: `playwright-cli` để chụp ảnh thực tế trên trình duyệt + bộ quy tắc `taste` & `impeccable`.

---

## 2. Kiến Trúc 3D & Giải Thuật "Venom Morph"

### 2.1. Cấu trúc Scene Graph mới
Thay vì tạo 5 nhóm `THREE.Sprite` 2D tĩnh như trước:
1. **Ở chế độ Toàn cảnh (`Universe Overview`)**:
   - Tại vị trí của 5 thiên hà (`GALAXY_DEFS[0..4].pos`), đặt 5 cụm hành tinh 3D (`planetOverviewGroups[0..4]`) cùng đám mây hạt xoắn ốc (`galaxyMeshes[0..4]`).
2. **Khi bay vào Khám phá (`Interior Mode`)**:
   - Khối hành tinh chủ đạo (`heroPlanetGroup`) sẽ là tâm điểm tương tác ở nửa trái màn hình, sử dụng **1 khối cầu 3D độ phân giải cao duy nhất (`heroSphereMesh`)** gắn **Venom Symbiote Shader**.
   - Đi kèm `heroSphereMesh` là 5 nhóm phụ kiện 3D (`attachmentGroups[0..4]`):
     - `[0]`: Vòng quỹ đạo la bàn đôi (Alpha).
     - `[1]`: Đĩa xoáy năng lượng Wormhole (Beta).
     - `[2]`: Vòm cầu lưới Geodesic 3D Ø35m (DOME 360°).
     - `[3]`: Chùm vệ tinh ánh sáng quỹ đạo (DOMI).
     - `[4]`: Hệ vành đai đa tầng kiểu Sao Thổ (Omega).

### 2.2. Giải thuật GLSL "Venom Symbiote Shader"
Trong Shader của khối cầu hành tinh:
- **Uniforms chính**:
  - `uTime`: Thời gian thực để các mạch năng lượng và xúc tu chuyển động âm ỉ.
  - `uFromIndex` (0..4) & `uToIndex` (0..4): Chỉ số hành tinh hiện tại và hành tinh sắp biến thành.
  - `uMorph` (`0.0` → `1.0`): Tiến trình lớp Venom đã bò được bao nhiêu phần trăm trên hành tinh.
- **Công thức tạo Xúc tu Venom (`venomMask`)**:
  - Tính hướng lan từ cực Bắc xuống cực Nam kết hợp góc phương vị: `float sweep = (vNormalObj.y * 0.5 + 0.5);`
  - Cộng thêm nhiễu đa tầng 3D (*Fractal Brownian Motion - FBM 3D Simplex Noise*):
    `float tendrils = fbm3d(vPositionObj * 2.8 + vec3(0.0, uTime * 0.25, 0.0)) * 0.48;`
  - Mặt nạ Venom: `float mask = smoothstep(uMorph - 0.08, uMorph + 0.08, sweep - tendrils);`
  - **Viền xúc tu đang bò (`seam`)**: Tại nơi `mask` nằm giữa `0.05` và `0.95`, đẩy đỉnh 3D nhô cao lên (`vertex displacement`) và cộng dải phát sáng rực rỡ (`seamGlow`) để tạo hiệu ứng ký sinh trùng Venom đang trùm lấy bề mặt!

---

## 3. File Touchpoints
- [index.html](file:///Users/nguyenhuy/works/thien%20ha%20bao%20la/index.html):
  - Bổ sung CSS cho thanh chỉ báo tiến trình lướt 5 hành tinh (`#venom-scroll-hud`) bên cạnh màn hình khi ở trong thiên hà.
  - Thay thế phần tạo `Sprite` 2D bằng hệ thống **3D Planet & Venom Shader Engine**.
  - Thêm bộ điều khiển sự kiện lăn chuột (`wheel`) và vuốt cảm ứng (`touchstart`/`touchmove`) để biến hình qua lại giữa 5 hành tinh.

