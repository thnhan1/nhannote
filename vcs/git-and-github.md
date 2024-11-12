# Git and GitHub

## Config&#x20;

Config some options at the first time using git

```bash
git config --global user.name="username"
git config --golbal user.email="user@gmail.com"
```

## Initialize the repository

1. Initialize local repository

```bash
git init
```

### Add file to `staging area` then commit

&#x20;Can specific single file or all with `add .`

```bash
git add .
git commit -m "Commit message"
```

The shorthand for `git add` and `git commit` (requires files to be tracked before) is `git commit -a -m`.

```bash
git commit -a -m "commit mesage"
```

### Add remote origin for local repo

```bash
git remote add origin <URL of remote repo>
```

### \*Rebase

* Giúp tái sắp xếp lại các commit, giúp lịch sử commit sạch sẽ và tuyến tính hơn. Khi thực hiện rebase, Git sẽ lấy tất cả các commit của bạn và "phát lại" lên một nhánh mới, như thể bạn đã tạo các commit này dựa trên nhánh đó ngay từ đầu.
* Khi chạy git rebase -> sẽ di chuyển các commit (lấy các commit từ nhánh hiện tại mà không trên nhánh gốc ví dụ main và di chuyển chúng lên đỉnh của nhánh gốc). -> Phát lại commit (Áp dụng từng commit đó lên nhánh gốc theo thứ tự ban đầu, nhưng dựa trên điểm bắt đầu mới)
* Ví dụ: 1. đang làm việc trên nhánh feature. 2. Nhánh main có người mới commit gì đó mới vào. 3. muốn update những cập nhật từ main vào feature của mình -> dùng git rebase.
* Có nghĩa là muốn đưa các thay đổi mới nhất từ `main` vào `feature` để đảm bảo tính năng đang làm ko xung đột với `main`.

```bash
git checkout main
git pull origin main

git checkout feature
git rebase main
```



### git pull

```bash
git push origin main
```

### git log

```bash
# display in one line
git log --oneline

# 5 latest commit
git log -n 5 

# display graph 
git log --graph --online

```

## \*Advance command git commit

### Git commit --amend

```bash
git add file1.txt
git commit -m "cap nhap 1"
```

Quên thêm file 2 vào. nếu commit nữa thì quá nhỏ. nên có thể dùng amend

```bash
git add file2.txt
# option 1: không đổi msg của commit cũ
git commit --amend --no-edit

# option 2: đổi msg mới
git commit --amend -m "message"
```

### Rollback deleted file if not commit

```bash
ls 
a.txt
rm a.txt

# sau khi xóa
git checkout -- a.txt
# sẽ phục hồi file a.txt (commit gan nhat)
```

### Git Reset

quay lại commit trước đó trong lịch sử commit của nhánh hiện tại. đồng thời xóa bỏ các thay đổi trong thư mục và việc và staging area.

* `HEAD` đại diện cho commit hiện tại. khi thực hiện một commit mới `HEAD` sẽ trỏ đến commit đó.
* `HEAD^` đại diện cho commit ngay trước commti hiện tại. tương đương `HEAD~1`
* `--hard` git sẽ không dui chuyển `HEAD` về commit trước đó mà còn xóa tất cả các thay đổi trong thư mục làm việc và staging area. mọi thay đổi chưa được làm việc sẽ bị mất.

```bash
git reset --hard HEAD^
```

* Git sẽ quay lại commit trước đó, và **`HEAD`** sẽ trỏ đến commit đó.
* Tất cả các thay đổi chưa được commit trong thư mục làm việc sẽ bị xóa hoàn toàn, đưa thư mục làm việc về trạng thái giống như tại thời điểm của commit trước đó.

Ví dụ

1. Commit 3(HEAD)
2. Commit 2
3. Commit 1

chạy `git reset --hard HEAD^`

HEAD sẽ trỏ đến commit 2, commit 3 sẽ không còn trong lịch sử (nhưng vẫn còn trong `reflog`)

mọi thay đổi từ commit 3 sẽ bị mất. (xem `git reflog)`

#### **Các Tùy Chọn**

Khi bạn sử dụng lệnh

```
git reset
```

bạn có thể chỉ định các tùy chọn khác nhau để xác định cách mà các thay đổi sẽ được xử lý:

* **`-soft`**: Di chuyển HEAD về commit trước nhưng giữ nguyên các thay đổi trong staging area (index). Điều này có nghĩa là bạn có thể dễ dàng thực hiện lại commit sau khi chỉnh sửa.
* **`-mixed` (mặc định)**: Di chuyển HEAD về commit trước và loại bỏ các thay đổi khỏi staging area, nhưng vẫn giữ chúng trong thư mục làm việc. Bạn sẽ cần phải sử dụng **`git add`** để thêm lại các thay đổi nếu muốn commit chúng.
* **`-hard`**: Di chuyển HEAD về commit trước và xóa tất cả các thay đổi trong thư mục làm việc và staging area. Điều này đồng nghĩa với việc bạn sẽ mất tất cả các thay đổi chưa được commit.

ví dụ

```bash
git reset HEAD~1 # trở về commit trước commit hiện tại.
```

## Xử lý conflig

```bash
git pull origin main
# xoá file, thêm file, hoàn thành commit
git rebase --continue
```

## Git branch

Để tạo và merge branch giữa local và remote repo, bạn có thể làm theo các bước sau:

### Tạo Branch Mới

Nếu branch đã có thì cứ git `checkout branch_name, đẩy lên branch remote = git push origin branch_name`

1. **Tạo một branch mới trong local repo**:

```bash
git branch <tên-branch-mới>
```

2. Tạo branch mới và chuyển đến branch đó

```bash
git checkout -b <tên-branch-mới>
```

3. **Thêm các thay đổi và commit chúng**:

```bash
git add .
git commit -m "Thông điệp commit của bạn"
```

4. **Push branch mới lên remote repo**:

```bash
git push origin <tên-branch-mới>
```

### Merge Branch

1.  **Chuyển sang nhánh `main` trong local repo**:

    ```bash
    git checkout main
    ```
2.  **Fetch các thay đổi từ remote repo**:

    ```bash
    git fetch origin
    ```
3.  **Merge branch mới vào nhánh `main`**:

    ```bash
    git merge <tên-branch-mới>
    ```

    Nếu có conflict, bạn sẽ cần phải giải quyết chúng bằng cách chỉnh sửa các file bị conflict và sau đó commit lại:

    ```bash
    git add .
    git commit -m "Giải quyết conflict"
    ```
4.  **Push các thay đổi lên remote repo**:

    ```bash
    git push origin main
    ```

### Ví dụ git branch

Giả sử bạn muốn tạo một branch mới tên là `feature-branch` và merge nó vào nhánh `main`:

1. **Tạo branch mới**

```bash
git checkout -b feature-branch
```

2. **Thêm thay đổi và commit**:

```bash
git add .
git commit -m "Thêm tính năng mới"
```

3. **Push branch mới lên remote**:

```bash
git push origin feature-branch
```

4. **Chuyển sang nhánh `main` và fetch các thay đổi**:

```bash
git checkout main
git fetch origin
```

5. **Merge branch mới vào `main`**:

```bash
git merge feature-branch
```

6. **Push các thay đổi lên remote**:

```bash
git push remote orgin <branch_name>
```

## Các lệnh Git tà đạo

Cân nhắc khi sử dụng khi làm việc nhóm

#### 1. `git push --force` hoặc `git push -f`

* **Mô tả**: Bắt buộc đẩy thay đổi lên remote, ghi đè toàn bộ lịch sử commit của nhánh.
* **Nguy hiểm**: Khi làm việc nhóm, `--force` có thể ghi đè lên commit của người khác nếu họ cũng đã đẩy commit lên cùng nhánh.
* **Khi nào nên dùng**: Chỉ dùng khi làm việc trên nhánh cá nhân hoặc trong trường hợp bạn chắc chắn rằng không có ai khác phụ thuộc vào commit của bạn.

#### 2. `git rebase -i` (Interactive Rebase) trên nhánh đã chia sẻ

* **Mô tả**: Dùng để chỉnh sửa, sắp xếp lại, gộp commit của mình.
* **Nguy hiểm**: Thay đổi lịch sử commit, làm người khác gặp xung đột nếu nhánh đã được đẩy lên remote và có người khác cũng đang làm việc.
* **Khi nào nên dùng**: Chỉ trên nhánh chưa đẩy lên remote hoặc chưa ai khác đang sử dụng.

#### 3. `git reset --hard`

* **Mô tả**: Xóa các thay đổi và quay lại trạng thái của commit cụ thể, bỏ qua cả thay đổi trong staging và working directory.
* **Nguy hiểm**: Khi dùng nhầm có thể mất dữ liệu chưa được commit. Nếu áp dụng trên một nhánh đã chia sẻ, có thể gây mất các commit quan trọng.
* **Khi nào nên dùng**: Khi cần hủy thay đổi ở local mà không cần đẩy lên remote.

#### 4. `git cherry-pick` từ nhánh chưa ổn định

* **Mô tả**: Chọn một commit cụ thể từ một nhánh khác để áp dụng vào nhánh hiện tại.
* **Nguy hiểm**: Có thể làm lịch sử commit khó theo dõi và gây xung đột nếu cherry-pick nhầm commit chưa ổn định.
* **Khi nào nên dùng**: Khi cần lấy một commit duy nhất và chắc chắn nó sẽ không thay đổi sau này.

#### 5. `git commit --amend`

* **Mô tả**: Chỉnh sửa commit cuối cùng, thay đổi message hoặc thêm bớt nội dung vào commit cuối.
* **Nguy hiểm**: Thay đổi commit history, gây xung đột với người khác nếu nhánh đã được đẩy lên remote.
* **Khi nào nên dùng**: Chỉ khi commit chưa được đẩy lên remote.

#### 6. `git stash -p` hoặc `git stash pop`

* **Mô tả**: Lưu các thay đổi tạm thời, có thể gây rối nếu quên không pop hoặc apply đúng thứ tự.
* **Nguy hiểm**: Dễ dẫn đến việc mất các thay đổi nếu không nhớ thao tác hoặc pop nhầm.

#### 7. `git reflog delete` hoặc `git gc`

* **Mô tả**: `reflog delete` xóa các bản ghi lịch sử gần đây, `git gc` dọn dẹp các tham chiếu và commit không còn sử dụng.
* **Nguy hiểm**: Có thể xóa các commit cần thiết cho người khác, gây khó khăn trong việc phục hồi commit.
