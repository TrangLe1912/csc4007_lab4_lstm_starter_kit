# CSC4007 — Lab 4, Part B: VietNewsSense Long-Context Transfer Challenge

> **Mục tiêu:** nối tiếp Lab 3 (sequence audit tiếng Việt), không thay thế bài thực hành LSTM/GRU trên IMDB và không yêu cầu huấn luyện một mô hình phân loại nhiều lớp trong Lab 4.
>
> **Nguồn tái sử dụng:** https://github.com/TrangLe1912/csc4007_lab3_starter_kit — tệp run_vietnews_sequence_audit.py.
>
> **Trạng thái đánh giá:** Đây là hoạt động thực hành bổ sung; rubric Lab 4 hiện hành giữ nguyên cho đến khi giảng viên công bố phương án phân bổ điểm mới.

## 1. Mạch bài học

- **Part A — IMDB/CineSense:** chạy Embedding + LSTM; ít nhất hai biến thể GRU, BiLSTM hoặc stacked LSTM/GRU; báo cáo validation macro-F1, test accuracy/macro-F1, ablation, learning curves và 10 mẫu lỗi.
- **Part B — VietNewsSense:** dùng kết quả kiểm toán chuỗi từ Lab 3 để so sánh max_len=128 và max_len=256, xác định mức cắt chuỗi/padding và phân tích ba bài báo dài.

Phân biệt hai hiện tượng:

1. **Truncation:** token vượt quá max_len bị loại khỏi đầu vào. LSTM/GRU không thể sử dụng trực tiếp phần văn bản đã bị loại.
2. **Long-range dependency:** token vẫn nằm trong đầu vào nhưng thông tin quan trọng xuất hiện cách xa nhau. Cơ chế gates của LSTM/GRU có thể giúp học phụ thuộc dài hạn; lợi ích thực tế phải được kiểm chứng bằng thí nghiệm mô hình.

Bài lab **không** dùng F1 trên IMDB nhị phân để so trực tiếp với F1 phân loại chủ đề của VietNewsSense.

## 2. Chuẩn bị

- Repo Lab 4 hiện tại; hoàn thành hoặc có kết quả chính của Part A.
- Clone repo Lab 3 cạnh repo Lab 4 (hoặc biết đường dẫn của Lab 3).
- Dữ liệu VietNewsSense do giảng viên cung cấp; không commit dữ liệu gốc nếu chưa được phép.
- Xác nhận tên cột của dữ liệu. Ví dụ bên dưới **giả định** CSV có cột **content** (văn bản) và **category** (nhãn), vì đây là cấu hình mặc định của script Lab 3, **không phải khẳng định về schema chính thức**.
- Môi trường Python đã cài requirements của repo Lab 3, bao gồm **underthesea** nếu chọn tokenizer này.

Lưu ý: chạy hai lệnh ở mục 3 **trong thư mục Lab 3**, không phải thư mục Lab 4. Có thể dùng đường dẫn tuyệt đối đến file CSV nếu file nằm ở nơi khác.

## 3. Chạy lại sequence audit của Lab 3

Giữ nguyên dataset, seed, max_rows, vocab_size và tokenizer; chỉ thay max_len và thư mục output.

### Cấu hình 128 token

~~~bash
python run_vietnews_sequence_audit.py \
  --data_path data/raw/vietnewssense.csv \
  --text_col content --label_col category \
  --seed 42 --max_rows 1000 --vocab_size 20000 \
  --max_len 128 --modes whitespace underthesea \
  --output_dir outputs/vietnews/lab4_len128
~~~

### Cấu hình 256 token

~~~bash
python run_vietnews_sequence_audit.py \
  --data_path data/raw/vietnewssense.csv \
  --text_col content --label_col category \
  --seed 42 --max_rows 1000 --vocab_size 20000 \
  --max_len 256 --modes whitespace underthesea \
  --output_dir outputs/vietnews/lab4_len256
~~~

Nếu tập dữ liệu có ít hơn 1000 bản ghi, điều chỉnh max_rows cho phù hợp ở **cả hai lệnh**. Nếu dữ liệu có tên cột khác, cập nhật hai tham số text_col và label_col. Hai lần chạy phải cùng split; script Lab 3 lấy mẫu và chia train/test với seed đã chỉ định.

Mỗi lần chạy sẽ tạo transfer_comparison.csv, transfer_comparison.md và token_examples.csv trong output tương ứng.

## 4. Tổng hợp trong repo Lab 4

Từ thư mục gốc **Lab 4**, chạy script tổng hợp, trỏ đến hai CSV đã tạo ở Lab 3:

~~~bash
python scripts/compare_vietnews_audits.py \
  --len128 ../csc4007_lab3_starter_kit/outputs/vietnews/lab4_len128/transfer_comparison.csv \
  --len256 ../csc4007_lab3_starter_kit/outputs/vietnews/lab4_len256/transfer_comparison.csv \
  --output_dir outputs/vietnews_lab4
~~~

Tùy cách đặt thư mục, có thể cần điều chỉnh đường dẫn. Không cần chạy mô hình LSTM/GRU trong Part B.

Script tổng hợp kiểm tra hai lần chạy có cùng số mẫu, số nhãn, kích thước vocabulary, tokenizer; kiểm tra p95 và UNK trên văn bản gốc không đổi khi chỉ thay max_len. Script **không** giả lập chỉ số và dừng nếu phát hiện hai cấu hình không thể so sánh công bằng.

Kết quả:
- outputs/vietnews_lab4/vietnews_length_comparison.csv
- outputs/vietnews_lab4/vietnews_length_comparison.md

**Lưu ý chính xác về UNK:** Trong phiên bản hiện có của Lab 3, hàm audit_sequence_texts tính **test_unk_rate trên toàn bộ token gốc trước truncation**. Do đó, khi chỉ thay max_len và mọi thứ khác giữ nguyên, UNK rate và test_len_p95 phải giữ nguyên. Truncation rate và average padding ratio có thể thay đổi.

## 5. Phân tích ba bài báo dài

Chọn ba bài báo thật từ VietNewsSense (ưu tiên bài có độ dài vượt 256 token). Ghi chỉ số dòng hoặc ID có thể truy vết. Tokenize bằng cùng tokenizer **underthesea** đã dùng khi chạy audit, rồi chia vị trí:

- **A:** token 1–128;
- **B:** token 129–256;
- **C:** token 257 trở đi.

Với từng bài báo, mô tả ngắn bằng chứng xác định chủ đề xuất hiện ở vùng nào. Cần chỉ ra cụ thể thông tin nào bị loại ở max_len=128, còn lại ở max_len=256 và thông tin nào vẫn bị loại ở cả hai giới hạn (nếu có).

Phân tích này là **đánh giá rủi ro mất thông tin**, không phải bằng chứng rằng mô hình đã phân loại sai. Không sao chép toàn bộ bài báo vào GitHub; chỉ trích đoạn ngắn cần thiết và lưu ID.

## 6. Câu hỏi và sản phẩm nộp

Sinh viên trả lời:

1. Với cùng tokenizer, truncation và padding thay đổi thế nào giữa max_len=128/256?
2. Vì sao p95 độ dài văn bản gốc và UNK rate trong script hiện tại phải bất biến?
3. Vì sao tăng max_len không đồng nghĩa LSTM sẽ đạt accuracy cao hơn?
4. LSTM/GRU giải quyết loại giới hạn nào, và giới hạn nào cần xử lý từ phía dữ liệu đầu vào?
5. Nếu muốn kiểm chứng hiệu quả LSTM/GRU trên VietNewsSense, phải thiết kế so sánh công bằng ra sao?

Điền **reports/vietnews_transfer_lab4.md** và đưa bảng số liệu có nguồn từ output vào báo cáo. Phần nhận xét kỹ thuật đề xuất dài 150–200 từ. Cần có đủ ba bài báo và giải thích giới hạn kết luận.

Có thể commit hai CSV kết quả so sánh nhỏ để tái lập; không đưa corpus nguyên bản, thông tin cá nhân hoặc checkpoint lớn vào repo.

## 7. Kiểm tra và chấm

- Công cụ so sánh được kiểm thử bằng các CSV giả lập **chỉ để thử chương trình**, không phải số liệu VietNewsSense thật.
- CI kiểm tra code và script; **không kiểm tra accuracy/F1 trên VietNewsSense** và không xác thực nội dung ba bài báo.
- Đề xuất phân bổ 10 điểm cho Part B là một **phương án cần giảng viên phê duyệt**. Không tự ý sửa rubric 100 điểm đã công bố cho lớp.

## 8. Liên kết sang Lab 5

VietNewsSense có thể trở thành bài toán fine-tuning Transformer tiếng Việt ở Lab 5, khi đó mới thực hiện đánh giá mô hình trên đúng dataset/split/metric và so với baseline tiếng Việt tương ứng.
