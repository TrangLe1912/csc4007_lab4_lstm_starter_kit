# VietNewsSense Long-Context Transfer – Lab 4

- Họ tên / MSSV / lớp:
- Nguồn dữ liệu, giấy phép, schema:
- Đường dẫn manifest.json và dataset_sha256:
- Seed / max_rows / vocab_size:

## Bảng số liệu
| Tokenizer | max_len | p95 | UNK rate | Truncation rate | Padding ratio |
|---|---:|---:|---:|---:|---:|
| whitespace | 128 | | | | |
| whitespace | 256 | | | | |
| underthesea | 128 | | | | |
| underthesea | 256 | | | | |

## Ba bài báo dài (theo article_candidates.csv)
| source_csv_row | article_sha256 | Chủ đề | Tổng token | Bằng chứng A/B/C và ảnh hưởng 128/256 |
|---|---|---|---:|---|
| | | | | |
| | | | | |
| | | | | |

## Nhận xét kỹ thuật (150–200 từ)
So sánh hai max_len, lý giải UNK rate bất biến, mức mất thông tin và đánh đổi padding. Phân biệt truncation với long-range dependency. Không suy diễn kết quả phân loại khi chưa huấn luyện.

## Checklist
- [ ] Dữ liệu thật và fingerprint có thể truy vết
- [ ] Không công khai toàn văn
- [ ] Ba bài có số dòng và SHA-256
- [ ] Kết luận dựa trên số liệu thật