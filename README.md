# Demo Control-flow Integrity
## Mô tả đoạn code
Nhìn vào đoạn code, chúng ta có một struct auth chứa password và con trỏ đến một hàm sẽ xử lý chức năng xác thực. Ở trong hàm main(), password được đọc từ stdin thông qua lệnh gọi scanf() vào pass, là một mảng ký tự 4 byte. Nếu giá trị truyền vào giống với đoạn ký tự yêu cầu (pass) thì sẽ gọi đến hàm success và ngược lại thì truyền vào hàm faulure. Do thiếu quy trình kiểm tra giá trị đầu vào ở hàm scanf() cho phép ghi đè con trỏ hàm được đặt ở dòng 33. Chúng ta có thể tận dụng điểm yếu này bằng cách đặt con trỏ thành địa chỉ hàm xác nhận quá trình đăng nhập, hàm success(). Điều này sẽ khiến luồng điểu khiển sẽ chuyển hướng và được kiểm soát khi con trỏ func được tham chiếu ở dòng 38. Đầu tiên mình sẽ biên dịch và thực thi đoạn mã trên mà không có sự hỗ trợ của CFI, cố gắng chiếm đoạt luồng thực thi để buộc đăng nhập ngay cả với mật khẩu không chính xác.

## Biên dịch
### Clang hỗ trợ CFI
$ clang -o password_cfi password.c -flto -fsanitize=cfi -fvisibility=default
### GCC hỗ trợ bảo vệ
$ gcc password.c -o password_ssd -fstack-protector-all -no-pie
