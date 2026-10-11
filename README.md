# Ký âm một hàng cho Kalimba 17 phím, Organ, Piano

Web app biến MIDI, MP3, link YouTube, ảnh sheet hoặc dãy nốt bạn gõ thành **một hàng nốt dễ đánh**, có hợp âm phía trên, nghe thử được, in được khổ A4 và xuất Word.

## 1. Cấu trúc thư mục

```
kalimba-notation/
├── frontend/                  ← phần web (HTML + Tailwind + JS), đưa lên Vercel
│   ├── index.html             ← giao diện
│   ├── config.js              ← địa chỉ backend và khóa Supabase (công khai)
│   └── js/
│       ├── converter.js       ← BỘ XỬ LÝ LÕI: chọn giai điệu, dịch tông, chữ↔số, hợp âm, ngắt dòng
│       ├── converter.test.js  ← bài kiểm tra tự động cho converter.js
│       └── app.js             ← nối giao diện với converter: tải file, phát nhạc, Word, in, kho cộng đồng
├── backend/                   ← máy chủ Python (chỉ cần cho MP3 và YouTube), đưa lên Render
│   ├── main.py                ← FastAPI: /api/audio-to-midi và /api/youtube-to-midi
│   ├── requirements.txt       ← danh sách thư viện Python
│   └── Dockerfile             ← để Render cài sẵn ffmpeg
├── supabase/
│   └── setup.sql              ← tạo bảng và quyền cho kho nhạc cộng đồng
├── .gitignore
└── README.md
```

**Ý tưởng chính:** mọi nguồn nhạc đều đi về cùng một chỗ.
MP3 và YouTube được backend đổi thành MIDI, rồi frontend xử lý giống hệt file `.mid`. Ảnh (OCR) và văn bản thì thành chuỗi nốt. Nhờ vậy bạn chỉ cần sửa logic ở `converter.js`.

| Nguồn | Chạy ở đâu | Cần backend? |
|---|---|---|
| MIDI | trình duyệt (`@tonejs/midi`) | Không |
| Dán văn bản | trình duyệt | Không |
| Ảnh sheet (OCR) | trình duyệt (`Tesseract.js`) | Không |
| MP3 | backend (`Basic Pitch`) | Có |
| YouTube | backend (`yt-dlp` + `Basic Pitch`) | Có |

## 2. Cài công cụ (một lần)

1. **VS Code**: https://code.visualstudio.com
2. Tiện ích **Live Server** (tác giả Ritwick Dey): bấm biểu tượng Extensions bên trái VS Code, tìm "Live Server", bấm Install.
3. **Node.js** (chỉ để chạy bài kiểm tra, không bắt buộc): https://nodejs.org
4. **Python 3.10 hoặc 3.11** (chỉ khi dùng MP3/YouTube). Nên tránh bản mới hơn vì `basic-pitch` có thể chưa hỗ trợ.
5. **ffmpeg** (chỉ khi dùng MP3/YouTube ở máy bạn):
   - Windows: mở PowerShell gõ `winget install Gyan.FFmpeg`, rồi **đóng và mở lại VS Code**
   - macOS: `brew install ffmpeg`

## 3. Chạy phần web (không cần backend)

1. Mở VS Code, chọn **File → Open Folder** và mở thư mục `kalimba-notation`.
2. Chuột phải vào `frontend/index.html` → **Open with Live Server**.
3. Trình duyệt mở ra. Bạn đã dùng được ngay các tab **MIDI**, **Dán nốt**, **Ảnh sheet**. Máy cần có mạng vì thư viện tải từ CDN.

Muốn chạy bài kiểm tra cho bộ lõi, mở Terminal trong VS Code (**Terminal → New Terminal**):

```bash
cd frontend
node js/converter.test.js
```

Thấy dòng "Tất cả bài kiểm tra đều đạt" là ổn.

## 4. Chạy backend (cho MP3 và YouTube)

Mở Terminal mới trong VS Code:

**Windows (PowerShell)**
```powershell
cd backend
py -3.11 -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

**macOS / Linux**
```bash
cd backend
python3.11 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

- Cài lần đầu khá lâu và nặng (Basic Pitch kéo theo thư viện AI).
- Mở http://localhost:8000/docs để thử API. `config.js` đã đặt sẵn `API_BASE: 'http://localhost:8000'`, nên bạn quay lại trang web là dùng được tab MP3 và YouTube.
- Nếu Windows báo không cho chạy `Activate.ps1`: chạy `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` một lần rồi thử lại.

## 5. Kho nhạc cộng đồng (Supabase, miễn phí)

1. Tạo tài khoản và một project tại https://supabase.com.
2. Vào **SQL Editor → New query**, dán toàn bộ `supabase/setup.sql`, bấm **Run**.
3. Vào **Project Settings → API**, chép **Project URL** và khóa **anon public** vào `frontend/config.js`:
   ```js
   SUPABASE_URL: 'https://xxxx.supabase.co',
   SUPABASE_ANON_KEY: 'eyJ...',
   ```
   Khóa `anon` được phép công khai vì quyền đã bị giới hạn bởi `setup.sql`. **Không bao giờ** dán khóa `service_role`.
4. Thử: tải một file MIDI lên. Vào **Table Editor → songs**, bài sẽ nằm đó với `approved = false`.
5. Muốn bài hiện trong kho công khai: bật `approved = true` cho bài đó.

Vì sao bài mới cần duyệt: ai cũng gửi bài được, nên nếu hiện công khai ngay thì kho dễ bị spam và dễ chứa nhạc vi phạm bản quyền. Giao diện có ô tích "Chia sẻ vào kho cộng đồng" hiển thị rõ để người dùng biết và tự quyết định.

Nếu muốn dùng Firebase thay Supabase, bạn phải viết lại hai hàm `shareSong` và `loadCommunity` trong `app.js` (phần còn lại không đổi).

## 6. Đưa lên mạng miễn phí

### 6.1. Đưa mã lên GitHub
1. Tạo tài khoản https://github.com và một repository mới (ví dụ `kalimba-notation`).
2. Trong VS Code, mở **Source Control** (biểu tượng nhánh) → **Initialize Repository** → nhập thông điệp → **Commit** → **Publish Branch**.

### 6.2. Backend lên Render
1. https://render.com → **New → Web Service** → chọn repository vừa tạo.
2. **Root Directory**: `backend`. **Runtime / Language**: `Docker`. **Instance Type**: Free.
3. Mục Environment, thêm biến `ALLOWED_ORIGINS` = địa chỉ Vercel của bạn (làm bước 6.3 xong quay lại điền, ví dụ `https://ky-am.vercel.app`).
4. Bấm **Create Web Service**, chờ build xong sẽ có địa chỉ dạng `https://ten-app.onrender.com`. Mở địa chỉ đó, thấy `{"ok":true,...}` là chạy.

### 6.3. Frontend lên Vercel
1. Sửa `frontend/config.js`: đặt `API_BASE` thành địa chỉ Render ở trên. Commit và push lại.
2. https://vercel.com → **Add New → Project** → chọn repository.
3. **Root Directory**: `frontend`. **Framework Preset**: Other. Để trống Build Command. Bấm **Deploy**.
4. Quay lại Render, điền `ALLOWED_ORIGINS` bằng địa chỉ Vercel vừa có rồi lưu.

Từ đó mỗi lần bạn push mã mới lên GitHub, Vercel và Render tự cập nhật.

## 7. Những giới hạn bạn nên biết trước

- **MP3 và YouTube cho kết quả "gần đúng", không phải 100%.** Basic Pitch đoán nốt từ âm thanh, nên bài nhiều nhạc cụ hoặc còn tiếng hát sẽ ra nốt thừa hoặc sai. Dùng bản piano, nhạc không lời hoặc bản đã tách giọng sẽ tốt hơn nhiều. Chỉ file MIDI mới đọc chính xác.
- **Nhịp của MP3/YouTube chỉ là ước chừng**: MIDI do AI tạo mặc định 120 BPM, chưa dò nhịp thật. Tên nốt thường đúng hơn độ dài nốt.
- **Render miễn phí rất hạn chế**: RAM khoảng 512 MB có thể không đủ để nạp Basic Pitch, và máy chủ "ngủ" sau một thời gian không dùng (lần gọi đầu chờ khá lâu). Hãy kiểm tra giới hạn hiện tại trên trang Render. Nếu bị hết bộ nhớ, bạn có thể (a) chạy backend ở máy mình khi cần dùng MP3/YouTube, hoặc (b) đưa chính `backend/Dockerfile` lên Hugging Face Spaces (loại Docker, thường nhiều RAM miễn phí hơn; nhớ đặt cổng cho khớp) hoặc dịch vụ khác.
- **YouTube hay chặn máy chủ đám mây.** Từ Render, `yt-dlp` thường gặp lỗi yêu cầu đăng nhập. Chạy backend ở máy bạn ổn định hơn. Có thể đặt biến `YT_COOKIES_FILE` trỏ đến file cookies, nhưng đó là việc nâng cao và có rủi ro cho tài khoản của bạn.
- **OCR chỉ đọc được chữ và số rõ nét** (bản in). Chữ viết tay, ảnh nghiêng, mờ, hoặc dấu chấm trên/dưới số sẽ bị sai hoặc mất. Vì vậy kết quả OCR luôn được đưa vào ô văn bản để bạn sửa trước khi chuyển đổi.
- **Ép về phím trắng**: phím đen được hạ xuống phím trắng ngay bên dưới (F# thành F). Bài nhiều nốt thăng giáng có thể nghe lệch giai điệu. Bỏ tích "Ép về phím trắng" nếu dùng Organ/Piano.
- **Hợp âm** chỉ gồm 6 hợp âm thuận của Đô trưởng (C, Dm, Em, F, G, Am), đoán theo từng ô nhịp. Đây là gợi ý để đệm đơn giản, không phải bản phối chuẩn.
- **Dải đàn**: sửa `min`/`max` trong `INSTRUMENTS` ở `converter.js` nếu đàn của bạn khác. Số MIDI: C4 = 60.

## 8. Về bản quyền và dữ liệu người dùng

- Tải nhạc từ YouTube có thể vi phạm điều khoản của YouTube và bản quyền tác giả. Hãy chỉ dùng để tự tập, và nếu mở dịch vụ công khai, hãy cân nhắc bỏ tính năng này hoặc tự chịu trách nhiệm pháp lý.
- Kho cộng đồng lưu file và chuỗi nốt do người dùng gửi. Hãy giữ khâu duyệt bài, và ghi rõ trên trang chuyện gì được lưu (ô tích chia sẻ đã làm việc này). Nếu có người dùng ở EU hoặc nơi có luật bảo vệ dữ liệu, hãy tìm hiểu thêm các quy định liên quan.

## 9. Ý tưởng mở rộng cho bạn tập code

1. Thêm hợp âm G7, Bdim, hoặc đoán hợp âm theo nửa ô nhịp (hàm `detectChordsByBar`).
2. Thêm nhạc cụ mới: chỉ cần thêm một dòng vào `INSTRUMENTS`.
3. Dò nhịp thật cho MP3/YouTube bằng thư viện `librosa` ở backend.
4. Cho phép người dùng sửa từng nốt trên màn hình rồi mới in.
