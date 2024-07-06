1. Lý thuyết<br>
- tiêm SQL bậc 1 xảy ra khi app xử lý user input từ yêu cầu HTTP và kết hợp input vào truy vấn SQL theo cách ko an toàn
- tiêm SQL bậc 2 là app lấy user input từ yêu cầu HTTP và lưu trữ nó để xài trong tương lai:<br>
+ thực hiện bằng cách đặt input vào database và ko có vul nào tại điểm lưu trữ data
+ sau đó khi xử lý yêu cầu HTTP khác, app sẽ lấy data đã lưu trữ và kết hợp vào truy vấn SQL theo cách ko an toàn
+ -> second-order SQL injection còn đc biết là stored SQL injection
-  xảy ra trong tình huống các nhà phát triển nhận thức đc vul SQL và xử lý an toàn vị trí đầu vào của input trong database 
-  data đc xử lý sau đó ddc xem là an toàn vì nó đã đc lưu trong database 1 cách an toàn
-  -> nó đc xử lý theo cách ko an toàn vì nhà phát triển nhầm tưởng là nó đáng tin 
