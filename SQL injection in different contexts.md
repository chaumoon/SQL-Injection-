1. Lý thuyết<br>
- ko phải chỉ có chuỗi truy vấn mới được tiêm vào, bạn có thể dùng bất kì đầu vào có thể kiểm soát nào được app xử lý như truy vấn SQL để tiêm vào
- các định dạng input khác nhau có các cách khác nhau để xáo trộn cuộc tấn công bị chặn bởi WAFs hay các kỹ thuật ohongf thủ khác
- triển khai đơn giản chỉ tìm từ khóa SQL phổ biến trong yêu cầu nên có thể vượt qua bằng cách mã hóa hay thát các kí tự từ khóa bị cấm
- VD: tiêm SQL dựa trên XML xài chuỗi thoát XML để mã hóa kí tự S<br>
```
<stockCheck>
    <productId>123</productId>
    <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>
</stockCheck>
```
- nó đc giải mã ở máy chủ trước khi chuyển tới trình thông dịch SQL<br>

2. Lab: SQL injection with filter bypass via XML encoding<br>
- https://0a0800bb035dd257802fb27700b700d5.web-security-academy.net/<br>

This lab contains a SQL injection vulnerability in its stock check feature. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables.

The database contains a users table, which contains the usernames and passwords of registered users. To solve the lab, perform a SQL injection attack to retrieve the admin user's credentials, then log in to their account.

 Hint
A web application firewall (WAF) will block requests that contain obvious signs of a SQL injection attack. You'll need to find a way to obfuscate your malicious query to bypass this filter. We recommend using the Hackvertor extension to do this.<br>

3. Giải<br>
- B1: down Hackvertor để chuyển đổi kí tự: https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100
- B2: xác định có lỗ hổng ko<br>

+ thay đổi đầu vào -> trả về kết quả khác nhau<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/2a98d28c-00ac-439e-ba25-36443ab47643)<br>

+ test thử lệnh -> bị phát hiện<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/f50ab444-f90b-442a-8890-bf5e14f3f8fc)<br>

- B3: vượt ngăn chặn -> mã hóa<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/33524e28-7fca-4b28-9718-b677831cc391)<br>

- B4: gửi truy vấn<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/2d9ebe95-b3e5-49f5-adf1-33ce1d3fe2b7)<br>



