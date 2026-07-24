# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Hãy thay các dòng giữ chỗ bằng câu trả lời
thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature 0.0–1.0, các phản hồi khá nhất quán: đều chọn Hang Sơn
> Đoòng và trình bày nhiều chi tiết tương tự nhau. Ở mức 1.5, phản hồi chuyển
> sang chủ đề xuất khẩu hồ tiêu và ngắn hơn, cho thấy temperature cao làm đầu
> ra đa dạng và khó đoán hơn; tuy nhiên cần chạy nhiều lần để kết luận chắc chắn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature khoảng 0.2. Mức thấp giúp câu trả lời ổn định, nhất
> quán và bám sát thông tin hỗ trợ, đồng thời vẫn giữ một chút linh hoạt để
> diễn đạt tự nhiên thay vì quá máy móc.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Workload tạo ra 10.000 × 3 × 350 = 10,5 triệu token đầu ra/ngày. Theo bảng
> giá trong bài, GPT-4o tốn khoảng 10.500 × 0,010 = 105 USD/ngày, còn
> GPT-4o-mini tốn 10.500 × 0,0006 = 6,30 USD/ngày; vì vậy GPT-4o đắt hơn
> khoảng 16,7 lần. GPT-4o xứng đáng cho yêu cầu phức tạp, nhiều ngữ cảnh hoặc
> có rủi ro cao như xử lý khiếu nại khó; mini phù hợp cho FAQ, phân loại và
> các câu hỏi hỗ trợ thường gặp có lưu lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với persona giáo viên tiểu học, phản hồi ngắn và dễ hiểu hơn, dùng hình ảnh
> “quyển sổ” và “trang giấy” để giải thích block và chain cho trẻ em. Persona
> chuyên gia tài chính trả lời dài, có cấu trúc danh sách và dùng nhiều thuật
> ngữ như DLT, sổ cái phân tán, giao dịch và hàm băm mật mã. Như vậy, dù câu
> hỏi không đổi, system prompt đã điều chỉnh đối tượng người đọc, từ vựng, độ
> sâu và cách chọn ví dụ của model.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với đoạn văn tiếng Việt 110 từ, `count_tokens` đếm được 147 token, còn công
> thức `số từ / 0.75` ước lượng 146,67 token. Kết quả thật chỉ cao hơn khoảng
> 0,23%, tức gần như trùng với ước lượng trong mẫu này. Tuy nhiên, tokenizer
> tách văn bản thành các mảnh subword chứ không đếm từ; dấu tiếng Việt và việc
> một từ ghép thường gồm nhiều âm tiết cách nhau bằng dấu cách có thể làm một
> từ bị biểu diễn bởi nhiều token hơn tiếng Anh, tùy bộ mã hóa và nội dung.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi phản hồi dài hoặc mô hình cần nhiều thời gian xử lý, chẳng hạn chatbot, trợ lý lập trình hay tạo nội dung, vì người dùng thấy kết quả xuất hiện ngay và có thể đọc hoặc dừng sớm thay vì chờ toàn bộ phản hồi. Non-streaming phù hợp hơn khi phản hồi ngắn, cần nhận một kết quả hoàn chỉnh trước khi xử lý tiếp, hoặc cần dễ dàng kiểm tra, lưu trữ và parse dữ liệu có cấu trúc như JSON.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tăng dần thời gian chờ sau mỗi lần thất bại, nhờ đó giảm nhanh lượng request retry và cho API có thời gian phục hồi khi quá tải. Với delay cố định, hàng nghìn client có thể retry đồng thời theo cùng một nhịp, tạo ra các đợt request lớn lặp lại

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona: “Bạn là trợ lý học tập AI dành cho sinh viên mới bắt đầu.
> Hãy trả lời bằng tiếng Việt, ngắn gọn, theo từng bước và đưa ví dụ thực tế
> khi gặp khái niệm khó. Nếu không đủ thông tin, hãy nói rõ thay vì tự suy
> đoán.” Cụm “sinh viên mới bắt đầu” giúp model chọn mức kiến thức và từ vựng
> phù hợp. Yêu cầu “ngắn gọn, theo từng bước” làm câu trả lời dễ theo dõi,
> đồng thời hạn chế sinh nội dung dài không cần thiết.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là trợ lý chỉ giữ 3 lượt hội thoại gần nhất, vì vậy có thể
> quên yêu cầu hoặc dữ kiện quan trọng ở đầu phiên. Tôi sẽ thêm bộ nhớ dạng
> tóm tắt: trước khi loại các message cũ, hệ thống tóm tắt chúng vào biến
> `summary`; ở những lượt sau, `summary` được chèn ngay sau system prompt cùng
> history gần nhất. Cách này giữ được ngữ cảnh dài hạn mà không phải gửi lại
> toàn bộ hội thoại, nhờ đó giảm số token và chi phí.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
