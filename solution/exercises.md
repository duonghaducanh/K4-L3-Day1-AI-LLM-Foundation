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
Ở temperature 0.0, phản hồi thường ổn định, ít biến thể và bám sát cách diễn đạt quen thuộc. Khi tăng lên 0.5, 1.0 rồi 1.5, câu trả lời thường đa dạng và sáng tạo hơn nhưng cũng có thể lan man hoặc kém nhất quán hơn; đây là xu hướng xác suất chứ không phải bảo đảm tuyệt đối.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Mình sẽ bắt đầu với temperature khoảng 0.2–0.4. Chatbot hỗ trợ khách hàng cần câu trả lời nhất quán, chính xác và ít bịa đặt; mức thấp vẫn cho phép diễn đạt tự nhiên nhưng hạn chế sự ngẫu nhiên. Sau đó có thể điều chỉnh dựa trên log và đánh giá thực tế.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
Với cùng 350 token output, theo bảng giá trong lab, GPT-4o có giá 0.010 USD/1K token còn GPT-4o-mini là 0.0006 USD/1K token, nên phần output của GPT-4o đắt khoảng 16.7 lần; số lượng 10.000 người dùng và 3 lượt/ngày làm tổng chi phí tăng theo cùng tỷ lệ. GPT-4o phù hợp với phân tích phức tạp hoặc câu trả lời cần chất lượng cao, còn mini phù hợp với phân loại ý định, FAQ đơn giản và các tác vụ khối lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Prompt dành cho giáo viên tiểu học thường tạo câu trả lời ngắn hơn, dùng từ phổ thông, phép so sánh và ví dụ gần gũi với trẻ. Prompt dành cho chuyên gia tài chính thường dẫn đến giải thích dài hơn, nhiều thuật ngữ như sổ cái phân tán, đồng thuận và mật mã học. System prompt đặt vai trò, đối tượng và giọng điệu nên định hướng cách model lựa chọn từ vựng, mức độ chi tiết và loại ví dụ. Nó không bảo đảm mọi thông tin đều đúng, vì nội dung vẫn cần được kiểm tra.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Trong thử nghiệm của mình, một đoạn khoảng 100 từ có thể được tách thành khoảng 150–200 token, trong khi ước lượng theo số từ là 100 / 0.75, tức khoảng 133 token; nếu tiktoken đếm 180 token thì chênh khoảng 35% so với ước lượng. Con số thực tế phụ thuộc nội dung và encoding. Tiếng Việt có dấu, nhiều âm tiết cách bằng khoảng trắng và cách biểu diễn Unicode khiến tokenizer có thể chia nhỏ chuỗi thành nhiều token hơn tiếng Anh cùng ý nghĩa.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming quan trọng khi phản hồi dài hoặc người dùng cần thấy kết quả sớm, chẳng hạn chatbot, trợ lý lập trình và soạn thảo nội dung; nó giảm thời gian chờ cảm nhận dù không nhất thiết giảm tổng thời gian xử lý. Non-streaming phù hợp với câu trả lời ngắn, tác vụ backend cần một kết quả hoàn chỉnh để parse, hoặc khi muốn xử lý lỗi và ghi log sau khi nhận toàn bộ response.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Exponential backoff làm các lần retry sau thưa dần, nhờ đó giảm áp lực lên dịch vụ đang quá tải và cho mạng có thời gian hồi phục. Nếu hàng nghìn client cùng chờ đúng 1 giây rồi retry, chúng sẽ tạo các đợt request đồng bộ, khiến quá tải nặng hơn và có thể tạo vòng lặp lỗi. Trong hệ thống lớn có thể kết hợp thêm jitter để tránh các client retry cùng thời điểm.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Persona mình chọn: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn, rõ ràng bằng tiếng Việt và dùng ví dụ đơn giản khi giải thích khái niệm kỹ thuật." Cụm "trợ giảng thân thiện" định hướng giọng điệu hỗ trợ thay vì phán xét. "Ngắn gọn, rõ ràng bằng tiếng Việt" giúp câu trả lời phù hợp với người học và dễ đọc trong giao diện terminal; yêu cầu ví dụ giúp biến khái niệm trừu tượng thành nội dung thực hành.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất là history chỉ giữ ba lượt nên trợ lý có thể quên mục tiêu hoặc quyết định ở đầu phiên. Một cải thiện cụ thể là tóm tắt các lượt cũ trước khi cắt history: sau mỗi vài lượt, gửi history cho một hàm summarizer, lưu bản tóm tắt với vai trò system hoặc một trường trạng thái riêng, rồi ghép tóm tắt đó với sáu message gần nhất trong request tiếp theo. Cách này giảm token nhưng vẫn giữ được thông tin dài hạn quan trọng.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
