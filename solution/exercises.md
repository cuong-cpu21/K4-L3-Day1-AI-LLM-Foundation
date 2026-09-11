# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
Khi tăng temperature từ 0.0 lên 1.5, câu trả lời thay đổi đa dạng hơn. Mức thấp
cho câu trả lời ổn định và dễ đoán, còn mức cao sáng tạo hơn nhưng đôi khi không
nhất quán bằng.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Tôi sẽ đặt temperature khoảng 0.2 đến 0.4. Chatbot hỗ trợ khách hàng cần trả lời
ổn định, rõ ràng và đúng chính sách hơn là quá sáng tạo.


### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
Theo bảng giá của lab, GPT-4o đắt khoảng 16.7 lần so với GPT-4o-mini. Với workload
này, tổng output khoảng 10.5 triệu token, tương đương khoảng 105 USD cho GPT-4o
và 6.3 USD cho mini. Tôi chọn GPT-4o cho câu hỏi phức tạp, còn mini cho những
câu hỏi đơn giản và có nhiều người dùng.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Hai câu trả lời có cách diễn đạt rất khác nhau. Vai trò giáo viên tiểu học khiến
model dùng từ đơn giản, giải thích ngắn và đưa ví dụ dễ hiểu. Vai trò chuyên gia
tài chính khiến câu trả lời dài hơn và có nhiều thuật ngữ hơn. Như vậy system
prompt ảnh hưởng trực tiếp đến giọng điệu, từ vựng và mức độ chi tiết.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Với đoạn tiếng Việt khoảng 100 từ, cách ước lượng số từ chia 0.75 cho khoảng 133
token, còn tiktoken thường cho con số cao hơn vài chục phần trăm. Nguyên nhân là
tiếng Việt có dấu và cách mã hóa Unicode có thể tách một từ thành nhiều token hơn
so với tiếng Anh.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming hữu ích khi câu trả lời dài vì người dùng có thể đọc ngay từng phần,
thay vì phải chờ toàn bộ kết quả. Nó phù hợp với chatbot và trợ lý lập trình.
Non-streaming phù hợp hơn với câu trả lời ngắn hoặc khi chương trình cần nhận đủ
kết quả rồi mới xử lý tiếp.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Exponential backoff cho các client chờ lâu dần giữa các lần thử lại, ví dụ 0.1,
0.2 rồi 0.4 giây. Nhờ vậy server có thời gian phục hồi. Nếu hàng nghìn client
cùng chờ đúng một giây rồi retry, chúng sẽ gửi request dồn vào cùng một lúc và
làm server quá tải hơn.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Persona tôi chọn là “Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn
bằng tiếng Việt”. Từ “thân thiện” giúp câu trả lời gần gũi hơn. Cụm “ngắn gọn
bằng tiếng Việt” giúp tôi dễ đọc và không bị trả lời quá dài.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất là trợ lý chỉ nhớ ba lượt gần nhất nên có thể quên thông tin cũ.
Tôi sẽ cải thiện bằng cách cho model tóm tắt các lượt trước rồi lưu bản tóm tắt
đó cùng history mới. Như vậy trợ lý nhớ được nhiều hơn mà không phải gửi quá
nhiều token.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
