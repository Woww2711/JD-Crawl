# JD-Crawl

## Overview
Từ file data ban đầu, cào dữ liệu JD tự động từ các trang tuyển dụng của các công ty, lưu lại các thông tin JD vào từng tệp

## Data
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

## EDA
Số các link xuất hiện nhiều nhất:
![image](https://github.com/Woww2711/JD-Crawl/assets/120792827/2959a5aa-3120-4287-a9c5-660f4653d4dc)
Thời gian làm việc:
![image](https://github.com/Woww2711/JD-Crawl/assets/120792827/3650e85d-98a6-4285-a996-31a577e22a6e)





















