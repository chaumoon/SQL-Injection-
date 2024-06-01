I. Lý thuyết

II. Lab: SQL injection vulnerability allowing login bypass<br>
This lab contains a SQL injection vulnerability in the login function.
To solve the lab, perform a SQL injection attack that logs in to the application as the administrator user.
https://portswigger.net/academy/labs/launch/befc0f5ddbc0e32a7adc4c9d4d286582f462f70bf32a8d5df15489d679dcd5c9?referrer=%2fweb-security%2fsql-injection%2flab-login-bypass

III. Giải
1. thử với user (') -> thông báo lỗi<br>

![image](https://github.com/bangngoc116/SQL-Injection-/assets/127403046/fe5f6027-2b71-4019-9075-d79afcf9ef5d)<br>

-> can xài SQLi<br>
2. sử dụng user: administrator -> nhập administrator'-- và password tùy ý<br>
-> login thành công
