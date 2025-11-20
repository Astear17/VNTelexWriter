# VN Telex Writer

**VN Telex Writer** là một công cụ gọn nhẹ, nhanh chóng, chạy trên nền web, cho phép gõ tiếng Việt bằng phương pháp nhập Telex. Công cụ được thiết kế để **hoàn toàn responsive**, hoạt động **offline**, và có logic nâng cao để xử lý các tổ hợp nguyên âm phức tạp (như `uoiw -> ươi`) thường gặp trong cách gõ tiếng Việt hiện đại.

Dự án này được triển khai trực tiếp trên GitHub Pages tại:

➡️ [Live Demo: VN Telex Writer](https://astear17.github.io/VNTelexWriter/)

## ✨ Tính năng

- **Ứng dụng một file duy nhất**: Toàn bộ trình soạn thảo (HTML, CSS/Tailwind, và JavaScript Telex Engine) nằm gọn trong một file duy nhất, dễ dàng mang theo.
- **Telex Engine cải tiến**: Áp dụng các quy tắc nâng cao, sửa các trường hợp đặc biệt như `cuoiw` để cho ra kết quả đúng là `cười`, đồng thời xử lý các nguyên âm ghép phức tạp.
- **Chuyển đổi theo thời gian thực**: Tự động chuyển đổi đầu vào Telex sang ký tự tiếng Việt ngay khi bạn gõ trong phạm vi từ.
- **Chuyển đổi giao diện**: Hỗ trợ chế độ Sáng và Tối.
- **Lựa chọn phông chữ**: Cho phép người dùng chọn nhiều phông chữ hiện đại, dễ đọc.
- **Chức năng sao chép**: Dễ dàng sao chép văn bản đã chuyển đổi.
- **Bật/Tắt Telex**: Cho phép bật hoặc tắt bộ máy chuyển đổi ngay lập tức.

## ✍️ Cách sử dụng (Quy tắc Telex)

Trình soạn thảo sử dụng phương pháp nhập Telex tiêu chuẩn. Hệ thống dựa vào các tổ hợp phím để nhập dấu thanh và biến đổi nguyên âm (dấu mũ, dấu ă, dấu ơ, dấu ư).

### Biến đổi nguyên âm (Nguyên âm có dấu mũ/dấu ơ/dấu ă)

| Nguyên âm | Phím nhập | Ví dụ   | Kết quả |
|-----------|-----------|---------|---------|
| ă         | aw        | bawn    | băn     |
| â         | aa        | laam    | lâm     |
| ê         | ee        | teen    | tên     |
| ô         | oo        | tooi    | tôi     |
| ơ         | ow        | mow     | mơ      |
| ư         | uw hoặc w | tuw/tw  | tư      |
| đ         | dd        | ddieen  | điên    |

### Dấu thanh

Dấu thanh được gõ ở cuối từ.

| Phím | Tên dấu | Phím nhập | Ví dụ | Kết quả |
|------|---------|-----------|-------|---------|
| s    | Sắc     | s         | tas   | tá      |
| f    | Huyền   | f         | taf   | tạ      |
| r    | Hỏi     | r         | tar   | tả      |
| x    | Ngã     | x         | tax   | tã      |
| j    | Nặng    | j         | taj   | tại     |
| z    | Xóa dấu | z         | táz   | ta      |

## 💻 Phân tích kỹ thuật: Telex Engine

Chức năng cốt lõi nằm trong hàm JavaScript `convertWordToVietnamese`, sử dụng chuỗi các biểu thức chính quy và logic đặt nguyên âm ưu tiên để đảm bảo chuyển đổi chính xác.

Quá trình chuyển đổi diễn ra qua hai giai đoạn chính:

### Giai đoạn 1: Biến đổi nguyên âm và phụ âm (ă, ơ, â, ê, ô, đ)
Giai đoạn này xử lý việc biến đổi nguyên âm cơ bản và phụ âm 'd' bằng các phím `o`, `e`, và `w`. Đặc biệt, engine áp dụng logic riêng để xử lý cụm nguyên âm phức tạp trước khi thay thế đơn giản.

| Loại quy tắc               | Mẫu nhập            | Nguyên âm xuất ra | Mục đích |
|-----------------------------|---------------------|------------------|----------|
| **Nguyên âm phức tạp**      | uo([a-zA-Z]*)w      | ươ + nhóm        | Sửa lỗi `cuoiw`. Chuyển đổi chuỗi phức tạp như `uoiw` thành `ươi` (ví dụ: `tươi`). |
| **Cụm nguyên âm đặc biệt**  | uaw                 | ưa               | Xử lý đúng cụm `ua` (ví dụ: `quaw` là ngoại lệ). |
| **Nguyên âm cơ bản**        | aw, ow, uw          | ă, ơ, ư          | Chuyển đổi tiêu chuẩn cho ba nguyên âm chính. |
| **Nguyên âm có mũ/Đ**       | aa, ee, oo, dd      | â, ê, ô, đ       | Chuyển đổi tiêu chuẩn cho nguyên âm có dấu mũ và phụ âm 'đ'. |

### Giai đoạn 2: Áp dụng dấu thanh
Sau khi biến đổi nguyên âm, engine kiểm tra ký tự cuối của từ để tìm phím dấu thanh (`s`, `f`, `r`, `x`, `j`). Nếu có, hàm `applyTone` sẽ được gọi, với logic thông minh để đặt dấu:

1. **Xác định nguyên âm**: Tìm tất cả vị trí nguyên âm trong từ.
2. **Quy tắc một nguyên âm**: Nếu chỉ có một nguyên âm (ví dụ: `lam`), dấu được đặt tại đó (`làm`).
3. **Quy tắc nguyên âm phức tạp**: Với từ có hai hoặc nhiều nguyên âm (ví dụ: `hoan`, `tươi`), logic xác định nguyên âm "chính" để đặt dấu, dựa trên quy tắc âm tiết mở/đóng.
   - Ví dụ (hai nguyên âm, âm tiết đóng): Trong `hoan`, dấu thường đặt trên nguyên âm thứ hai (`a`) thành `hoán`.
4. **Áp dụng dấu**: Sử dụng CHAR_MAP (ví dụ: `aàáảãạ`) để áp dụng dấu tương ứng với phím nhập (0=Huyền, 1=Sắc, 2=Hỏi, 3=Ngã, 4=Nặng).

Cách tiếp cận nhiều lớp này đảm bảo các chuỗi phức tạp được xử lý đúng trước khi áp dụng dấu, mang lại kết quả chính xác cao.

---

## 🔧 Phát triển

Để chạy VN Telex Writer trên máy cục bộ, hãy clone repository và mở file `index.html` trong trình duyệt.

```bash
git clone https://github.com/Astear17/VNTelexWriter.git
cd VNTelexWriter
open index.html
