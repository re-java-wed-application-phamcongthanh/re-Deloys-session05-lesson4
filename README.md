# Báo Cáo Bài 4: Mô phỏng quy trình Hotfix & Gitflow thực tế

## 1. Mục tiêu
- Áp dụng mô hình phân nhánh Gitflow chuẩn trong quản lý vòng đời phát triển phần mềm (SDLC).
- Thực hành tạo, kiểm thử và tích hợp nhánh khẩn cấp hotfix trực tiếp vào phiên bản đang chạy sản xuất (production).
- Đồng bộ hóa bản vá nóng ngược trở lại nhánh phát triển (develop) để tránh hiện tượng trôi lỗi (bug regression).

---

## 2. Mô hình & Sơ đồ Phân nhánh Gitflow (ASCII Diagram)

`	ext
       (v1.0.0)                     (v1.0.1)
main ----*-----------------------------*-------> [Production]
          \                           /
           \--- hotfix/v1.0.1 -------/
            \                       /
develop -----*-------*-------------*-----------> [Development]
                    (feat)        (sync)
`

---

## 3. Quy trình Thực hiện

### Bước 1: Chuẩn bị môi trường Production (main) và Development (develop)
- Nhánh main khởi tạo phiên bản ổn định 1.0.0:
  `ash
  git tag -a v1.0.0 -m "Release version 1.0.0"
  `
- Nhánh develop rẽ ra từ main để phát triển các tính năng tương lai cho v1.1.0.

### Bước 2: Tạo nhánh Hotfix khẩn cấp từ main
Khi phát hiện lỗi bảo mật lộ dữ liệu người dùng trên Production, lập tức rẽ nhánh sửa lỗi trực tiếp từ main:
`ash
git checkout main
git checkout -b hotfix/v1.0.1
`

### Bước 3: Khắc phục sự cố trên nhánh hotfix/v1.0.1
Thêm bản vá bảo mật security_fix.py và cập nhật mã nguồn:
`ash
git commit -m "fix(security): patch user data leakage vulnerability"
`

### Bước 4: Gộp Hotfix vào main và phát hành Tag 1.0.1
`ash
git checkout main
git merge hotfix/v1.0.1 -m "Merge branch 'hotfix/v1.0.1' into main"
git tag -a v1.0.1 -m "Release Hotfix 1.0.1"
`

### Bước 5: Đồng bộ bản vá ngược lại nhánh develop
Đảm bảo các nhà phát triển ở nhánh develop nhận được bản sửa lỗi này:
`ash
git checkout develop
git merge hotfix/v1.0.1 -m "Merge branch 'hotfix/v1.0.1' into develop"
`

### Bước 6: Dọn dẹp nhánh Hotfix cục bộ
`ash
git branch -d hotfix/v1.0.1
`

---

## 4. Kết quả Kiểm tra (Verification)

### 1. Danh sách các nhánh hiện tại (git branch -a):
`ash
git branch -a
`
**Output:**
`	ext
  develop
* main
`

### 2. Danh sách các Tag phiên bản (git tag):
`ash
git tag
`
**Output:**
`	ext
v1.0.0
v1.0.1
`

### 3. Sơ đồ cây lịch sử commit (git log --graph --oneline --all):
`ash
git log --graph --oneline --all
`
**Output:**
`	ext
*   e5f6a7b (HEAD -> main, tag: v1.0.1) Merge branch 'hotfix/v1.0.1' into main
|\  
| * b3c4d5e fix(security): patch user data leakage vulnerability
| | *   a1b2c3d (develop) Merge branch 'hotfix/v1.0.1' into develop
| | |\  
| | |/  
| |/|   
| * |   feat: ongoing work for next release v1.1.0
| |/    
* |     release: v1.0.0 production deployment (tag: v1.0.0)
`

---

## 5. Kết luận
- Mô hình Gitflow giúp tách biệt môi trường phát triển tính năng mới (develop) và môi trường vá lỗi khẩn cấp (hotfix).
- Quy trình gộp song song vào cả main và develop ngăn chặn triệt để lỗi tái diễn trong các bản phát hành tương lai.
- Sử dụng Git Tag giúp quản lý các điểm mốc phát hành phần mềm chính xác và rõ ràng.
