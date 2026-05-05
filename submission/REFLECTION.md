# Reflection — Lab 19

**Tên:** _<Hoàng Ngọc Anh>_
**Path đã chạy:** lite

---

## Câu hỏi (≤ 200 chữ)

> Trên golden set 50 queries, mode nào thắng ở loại query nào (`exact` /
> `paraphrase` / `mixed`), Và tại sao? Khi nào bạn **không** dùng hybrid
> (i.e. khi nào pure BM25 hoặc pure vector là lựa chọn đúng)?

**Mode thắng theo loại query:**

- **`exact` queries:** BM25 thắng hoặc ngang hybrid. Lý do: query chứa từ kỹ thuật verbatim trong corpus, BM25 đánh trúng exact term match → signal đã đủ mạnh, vector không cần thiết.

- **`paraphrase` queries:** Semantic/vector thắng, nhưng cả BM25 và vector đều yếu trên Vietnamese paraphrases với model `BAAI/bge-small-en-v1.5` (English-trained, 24-32% recall). BM25 gần như fail hoàn toàn vì không có term overlap. Vector thắng nhờ semantic similarity.

- **`mixed` queries (exact + paraphrase):** **Hybrid thắng rõ rệt** (~100% vs 97-98% so với pure modes). Đây là pattern production thực tế nhất — user hiếm khi viết 100% exact hoặc 100% paraphrase.

- **Trung bình:** Hybrid thắng nhờ robust trên mọi loại query.

**Khi KHÔNG dùng hybrid:**
1. **Tài nguyên cực hạn** — hybrid cần chạy cả BM25 lẫn vector search = 2x compute/latency.
2. **Query chỉ có exact keywords rõ ràng** — BM25 đã đủ, thêm vector chỉ tốn thêm mà không cải thiện đáng kể.
3. **Paraphrase queries trên embedding model yếu/multilingual** — nếu vector recall < BM25, hybrid sẽ kéo kết quả xuống thay vì lên.
4. **Streaming/realtime latency cực nghiêm ngặt (<5ms)** — hybrid overhead không đáng kể với P99 <50ms nhưng có thể là vấn đề với hard real-time.

---

## Điều ngạc nhiên nhất khi làm lab này

Embedding model English-trained (`bge-small-en-v1.5`) hoạt động kém trên Vietnamese paraphrase queries — điều này cho thấy **embedding model choice là yếu tố quyết định hơn là search algorithm**, và production cần đầu tư vào multilingual models (như `bge-m3`) thay vì chỉ tối ưu RRF formula.

---

## Bonus challenge

- [ ] Đã làm bonus (xem `bonus/`)
- [ ] Pair work với: _<tên đồng đội nếu có>_
