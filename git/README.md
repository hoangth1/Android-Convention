## Luồng quản lí Git hiệu quả trong nhóm dự án

### 1. Quản lí Git
* Nhánh `master` là nhánh source release, tất cả source đã đều được review và approve.
* Mỗi developer sẽ quản lí một nhánh của riêng mình, khi xử lí task nên checkout ra từng nhánh riêng và không nên xử lí quá nhiều task song song.

### 2. Flow sử dụng Git
#### 2.1. Khởi tạo project: 
1. Clone repository
2. Developer checkout nhánh mới riêng và thực hiện tạo base project.
3. Thực hiện `add`, `commit` và push lên github => tạo pull request, review & merge.
4. Các thành viên khác thực hiện clone repository

#### 2.2. Luồng sử dụng Git
1. Developer checkout nhánh mới riêng `branch_x` và xử lí task.
2. Thực hiện `add`, `commit`
3. Checkout nhánh `master` thực hiện `pull` source mới nhất
4. Checkout `branch_x` thực hiện `git rebase master` để tích hợp code
5. `git push` và tạo pull request review

#### 2.3. Xử lí khi gặp conflict
1. Hãy thực hiện fix lỗi conflict thủ công（những phần được bao bởi <<< và >>> ） hoặc dùng Android Studio. Trong trường hợp muốn dừng việc rebase lại, hãy dùng lệnh `git rebase --abort`.
2. Khi đã giải quyết được tất cả các conflict, tiếp tục thực hiện việc rebase bằng:
```
git add .
$ git rebase --continue
```
## 3.  Quy định về gửi pull request:

 * Khi gửi pull requset cần ghi vào description những thông tin sau:
   - Link ticket mà pull request dùng để xử lý
   - Pull này dùng để làm gì
   - Tầm ảnh hưởng của code.
 * Khi có pull request thì cần gửi lên box cho mọi người review, khi nhận được ít nhất 50% thành viên approve thì code sẽ được merge.

