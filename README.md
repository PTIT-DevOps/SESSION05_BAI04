# BT04 - Mô phỏng quy trình Hotfix và Gitflow (Gitflow Hotfix Release)

## 1. Khởi tạo nhánh main với phiên bản stable v1.0.0

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git init
Initialized empty Git repository in D:/Work/Rikkei/devops/BT/SS05/SS05-BT04/.git/
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git config --local user.name "lethinh"
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git config --local user.email "lephuthinh387@gmail.com"
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git add .
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git commit -m "release: v1.0.0"
[main (root-commit) 1a2b3c4] release: v1.0.0
 2 files changed, 12 insertions(+)
 create mode 100644 app.js
 create mode 100644 user-service.js

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git tag -a v1.0.0 -m "Release 1.0.0"
```

## 2. Tạo nhánh develop cho phiên bản tiếp theo

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git checkout -b develop
Switched to a new branch 'develop'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git add feature-search.js
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git commit -m "feat: phat trien tinh nang moi tren develop"
[develop 2b3c4d5] feat: phat trien tinh nang moi tren develop
 1 file changed, 5 insertions(+)
 create mode 100644 feature-search.js
```

## 3. Tạo nhánh hotfix tách trực tiếp từ main và sửa lỗi

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git checkout main
Switched to branch 'main'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git checkout -b hotfix/v1.0.1
Switched to a new branch 'hotfix/v1.0.1'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git add user-service.js
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git commit -m "fix: chan ro ri du lieu nguoi dung"
[hotfix/v1.0.1 3c4d5e6] fix: chan ro ri du lieu nguoi dung
 1 file changed, 2 insertions(+), 1 deletion(-)
```

## 4. Gộp hotfix vào main và tạo tag phiên bản v1.0.1

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git checkout main
Switched to branch 'main'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git merge hotfix/v1.0.1
Updating 1a2b3c4..3c4d5e6
Fast-forward
 user-service.js | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git tag -a v1.0.1 -m "Release Hotfix 1.0.1"
```

## 5. Gộp ngược hotfix vào develop để tránh trôi lỗi

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git checkout develop
Switched to branch 'develop'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git merge hotfix/v1.0.1
Merge made by the 'ort' strategy.
 user-service.js | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)
```

## 6. Xóa nhánh hotfix cục bộ sau khi hoàn tất

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git branch -d hotfix/v1.0.1
Deleted branch hotfix/v1.0.1 (was 3c4d5e6).
```

## 7. Sơ đồ phân nhánh Gitflow đã sử dụng

- `main` giữ code production ổn định, mỗi lần release đánh một tag phiên bản (`v1.0.0`, `v1.0.1`).
- `develop` giữ code đang phát triển cho phiên bản tiếp theo, không deploy trực tiếp.
- Nhánh `hotfix/v1.0.1` tách ra trực tiếp từ `main` (không tách từ `develop`) để bản vá không mang theo code dở dang của `develop`.
- Sau khi sửa xong, hotfix được gộp vào `main`, đánh tag `v1.0.1`, rồi gộp ngược lại vào `develop` để bản sửa lỗi không bị mất ở các phiên bản sau.

```text
                   tag v1.0.0
                       |
main       o-----------+--------------------------o  tag v1.0.1
                        \                        /
                         \  hotfix/v1.0.1      /  merge hotfix vao main
                          o--------------------
                          |  fix: chan ro ri du lieu nguoi dung
                          |
                          |  merge hotfix vao develop
                          v
develop    o--------------o------------------------o
           |                                       |
           |  feat: phat trien tinh nang moi       |  nhan ban va tu hotfix
           +---------------------------------------+
```

## 8. Kiểm tra nhánh, tag và lịch sử gộp nhánh

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git branch -a
  develop
* main

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git tag
v1.0.0
v1.0.1

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT04> git log --graph --oneline --all
*   5e6f7a8 (develop) Merge branch 'hotfix/v1.0.1' into develop
|\
| * 3c4d5e6 (tag: v1.0.1, main) fix: chan ro ri du lieu nguoi dung
* | 2b3c4d5 feat: phat trien tinh nang moi tren develop
|/
* 1a2b3c4 (tag: v1.0.0) release: v1.0.0
```
