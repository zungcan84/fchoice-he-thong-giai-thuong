# FChoice 2026 – Section "Hệ thống giải thưởng"

Bản bàn giao của section Hệ thống giải thưởng (thiết kế Figma *FChoice Awards 2026*). Toàn bộ là HTML/CSS/JS thuần trong một file, không cần build.

Trang demo gồm 3 phần:
1. **Thanh nav** (`.site-head`): chỉ để minh hoạ cách header đổi màu trắng khi nằm trên section và ẩn/hiện khi cuộn.
2. **Section trống** (`.blank`): khoảng đệm để thấy hiệu ứng section xuất hiện khi cuộn tới. Khi tích hợp thì bỏ đi.
3. **Section Hệ thống giải thưởng** (`.hm-head` + `#track`): phần chính cần tích hợp.

## Chạy thử

Cần một máy chủ tĩnh, vì mở file trực tiếp (`file://`) thì trình duyệt chặn module JS:

```
python -m http.server 8000
```

Sau đó mở http://localhost:8000. Cần Internet để tải three.js.

## Cấu trúc

```
index.html                 toàn bộ CSS, markup và JS
assets/fonts/              SVN-Apparat (Regular, Medium, Bold), SVN-Kallisto (Medium)
assets/icons/cat-01…25.svg icon 25 hạng mục (Figma "FChoice 2026 – Icon hạng mục")
assets/home/               logo + icon của thanh nav
assets/icon-*.svg          icon (i) của "Tiêu chí đề cử", nút đóng và mũi tên của popup
```

Trong `index.html` có 4 khối `<script>`, theo thứ tự:

| Khối | Nội dung |
|---|---|
| Script chính | Dữ liệu hạng mục, cuộn và chuyển hạng mục, hiệu ứng chữ, huy hiệu 3D (CSS), popup tiêu chí, popup danh sách hạng mục của nhóm, các thông số 3D (`SETTINGS`) |
| Header | Thanh nav đổi màu, ẩn/hiện khi cuộn |
| `window.LAND` | Đường bờ biển thế giới (Natural Earth 1:50m, public domain) để in nổi bản đồ lên mặt cầu |
| `type="module"` | Vòng tròn, cột và mặt cầu bằng **three.js 0.165.0** (tải từ cdn.jsdelivr.net qua importmap). Muốn tự host thì tải 2 file trong importmap về và đổi đường dẫn |

## Dữ liệu

Nguồn: Google Sheet, tab **"Hạng mục chốt đợt 1"**. Dữ liệu đang được chép cứng trong script chính:

- `GROUPS`: 5 nhóm hạng mục (cột "Nhóm hạng mục"), cũng là 5 tab ở dưới.
- `CATS`: 25 hạng mục. Mỗi hạng mục có:
  - `g`: số thứ tự nhóm, bắt đầu từ 0;
  - `t`: tên hạng mục;
  - `d`: mô tả ngắn;
  - `c`: mảng các tiêu chí đề cử.
- `ICONS`: ánh xạ **tên hạng mục** sang số file icon `cat-NN.svg`. Thêm hoặc đổi tên hạng mục thì cập nhật cả ở đây. Một hạng mục cũng có thể tự khai báo `icon: 'assets/…svg'`.

Khi nối API, chỉ cần tạo `GROUPS` và `CATS` đúng cấu trúc trên trước khi script chính chạy.

## Hoạt động

- **Cuộn:** section dính lại (sticky) trong lúc cuộn qua 25 hạng mục, mỗi hạng mục chiếm một đoạn 70vh (`#hmSpacer`).
- **Huy hiệu 3D:**
  - Huy hiệu giữa là hạng mục đang xem, hai bên là hạng mục trước và sau.
  - Mỗi lần đổi hạng mục, cả 3 huy hiệu lật 1 vòng quanh trục dọc, cùng nhịp với cột trên vòng.
  - Huy hiệu dựng bằng CSS 3D (`.medal`, `.coin`, `.face`).
- **Chữ:**
  - Lần đầu section xuất hiện: hiện từng ký tự.
  - Khi đổi hạng mục: từng dòng trượt lên.
  - Tên hai hạng mục bên cạnh chạy ra từ icon, cùng thời lượng với khối chữ giữa.
- **5 tab nhóm:**
  - Bấm vào tab: nhảy tới hạng mục đầu của nhóm.
  - Desktop: hover để mở danh sách hạng mục trong nhóm.
  - Mobile: giữ tab khoảng 0.45 giây để mở danh sách.
  - Chọn một hạng mục trong danh sách thì nhảy tới hạng mục đó.
- **"Tiêu chí đề cử":** mở popup gồm icon, tên và tiêu chí của hạng mục đang xem.
- **Vòng 3D:** bấm vào cột nào thì nhảy tới hạng mục của cột đó. Ánh sáng nghiêng nhẹ theo chuột.
- **"Bỏ qua":** cuộn qua hết section.

### Cần dev nối tiếp

- **"Toàn bộ hạng mục"** (`#allBtn`): chưa gắn hành động.
- **"Gửi đề cử"** trong popup (`#critSend`): đang để `href="#"`, bấm thì chỉ đóng popup. Đổi `href` thành trang hoặc form đề cử là chạy.
- **Các link trên thanh nav:** đang để tạm.

## Thông số 3D

Khối `SETTINGS` (tìm `var SETTINGS`) chứa toàn bộ thông số chất liệu, ánh sáng, cột, vòng và mặt cầu, đã chốt với design (bộ "Bộ 4"). Mỗi nhóm thông số đều có chú thích ngắn trong code. Hai thông số hay chỉnh nhất:

- `ringY` / `ringS`: độ cao và cỡ vòng trên desktop.
- `ringYm` / `ringSm`: độ cao và cỡ vòng trên điện thoại (≤767px).

Huy hiệu giữa luôn cách đỉnh cột active tối thiểu 20px. Vòng tự hạ xuống khi cần, và chỉ tính lại khi đổi kích thước màn hình, nên không nhấp nhô khi cuộn.

## Responsive

| Mốc | Ghi chú |
|---|---|
| < 1440 (base 1280) | tiêu đề 28px |
| ≥ 1440 | theo khung Figma 1440: tiêu đề 40px, huy hiệu 120px |
| ≥ 1921 | chữ dùng size của mốc 2560: tiêu đề 60px, mô tả 18px, tên hạng mục bên cạnh 20px |
| ≥ 2560 | khoảng cách và huy hiệu của khung 2560 |
| ≤ 960 | ẩn menu nav |
| ≤ 767 | bố cục mobile co giãn theo khung 390×844 (`--mu`): huy hiệu 80/60px, hai bên chỉ còn icon, 2 nút "Bỏ qua" / "Toàn bộ hạng mục" dưới hàng tab, vòng to và cao hơn |

Có hỗ trợ `prefers-reduced-motion`: tắt hiệu ứng chữ và hiệu ứng lật.

## Trình duyệt

Cần WebGL2 (Chrome, Edge, Safari, Firefox bản mới). Nếu không có WebGL, trang hiện một vòng tròn CSS tĩnh (`.fallback`) thay cho vòng 3D.

Font SVN-Apparat và SVN-Kallisto là font bản quyền: cần kiểm tra giấy phép trước khi đưa lên production.
