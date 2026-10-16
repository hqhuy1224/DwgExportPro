# DwgExportPro

Công cụ xuất hàng loạt bản vẽ AutoCAD (DWG/DXF) sang **PDF** hoặc **PNG** — cho phép chọn layout riêng cho từng bản vẽ, xử lý hàng loạt file trong cả thư mục và tự động chạy mà không cần giám sát liên tục.

DwgExportPro mở từng bản vẽ thông qua cơ chế tự động hóa COM của AutoCAD, tính toán phạm vi thực tế của bản vẽ (loại bỏ các đối tượng bất thường hoặc ngoại lai để tránh trường hợp block đặt sai vị trí hay đường thẳng vô hạn làm sai lệch kết quả), căn giữa vùng in và xuất sang PDF hoặc PNG. Nếu kết quả xuất bị trắng, công cụ sẽ tự động thử lại bằng các phương án dự phòng.

> Được phát triển dựa trên bộ máy xử lý hàng loạt **ScriptPro** nguyên bản do Autodesk Developer Network phát hành, sau đó được viết lại với quy trình xuất PDF/PNG chuyên biệt và lớp tự động hóa AutoCAD COM đáng tin cậy hơn. Xem mục [Ghi nhận đóng góp](#ghi-nhan-dong-gop).

---

## Tính năng

- **Xuất hàng loạt PDF/PNG** — thêm số lượng tùy ý bản vẽ DWG/DXF vào danh sách và xuất trong một lần chạy.
- **Chọn layout riêng cho từng bản vẽ** — chọn Model hoặc bất kỳ layout nào trong Paper Space cho từng file.
- **Tự động xác định vùng in thông minh** — tính toán khung bao thực tế của từng bản vẽ thay vì phụ thuộc vào thiết lập vùng in cố định `Extents` hoặc `Display`:
  - Thu thập khung bao của tất cả đối tượng hiển thị và được phép in.
  - Loại bỏ các đối tượng ngoại lai bằng bộ lọc thống kê Median + MAD (độ lệch tuyệt đối trung vị), giúp các block đặt sai vị trí hoặc kích thước ghi chú nằm quá xa không làm thu nhỏ bản vẽ chính.
  - Giữ lại các đối tượng có kích thước lớn như khung bản vẽ, khung tên ngay cả khi tâm của chúng nằm lệch khỏi cụm đối tượng chính.
  - Thêm lề nhỏ và căn giữa bản vẽ trong vùng in.
- **Tự động chuyển sang phương án dự phòng** — nếu kết quả xuất bị trắng (được kiểm tra bằng tỷ lệ điểm ảnh có nội dung đối với PNG), công cụ lần lượt thử lại với `Extents`, sau đó `Display` trước khi xác định thất bại.
- **Tự động hóa AutoCAD đáng tin cậy hơn** — chạy trên một luồng STA chuyên biệt và đăng ký COM Message Filter, hạn chế các lỗi `RPC_E_SERVERCALL_RETRYLATER` và lỗi ứng dụng đang bận trong quá trình xử lý hàng loạt kéo dài.
- **Xác minh kết quả xuất qua file thực tế** — chỉ đánh dấu bản vẽ là `Done` khi file đầu ra thực sự tồn tại và có dữ liệu, thay vì chỉ dựa vào việc AutoCAD kết thúc mà không phát sinh ngoại lệ.
- **Tự kết nối hoặc khởi chạy AutoCAD** — tái sử dụng phiên AutoCAD đang chạy nếu có và giữ nguyên phiên đó sau khi hoàn tất; nếu chưa có, công cụ tự khởi chạy và quản lý phiên AutoCAD riêng để xử lý tự động.
- **Tự động khởi động lại AutoCAD** sau mỗi N bản vẽ nhằm kiểm soát mức sử dụng bộ nhớ khi xử lý các lô file lớn.
- **Lưu và tái sử dụng cấu hình dự án** (`.bpl`) — lưu danh sách bản vẽ cùng các thiết lập để chạy lại sau này, kể cả thông qua dòng lệnh.
- **Nhập dự án ScriptPro cũ** — đọc được các file dự án ScriptPro (`.scp`) để hỗ trợ chuyển đổi sang định dạng mới.
- **Chạy qua dòng lệnh hoặc chế độ không giám sát** — thực thi dự án đã lưu từ file batch hoặc Windows Task Scheduler, hỗ trợ tự động thoát sau khi hoàn tất.

## Yêu cầu hệ thống

- Windows 10/11, phiên bản 64-bit.
- AutoCAD 2022 trở lên (phạm vi phiên bản đã được kiểm thử chính thức; các phiên bản cũ hơn có hỗ trợ COM có thể hoạt động nhưng không được hỗ trợ chính thức).
- .NET 8.0 Desktop Runtime (x64).
- AutoCAD đã được cài đặt và có giấy phép hợp lệ trên máy chạy công cụ.

## Cài đặt

**Sử dụng bản Portable:**

Giải nén thư mục build và chạy trực tiếp `DwgExportProUI.exe` — không cần cài đặt.

## Hướng dẫn sử dụng

1. Thêm bản vẽ bằng nút **Add** (thêm file riêng lẻ) hoặc **Add From Folder** (thêm từ thư mục, có thể bao gồm các thư mục con).
2. Chọn layout cần xuất cho từng bản vẽ trong bảng danh sách, bao gồm `Model` hoặc layout tương ứng.
3. Chọn định dạng xuất **PDF** hoặc **PNG**.
4. (Tùy chọn) Mở **Options** để cấu hình thời gian chờ, chu kỳ khởi động lại AutoCAD, script chạy khi khởi động, thư mục đầu ra và các thiết lập khác.
5. Nhấn **Run → Checked** để chạy các bản vẽ được đánh dấu; hoặc chọn **Selected** / **Failed** để xử lý các nhóm tương ứng.
6. Kiểm tra nhật ký sau khi chạy. Mỗi lần thực thi sẽ tạo nhật ký tổng kết và nhật ký chi tiết của dòng lệnh AutoCAD.

### Chạy bằng dòng lệnh

```bat
DwgExportProUI.exe "C:\Projects\MyProject.bpl" run
DwgExportProUI.exe "C:\Projects\MyProject.bpl" run exit
```

Lệnh đầu tiên thực thi dự án đã lưu. Lệnh thứ hai thực thi dự án và tự động thoát khi hoàn tất.

Tính năng này hữu ích khi cần lên lịch xuất bản vẽ tự động bằng Windows Task Scheduler hoặc chạy qua file batch.

## Cơ chế xác định vùng in

Khi xuất bản vẽ từ Model Space, DwgExportPro không chỉ sử dụng `Extents` hoặc `Display` của AutoCAD. Cả hai phương pháp đều có thể bị ảnh hưởng bởi các đối tượng nằm quá xa phạm vi bản vẽ thực tế, chẳng hạn như block nhập sai vị trí hoặc điểm được đặt nhầm tại tọa độ rất lớn.

Thay vào đó, công cụ thực hiện các bước sau:

1. Duyệt qua tất cả đối tượng trong không gian bản vẽ đang hoạt động và thu thập khung bao thông qua COM. Bỏ qua các đối tượng đường thẳng vô hạn như `XLINE`/`RAY`, các layer bị ẩn, đóng băng hoặc không được phép in, cùng những khung bao không hợp lệ.
2. Tính trung vị của tâm các đối tượng và MAD (độ lệch tuyệt đối trung vị). Đây là phương pháp đo độ phân tán ít bị ảnh hưởng bởi một số ít giá trị ngoại lai cực đoan.
3. Loại bỏ các đối tượng có tâm nằm cách trung vị khoảng hơn 20 lần MAD, trừ những đối tượng có kích thước lớn, chẳng hạn như khung tên hoặc khung viền, để tránh loại nhầm các thành phần quan trọng.
4. Tạo khung bao cuối cùng từ những đối tượng được giữ lại, bổ sung một khoảng lề nhỏ và thiết lập vùng in theo Window để căn giữa bản vẽ trên trang giấy hoặc ảnh đầu ra.
5. Nếu kết quả xuất bị trắng, lần lượt chuyển sang vùng in `Extents`, sau đó `Display` và thử xuất lại.

Cơ chế này giúp kết quả xuất hàng loạt nhất quán hơn ngay cả với các bản vẽ có hình học không sạch, hạn chế nhu cầu căn chỉnh thủ công cho từng file.

## Biên dịch từ mã nguồn

Yêu cầu:

- Visual Studio 2022.
- .NET 8.0 SDK.

Mở solution trong Visual Studio, khôi phục các gói NuGet và tiến hành build.

## Cấu trúc dự án

| Project | Mô tả |
|---|---|
| `DwgExportProUI` | Ứng dụng WPF chính, chứa giao diện, điểm khởi chạy và các thành phần điều hướng. |
| `DrawingListUC` | User Control quản lý danh sách bản vẽ, quản lý dự án, tự động hóa AutoCAD COM và bộ máy xuất PDF/PNG. |

## Ghi nhận đóng góp

DwgExportPro là sản phẩm phát triển dựa trên mã nguồn **ScriptPro**, ban đầu được Autodesk Developer Network phát hành:

- **Hiện đại hóa lên .NET 8.0 (bộ máy nền tảng)** — Madhukar Moogala.
- **DwgExportPro** — quy trình xuất hàng loạt PDF/PNG, chức năng chọn layout riêng cho từng bản vẽ, cơ chế xác định vùng in có lọc đối tượng ngoại lai và các cải tiến về độ ổn định của AutoCAD COM, bao gồm luồng STA, Message Filter và xác minh trạng thái xuất thông qua file đầu ra.

Biểu tượng: [FatCow Free Icons](http://www.fatcow.com/free-icons).

## Giấy phép

Dự án này là sản phẩm phái sinh từ mã nguồn ScriptPro, ban đầu được Autodesk, Inc. phát hành theo điều khoản cho phép sử dụng, sao chép, chỉnh sửa và phân phối dưới dạng mã đối tượng, với điều kiện giữ nguyên thông báo bản quyền và tuyên bố miễn trừ trách nhiệm ban đầu.

Vui lòng xem file [LICENSE](LICENSE) để biết đầy đủ các điều khoản áp dụng cho mã nguồn ScriptPro được kế thừa và những phần đã được chỉnh sửa trong dự án này.

**Bản quyền mã nguồn gốc © 2010–2026 Autodesk, Inc.** Mã nguồn được cung cấp theo nguyên trạng, không kèm bất kỳ bảo đảm nào.

Các phần chỉnh sửa và tên DwgExportPro © 2026 tác giả dự án, được phân phối theo cùng các điều khoản nêu trên.
