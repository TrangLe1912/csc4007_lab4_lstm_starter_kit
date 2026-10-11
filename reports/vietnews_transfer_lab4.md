# VietNewsSense — Long-Context Transfer Check (Lab 4)

> Hoạt động bổ sung, KHÔNG huấn luyện LSTM/GRU trên VietNewsSense. Đọc hướng dẫn tại docs/VIETNEWS_PART_B.md.

## 1. Thông tin và cấu hình

- Họ tên / MSSV / lớp:
- Link repo Lab 4:
- Dataset/file nguồn:
- Schema đã xác nhận (text_col, label_col):
- Seed:
- max_rows:
- vocab_size:
- Tokenizer:
- max_len: **128 và 256**
- Repo/commit Lab 3 dùng để sinh audit:

## 2. Kết quả sequence audit thực tế

| Tokenizer | max_len | Test length p95 | Test UNK rate | Test truncation rate | Test avg. pad ratio |
|---|---:|---:|---:|---:|---:|
| underthesea | 128 | | | | |
| underthesea | 256 | | | | |
| whitespace (đối chứng) | 128 | | | | |
| whitespace (đối chứng) | 256 | | | | |

**Nguồn các con số:** [liệt kê đường dẫn transfer_comparison.csv hoặc vietnews_length_comparison.csv]

**Nhận xét ngắn:** [tăng max_len làm truncation/padding thay đổi ra sao?]

**Kiểm tra:** p95 và UNK rate trong script Lab 3 đang tính trên toàn bộ token gốc; có bất biến khi chỉ thay max_len không?

## 3. Phân tích ba bài báo dài (dữ liệu thật)

Sử dụng tokenizer underthesea; đánh số vị trí token trên bản đã tokenize.

| ID / row index | Chủ đề gốc | Tổng token | Bằng chứng tại A (1–128), B (129–256), C (257+) | Ảnh hưởng của 128 so với 256 |
|---|---|---:|---|---|
| | | | | |
| | | | | |
| | | | | |

Không đưa toàn bộ bài báo vào repo. Chỉ ghi ID/nguồn và trích đoạn thật cần thiết.

## 4. Giải thích kỹ thuật (150–200 từ)

- Chọn 128 hay 256 token cho thí nghiệm tiếp theo? Nêu tối thiểu 2 số liệu.
- Phân biệt truncation với vấn đề long-range dependency của RNN.
- Tăng max_len đánh đổi với padding và chi phí tính toán như thế nào?
- Nếu cần so sánh RNN, LSTM, GRU trên VietNewsSense, nên giữ cố định những điều kiện gì?

[Viết nhận xét dựa trên số liệu và ví dụ thật.]

## 5. Minh chứng và giới hạn kết luận

- Kết quả cấu hình 128:
- Kết quả cấu hình 256:
- Bảng tổng hợp:
- ID/nguồn ba bài báo:
- Những điều chưa thể kết luận nếu chưa huấn luyện mô hình VietNewsSense:

## 6. Tự kiểm tra

- [ ] Hai lần chạy giữ nguyên dataset, split, seed, vocab_size và tokenizer.
- [ ] Điền bảng bằng số liệu thật, không tự ước lượng.
- [ ] Phân tích 3 bài báo có ID hoặc row index truy vết được.
- [ ] Không khẳng định hiệu quả LSTM/GRU nếu chưa có phép đo trên VietNewsSense.
- [ ] Không commit corpus khi chưa có quyền phân phối.
