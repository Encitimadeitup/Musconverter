# 🎵 Ký Âm Một Hàng — Web App Chuyển Đổi Bản Nhạc Cho Kalimba, Organ & Piano

**Ký Âm Một Hàng** là ứng dụng web miễn phí giúp biến các nguồn nhạc phức tạp (file MIDI, file MP3 tách vocal, link video YouTube, ảnh chụp bản sheet/tab nhạc, hoặc văn bản dán trực tiếp) thành **dãy nốt ký âm 1 hàng ngang đơn giản** (dạng Số Jianpu `1 2 3` hoặc Dạng Chữ `C D E`).

Ứng dụng tự động tích hợp hợp âm đệm tay trái, hỗ trợ nghe thử giai điệu trực tiếp, cho phép copy 1-touch, xuất file Word (`.docx`), in ấn chuẩn khổ A4/PDF, và chia sẻ vào kho nhạc cộng đồng.

---

## 🛠️ 1. Cấu Trúc Mã Nguồn & Công Nghệ (Tech Stack)

Dự án được thiết kế theo tư duy **All-In-One (Single-File Architecture)** tối giản cho người mới học lập trình (Newbie):

* **Frontend Engine:** HTML5, Tailwind CSS (qua CDN), Vanilla JavaScript ES6+.
* **MIDI Parsing:** Thư viện `@tonejs/midi` xử lý và trích xuất dữ liệu nốt nhạc trực tiếp trên trình duyệt.
* **OCR (Quét ảnh):** Thư viện `Tesseract.js` nhận diện ký tự chữ & số từ ảnh chụp sheet/tab.
* **Audio Synthesizer (Nghe thử):** Thư viện `Tone.js` tạo lập âm thanh tổng hợp cho Kalimba, Organ và Piano.
* **Xuất File Word:** Thư viện `docx.js` dựng và tải xuống file `.docx`.
* **Database Cộng Đồng (UGC):** `Supabase JavaScript SDK` kết nối lưu trữ kho bài hát dùng chung.

---

## 🚀 2. Các Tính Năng Cốt Lõi (Core Features)

### 📥 2.1. Đa dạng nguồn đầu vào (Multi-Input Support)
1. **File MIDI (`.mid`, `.midi`):** Phân tích chính xác 100% từng nốt nhạc, tự động lọc lấy track giai điệu chính (tay phải) bằng thuật toán *Skyline*.
2. **Dán văn bản nốt trực tiếp:** Nhập chuỗi nốt dạng Chữ (`C D E F G A B`) hoặc Số (`1 2 3 4 5 6 7`). Hỗ trợ các ký hiệu quãng cao/thấp (`1'`, `1*`, `1_`), phím thăng/giáng (`C#`, `Bb`), vạch nhịp (`|`), nốt kéo dài (`-`) và nghỉ (`0`).
3. **Quét ảnh Sheet / Tab nhạc (OCR):** Tải lên ảnh chụp bản nhạc, tự động trích xuất chuỗi ký tự sang ô chỉnh sửa.
4. **File MP3 & Link YouTube (Yêu cầu Backend Python):** Tích hợp giao diện kết nối API chuyển âm thanh/video thành dữ liệu nốt MIDI thông qua AI (`Basic Pitch` & `yt-dlp`).

### ⚙️ 2.2. Bộ xử lý lõi & Chuyển đổi thông minh (Core Converter Engine)
* **Bộ chọn nhạc cụ linh hoạt:**
  * **Kalimba 17 phím (C4–E6):** Giới hạn dải nốt chuẩn, tự động dời quãng tám để nốt rơi đúng phím đàn.
  * **Organ (C3–C6) & Piano tay phải (C4–C6):** Mở rộng quãng âm đa dạng.
* **Tự động dịch Tông (Auto-Transpose):** Đoán và đưa toàn bộ bản nhạc về cung **C Major / A Minor** để người mới chơi hoàn toàn trên các phím trắng cơ bản.
* **Ép phím trắng (Snap to White Keys):** Tự động chuyển các nốt thăng/giáng bất thường về phím trắng sát kề bên dưới.
* **Gợi ý hợp âm đệm:** Tự động phân tích ô nhịp và hiển thị hợp âm gốc (`[C]`, `[Am]`, `[F]`, `[G]...`) phía trên câu hát.
* **Hỗ trợ Lời bài hát (Lyrics Alignment):** Cho phép nhập lời kèm hợp âm (dạng Hợp Âm Chuẩn) để khớp từng dòng lời tương ứng dưới dòng nốt.

### 📤 2.3. Đầu ra, In ấn & Trải nghiệm người dùng (Output & UX)
* **Chế độ hiển thị 2 chiều:** Chuyển đổi qua lại linh hoạt giữa **Ký âm Số (Jianpu)** và **Ký âm Chữ (Letter)**.
* **Phát âm thanh (Audio Playback):** Nghe thử giai điệu trực tiếp với âm thanh giả lập đặc trưng của Kalimba / Organ / Piano, có hiệu ứng highlight nốt đang phát.
* **Sao chép 1-Click (Copy to Clipboard):** Chép toàn bộ dãy nốt dạng text để dán nhanh vào Zalo, Messenger, Notes.
* **Xuất file Word (`.docx`):** Tạo file tài liệu được định dạng đẹp mắt, sẵn sàng chỉnh sửa.
* **Tối ưu In ấn & Xuất PDF:** Tự động căn chỉnh lề, ẩn các công cụ điều khiển rườm rà, đưa bản nhạc về định dạng trang giấy A4 tiêu chuẩn.
* **Kho nhạc cộng đồng (UGC):** Lưu trữ và tìm kiếm các bản nhạc do người dùng đóng góp thông qua hệ thống Supabase.

---

## 💻 3. Hướng Dẫn Sử Dụng Chi Tiết (User Guide)

### 🎹 Cách tập đàn với kết quả hiển thị trên web:
1. **Bước 1: Chọn nguồn nhạc** ở cột bên trái (Ví dụ: Tải file MIDI hoặc dán đoạn nốt `[C] 1 1 5 5 | [F] 6 6 [C] 5 -`).
2. **Bước 2: Tùy chỉnh cách đánh** tại phần *Cách đánh*:
   * Chọn loại đàn bạn có (*Kalimba 17 phím* hoặc *Organ*).
   * Chọn dạng hiển thị mong muốn (*Số: 1 2 3* hoặc *Chữ: C D E*).
   * Chọn số ô nhịp hiển thị trên một dòng (mặc định là 4 ô nhịp/dòng).
3. **Bước 3: Xem kết quả & Nghe thử:**
   * Tờ nhạc sẽ hiện ra ở cột bên phải.
   * Bấm nút **Phát âm thanh** để nghe giai điệu chạy mẫu.
   * Bấm **Copy 1-Click** để lưu vào bộ nhớ tạm hoặc **In / Tải PDF** để in ra giấy A4 tập đàn.

---

## 🛠️ 4. Hướng Dẫn Chạy & Đưa Lên Mạng (Deployment Guide)

### 🔴 Cách 1: Chạy trực tiếp trên máy tính (Local Development)
1. Tải phần mềm **VS Code**.
2. Tạo một file tên là `index.html` và dán toàn bộ mã nguồn vào.
3. Nhấp đúp chuột (Double-click) vào file `index.html` đó để mở và chạy trực tiếp trên trình duyệt (Chrome/Edge/Firefox).

### 🌐 Cách 2: Đưa lên mạng miễn phí bằng GitHub Pages (Dành cho Newbie)
1. Đăng nhập vào [GitHub.com](https://github.com/) và bấm **New repository** để tạo kho chứa mới (ví dụ đặt tên là `kalimba-notation`).
2. Bấm **Upload an existing file** và kéo thả file `index.html` của bạn lên, sau đó chọn **Commit changes**.
3. Vào mục **Settings** (Cài đặt) của repository $\rightarrow$ Chọn **Pages** ở cột menu bên trái.
4. Tại mục **Branch**, chọn nhánh **`main`** (hoặc `master`) và bấm **Save**.
5. Chờ 1–2 phút, GitHub sẽ cấp cho bạn một đường link trang web công khai (dạng `https://<ten-tai-khoan>.github.io/kalimba-notation/`) để gửi cho bạn bè cùng sử dụng!

---

## 📜 5. Quy Chuẩn Viết Nốt (Text Notation Syntax)

Khi dùng ô **Dán nốt**, bạn có thể viết theo các quy tắc sau:
* **Các nốt cơ bản:** `1 2 3 4 5 6 7` hoặc `C D E F G A B`.
* **Nốt nghỉ:** `0`.
* **Nốt kéo dài:** Dấu gạch ngang `-` (ví dụ: `1 - -`).
* **Vạch nhịp:** Dấu gạch đứng `|`.
* **Hợp âm:** Đặt trong ngoặc vuông trước nốt (ví dụ: `[Am] 6` hoặc `[C] C`).
* **Ký hiệu cao độ:** 
  * Cao hơn 1 quãng tám: `1'` hoặc `1*`. Cao hơn 2 quãng tám: `1''` hoặc `1**`.
  * Thấp hơn 1 quãng tám: `1_`.
* **Dấu thăng / giáng:** `C#`, `Bb`, `#4`, `b3`.

---

## 📄 6. Giấy Phép & Bản Quyền (License & Disclaimer)

* Dự án được xây dựng vì mục đích học tập, chia sẻ cộng đồng phi thương mại.
* Hãy tôn trọng bản quyền tác giả: Chỉ chuyển đổi và chia sẻ các tác phẩm âm nhạc mà bạn có quyền sử dụng.
