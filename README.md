# K3 — Ngày 1: Khám Phá LLM API (9h00–13h00)

Xem hướng dẫn step-by-step ở website: https://codelabs.vlearn.dev/codelab/day1-lab-llm-api-foundation 
Đăng nhập bằng tài khoản vlearn đã được kích hoạt:
- Tên tài khoản: mail vinuni
- Mật khẩu: mã số sinh viên

## Mục Tiêu

Sau buổi lab này, bạn sẽ:
- Gọi được OpenAI Chat Completions API và hiểu các tham số sinh text quan trọng (temperature, top_p, max_tokens)
- So sánh GPT-4o và GPT-4o-mini về chất lượng, độ trễ và chi phí
- Dùng system prompt để định hình persona của model
- Đếm token bằng tiktoken và tính chi phí chính xác theo giá input/output
- Xây dựng chatbot streaming có lịch sử hội thoại và retry chịu lỗi
- Ghép tất cả thành một trợ lý CLI hoàn chỉnh (mini-project)

**Cách làm bài:** mở [LAB_GUIDE.md](LAB_GUIDE.md) và làm theo từng bước —
mỗi block có checkpoint theo giờ để bạn tự biết mình đang đúng tiến độ.

---

## Cài Đặt

### Yêu cầu
- Python 3.10+
- API key để chạy thủ công (toàn bộ kiểm thử dùng mock, không cần key) — một trong hai:
  - **OpenAI API key**, hoặc
  - **NVIDIA NIM key — miễn phí**, đăng ký ~5 phút tại [build.nvidia.com](https://build.nvidia.com):
    xem hướng dẫn từng bước ở [LAB_GUIDE.md — Phụ lục B](LAB_GUIDE.md#phụ-lục-b--lấy-api-key-miễn-phí-từ-nvidia-nim)

### Tạo môi trường ảo & cài thư viện

**macOS / Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**Windows (PowerShell):**
```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

> Nếu PowerShell chặn script, chạy một lần
> `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`,
> hoặc dùng Command Prompt với lệnh `.venv\Scripts\activate.bat`.

### Thiết lập API key qua file `.env`

Chỉ cần cho phần chạy thật — pytest không cần key.

```bash
cp .env.example .env             # Windows: copy .env.example .env
```

Mở `.env` và thay `sk-your-key-here` bằng key thật.

Code trong `template.py` đã gọi sẵn `load_dotenv()` nên key trong `.env`
được nạp tự động. File `.env` đã nằm trong `.gitignore` — **tuyệt đối không
commit hoặc chia sẻ API key**.

### Chạy demo Streamlit

```powershell
uv add -r requirements.txt
uv run streamlit run streamlit_app.py
```

Demo có giao diện chat streaming, persona tùy chỉnh, retry, thống kê token/
chi phí và bộ nhớ dài hạn. Khi hội thoại vượt quá 3 lượt gần nhất, ứng dụng
tóm tắt ngữ cảnh cũ bằng model mini và tự dùng bộ nhớ cục bộ nếu việc tóm tắt
gặp lỗi.

Xem sơ đồ và giải thích chi tiết tại
[STREAMLIT_WORKFLOW_GUIDE.md](STREAMLIT_WORKFLOW_GUIDE.md).

---

## Lịch Trình & Checkpoint

| Giờ | Hoạt động | Checkpoint |
|-----|-----------|------------|
| 9h00–10h00 | Mở đầu + setup môi trường | **CP0:** `pytest tests/ -v` chạy được (tests fail là đúng — bạn chưa code) |
| 10h00–10h40 | **Block 1** — API cơ bản: Task 1.1, 1.2, 1.3 | **CP1 (10h40):** `pytest tests/test_part1.py -v` |
| 10h40–11h20 | **Block 2** — System prompt & token: Task 2.1, 2.2, 2.3 | **CP2 (11h20):** `pytest tests/test_part2.py -v` |
| 11h20–11h30 | ☕ Giải lao | — |
| 11h30–12h10 | **Block 3** — Streaming & retry: Task 3.1, 3.2 | **CP3 (12h10):** `pytest tests/test_part3.py -v` |
| 12h10–12h50 | **Block 4** — Mini-project: `run_assistant` | **CP4 (12h50):** `pytest tests/test_part4.py -v` |
| 12h50–13h00 | Wrap-up: hoàn thiện `exercises.md`, chấm điểm, nộp bài | `python grade.py` |

Chi tiết từng bước của mỗi block: xem [LAB_GUIDE.md](LAB_GUIDE.md).

---

## Cấu Trúc Thư Mục

```
K3-Day01-LLM-API-Exploration/
├── README.md            # File này — tổng quan, lịch trình, chấm điểm
├── LAB_GUIDE.md         # Hướng dẫn chi tiết từng bước + checkpoint
├── exercises.md         # Phiếu bài tập & phản ánh (9 câu)
├── template.py          # Nơi bạn viết code — điền các TODO
├── streamlit_app.py     # Demo UI/UX cho trợ lý Block 4
├── grade.py             # Chấm điểm tự động
├── requirements.txt
└── tests/
    ├── test_part1.py    # Checkpoint 1
    ├── test_part2.py    # Checkpoint 2
    ├── test_part3.py    # Checkpoint 3
    └── test_part4.py    # Checkpoint 4 + Demo tự động
```

---

## Chạy Kiểm Thử

```bash
# Từng checkpoint
pytest tests/test_part1.py -v

# Toàn bộ
pytest tests/ -v
```

Tất cả kiểm thử dùng `unittest.mock` — **không cần API key thật**.

---

## Chấm Điểm Tự Động (100 điểm)

```bash
python grade.py
```

| Tiêu chí | Cách chấm | Điểm |
|----------|-----------|------|
| CP1 — Part 1: API cơ bản | `tests/test_part1.py` | 15 |
| CP2 — Part 2: System prompt & token | `tests/test_part2.py` | 15 |
| CP3 — Part 3: Streaming & retry | `tests/test_part3.py` | 15 |
| CP4 — Part 4: Mini-project cơ bản | `tests/test_part4.py -k Basic` | 15 |
| Demo — kịch bản hội thoại tự động | `tests/test_part4.py -k Scenario` | 15 |
| `exercises.md` — 9 câu phản ánh | Đếm số câu đã trả lời | 25 |
| **Tổng** | | **100** |

Điểm mỗi nhóm tỷ lệ với số test pass, nên **làm được đến đâu có điểm đến đó**.
Điểm exercises là điểm hoàn thành; chất lượng nội dung giảng viên có thể
điều chỉnh sau.

---

## Hướng Dẫn Nộp Bài

```bash
# 1. Tạo folder solution và copy bài làm
mkdir -p solution
cp template.py solution/solution.py
cp exercises.md solution/exercises.md

# 2. Chấm thử lần cuối (grade.py sẽ ưu tiên chấm folder solution)
python grade.py

# 3. Zip folder solution
zip -r solution.zip solution/

# 4. Đổi tên thành <mã sinh viên>_lab_1.zip và upload lên LMS
```

**Cấu trúc folder solution trước khi zip:**
```
solution/
├── solution.py      # template.py đã hoàn thiện
└── exercises.md     # 9 câu đã trả lời
```

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `pytest tests/ -v` — các checkpoint đều pass
- [ ] `python grade.py` — xem điểm, mục tiêu ≥ 75/100
- [ ] `solution/exercises.md` — cả 9 câu đã trả lời
- [ ] `solution/solution.py` — bản code cuối cùng
- [ ] Đã zip và đổi tên đúng quy định trước khi upload LMS
