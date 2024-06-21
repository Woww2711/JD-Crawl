# JD-Crawl

## Tổng quan
Từ file data ban đầu, cào dữ liệu JD tự động từ các trang tuyển dụng của các công ty, lưu lại các thông tin JD vào từng tệp

## Dữ liệu
File gồm 1596 dòng và 12 cột, trong đó:
-	Job ID: Mã công việc
-	JD Company: Công ty tuyển việc
-	JD Title (Eng): Tên công việc bằng tiếng Anh
-	JD Level: Bằng cấp yêu cầu
-	JD YOE Min: Số năm kinh nghiệm tối thiểu
-	JD Location: Nơi tuyển công việc
-	JD Gross Salary Max*: Tổng thu nhập cao nhất của công việc
-	JD Due Date: Ngày hết hạn tuyển
-	JD Working Hour: Giờ làm việc của công việc
-	JD Note: Chú thích công việc
-	JD Details: Đường link chứa thông tin công việc
-	JD Title (Vie): Tên công việc bằng tiếng Việt

Các vấn đề của tập dữ liệu:
-	Cột “JD Title (Vie)” thiếu khá nhiều dữ liệu, do không phải công việc được tuyển dụng nào cũng từ Việt Nam, vậy nên sẽ không có tên tiếng Việt 
-	Cột “JD YOE Min” có một số dữ liệu bị thiếu (Nan) có thể do số năm kinh nghiệm tối thiểu mà nhà tuyển dụng yêu cầu chưa đến 1 năm (Ví dụ: ít nhất 6 tháng,...)
-	Cột “JD Details” có nhiều đường link không thể truy cập được do: công việc đã có người trúng tuyển nên đường link bị đóng; lỗi không thể truy cập (404, 410, v.v.)

Phương pháp xử lý:
- Do trong tập dữ liệu có những đường link được update liên tục theo thời gian thực, có một số công việc có thể đã được nhận và không còn hiện JD, có một số trường hợp không thể tải được trang (linkedin.com)
-	Xóa bỏ cột “JD Title (Vie)” vì thiếu quá nhiều dữ liệu.
-	Thay các giá trị thiếu trong cột“JD YOE Min” thành 0.
-	Sử dụng các thư viện hỗ trợ để tìm ra những link có vấn đề, đồng thời xóa đi bản ghi có chứa đường link lỗi.

Sau xử lý, tập dữ liệu còn 776 dòng và 11 cột (loại bỏ cột “JD Title (Vie)”)

## Phân tích
Số các link xuất hiện nhiều nhất:
![image](https://github.com/Woww2711/JD-Crawl/assets/120792827/2959a5aa-3120-4287-a9c5-660f4653d4dc)

-	3 trang web: www.vietnamworks.com, www.linkedin.com và www.topcv.vn xuất hiện nhiều hơn hẳn so với cá trang web tuyển dụng còn lại và số lần xuất hiện đều trên 60 lần.
-	Các trang web còn lại chỉ xuất hiện cao nhất 25 lần, thấp nhất 1 lần


Thời gian làm việc:

![image](https://github.com/Woww2711/JD-Crawl/assets/120792827/3650e85d-98a6-4285-a996-31a577e22a6e)

-	Yêu cầu làm việc Fulltime chiếm nhiều nhất, với số lượng 717 nhà tuyển dụng 
yêu cầu.
-	Tiếp theo là Fulltime Hybrid, ít hơn rất nhiều với số lượng công ty yêu cầu chỉ là 36 nhà.
-	Các kiểu giờ làm việc còn lại chiếm số lượng cực ít, do tính chất của công việc liên quan đến phân tích dữ liệu, hiếm khi có công ty sẽ tuyển người làm theo giờ hay theo ca.


## Quá trình cào
### Khó khăn
Vì tập dữ liệu chứa nhiều đường link, các đường link hầu hết đều khác nhau về cấu trúc và định dạng, nên để có thể cào dữ liệu trên chúng một cách hiệu quả, ta cần tạo ra một DataFrame có chứa các đường link gốc và thẻ class ở HTML để có thể cào dữ liệu từ trang đó. Ví dụ:
- www.topcv.vn là 1 đường link gốc, vì trong tập dữ liệu nó có các đường link con từ nó như:
  + https://www.topcv.vn/viec-lam/ai-quantitative-researcher/782374.html
  + https://www.topcv.vn/viec-lam/data-analytics-supervisor-hcm/1033042.html
-	Để có thể cào dữ liệu từ các đường link con của www.topcv.vn, ta truy cập vào 1 đường link con bất kỳ và tìm ra thẻ div class có chứa JD trong trang đó. Với trang www.topcv.vn, ta tìm được class là “job-data”. Ảnh minh họa:

![image](https://github.com/Woww2711/JD-Crawl/assets/120792827/c5cbfe9f-b99e-4d98-9d30-5f896df99a07)

- Cần làm tương tự với các đường link gốc khác nhau
  + Có một số ít đường link có JD được chia làm nhiều phần, mỗi phần ở một thẻ class khác nhau, nhưng các thẻ class chỉ khác nhau ở một số ký tự, nên ta cần dùng regex để có thể tìm được các thẻ class như vậy
-	Trong quá trình tìm kiếm, có một số ít đường link khó để cào được JD bằng cách trên nên sẽ bỏ qua (các đường link có chứa "wd3", "wd5",...)
-	Trong quá trình xử lý dữ liệu, ta sẽ loại bỏ một số đường link bắt phải đăng nhập hoặc không thể xem được HTML như facebook và các dịch vụ của google (docs, drive).
-	Ta cần lưu trữ lại tập dữ liệu sau mỗi lần xử lý để tiện cho việc cào dữ liệu về sau.
Đây là bước khó khăn và thủ công nhất của bài toán, vì để có thể hoàn thành được sẽ tốn rất nhiều thời gian, tùy thuộc vào độ lớn của tập dữ liệu đã cho.

### Kết quả
Sau khi đã hoàn thành DataFrame có chứa link gốc và các thẻ class, tập dữ liệu class.csv này sẽ bao gồm 208 dòng và 2 cột:
- Links: Chứa link gốc
- Class: Chứa tên của thẻ class
Tiếp theo cần tạo các hàm để tự động hóa công việc cào dữ liệu

333 file text JD, số lượng nhỏ hơn nhiều so với tập dữ liệu do:
-	Trang tuyển dụng đã đóng, có thể truy cập được nhưng không có dữ liệu để cào
-	Có một số trang mặc dù truy cập được nhưng không cào được thông tin (VD: những trang có chứa “wd3”, “wd5” trong đường link)
-	Có những trang status code khác 200, nên sẽ không cào được
-	Có một số trang có thể truy cập được nếu bật VPN (1.1.1.1, Proton VPN, v.v.), bằng cách này có thể cào thêm được một số đường link
-	Sau khi chạy xong hết chương trình thì ta sẽ thấy rằng khi lưu trữ ở trong dataframe, regex sẽ biến thành string, cho nên cách dùng regex ở thẻ class không áp dụng được trong bài.
-	Trong các file text cào được vẫn sẽ tồn tại một số file gần như không lấy được dữ liệu hoặc là sai format nhiều.

## Tổng kết
-	Thời gian chạy chương trình: 5662.5 giây (xấp xỉ 1 tiếng 34 phút)
-	Thu thập được các thông tin về công việc đang được tuyển dụng trên các nhiều trang tuyển dụng khác nhau.
-	Áp dụng các kỹ thuật tiền xử lý, làm sạch dữ liệu để chuẩn bị cho quá trình cào dữ liệu được thuận lợi.
-	Cào được 333/776 bản ghi có trong tập dữ liệu đã được xử lý. Số còn lại do các lí do đã nêu ở trên nên không lấy được dữ liệu
-	Quy trình cào dữ liệu có thể hoàn thiện hơn nếu áp dụng nâng cao hơn các thư viện được nêu ở chương 2 cũng như có quy trình tiền xử lý dữ liệu tốt hơn
-	Quy trình còn có bước thủ công dài để tìm thẻ class ở HTML, có thể cải thiện phần lớn chương trình nếu tự động hóa được bước này.
















