# KỊCH BẢN THỰC HÀNH LAB 5 — GIT FLOW, CODE REVIEW & RELEASES
> **Học phần:** Vận hành và bảo trì phần mềm (DevOps)  
> **Dự án:** `devops-lab5-student-manager`  
> **Repository GitHub:** [lecuong2512/devops-lab5-student-manager](https://github.com/lecuong2512/devops-lab5-student-manager)  
> **Hình thức:** Nhóm 3 thành viên

---

## 👥 PHÂN CHIA VAI TRÒ NHÓM 3 THÀNH VIÊN

| Thành viên | GitHub Username | Vai trò | Nhiệm vụ chính |
|---|---|---|---|
| **Lê Việt Cường** | `lecuong2512` | **Dev A (Lead / Repo Owner)** | Quản trị repo, viết `student.py`, nhánh `feature/add-student`, review PR #3, Release v1.0.0 |
| **Bùi Tuấn** | `tuanbui69` | **Dev B** | Viết `main.py`, nhánh `feature/search-student`, review PR #1, Release v0.1.0, tạo conflict |
| **Lê An** | `lean1835` | **Dev C** | Nhánh `feature/delete-student`, review PR #2, Release v0.2.0, tạo & giải quyết conflict |

---

## 🏷️ KẾ HOẠCH PHÁT HÀNH (3 BẢN RELEASES)

1. **Release `v0.1.0` (Alpha / Base):** Khung ứng dụng và model sinh viên cơ bản.
2. **Release `v0.2.0` (Beta / Features Complete):** Tích hợp đủ 3 tính năng (Thêm, Tìm kiếm, Xóa) trên nhánh `develop`.
3. **Release `v1.0.0` (Production Official):** Bản chính thức trên nhánh `main` sau khi giải quyết xung đột và hoàn thiện README.

---

## 🚀 TIẾN TRÌNH THỰC HIỆN CHI TIẾT TỪNG BƯỚC

```
                      [v0.1.0]
                         │
main     o───────────────o───────────────────────────────────────o───────o [v1.0.0]
          \                                                     /
develop    o───────────o───────────────o───────────────o───────o [v0.2.0]
            \         / \             / \             / \     /
feature/add  o───────o   \           /   \           /   \   / (Conflict & Resolve)
                          \         /     \         /     \ /
feature/search             o───────o       \       /       x (Dev B vs Dev C)
                                            \     /
feature/delete                               o───o
```

---

### GIAI ĐOẠN 1: DỰNG CODE NỀN & PHÁT HÀNH RELEASE v0.1.0

#### Bước 1.1: Dev A (`lecuong2512`) tạo Model sinh viên
Trên máy Dev A:
```bash
cd devops-lab5-student-manager
git checkout main
mkdir src
```
Tạo file `src/student.py`:
```python
"""
Student Manager — DevOps Lab 5
Module: Student Model
"""

class Student:
    """Lớp đại diện cho một sinh viên."""

    def __init__(self, student_id: str, name: str, age: int, grade: str):
        self.student_id = student_id
        self.name = name
        self.age = age
        self.grade = grade

    def __str__(self) -> str:
        return f"Student(id={self.student_id}, name='{self.name}', age={self.age}, grade='{self.grade}')"

    def to_dict(self) -> dict:
        return {
            "student_id": self.student_id,
            "name": self.name,
            "age": self.age,
            "grade": self.grade,
        }

if __name__ == "__main__":
    s1 = Student("SV001", "Nguyen Van A", 20, "K20")
    print(s1)
```
Commit và đẩy lên GitHub:
```bash
git add src/student.py
git commit -m "feat: add Student model class (Dev A)"
git push origin main
```

---

#### Bước 1.2: Dev B (`tuanbui69`) Clone Repo & Tạo Main App
Trên máy Dev B:
```bash
git clone https://github.com/lecuong2512/devops-lab5-student-manager.git
cd devops-lab5-student-manager
git checkout main
git pull origin main
```
Tạo file `src/main.py`:
```python
"""
Student Manager — DevOps Lab 5
Module: Main Application
"""
from student import Student


def main():
    print("=" * 50)
    print("STUDENT MANAGER — DevOps Lab 5")
    print("=" * 50)

    students = [
        Student("SV001", "Nguyen Van A", 20, "K20"),
        Student("SV002", "Tran Thi B", 21, "K20"),
        Student("SV003", "Le Van C", 19, "K21"),
    ]

    for student in students:
        print(f"  {student}")

    print(f"\nTotal: {len(students)} students")


if __name__ == "__main__":
    main()
```
Commit và đẩy lên GitHub:
```bash
git add src/main.py
git commit -m "feat: add main application entry point (Dev B)"
git push origin main
```

---

#### 🏷️ Bước 1.3: TẠO BẢN RELEASE THỨ NHẤT (v0.1.0)
Dev B (hoặc Dev A) thực hiện gắn tag và tạo release đầu tiên:
```bash
git tag -a v0.1.0 -m "Release v0.1.0: Base Student Model and Main CLI Entry"
git push origin v0.1.0
```
*(Hoặc vào GitHub ➔ mục **Releases** ➔ **Create a new release** ➔ Chọn tag `v0.1.0`, Title: `Release v0.1.0 - Base Application`, nội dung mô tả hoàn thành cấu trúc nền tảng ➔ Bấm **Publish release**)*

---

#### Bước 1.4: Đồng bộ nhánh `develop`
Dev A chạy trên máy mình:
```bash
git checkout main
git pull origin main
git checkout develop
git merge main
git push origin develop
```
Dev C (`lean1835`) clone repo về máy:
```bash
git clone https://github.com/lecuong2512/devops-lab5-student-manager.git
cd devops-lab5-student-manager
git checkout develop
git pull origin develop
```

---

### GIAI ĐOẠN 2: GIT FLOW — PHÁT TRIỂN 3 TÍNH NĂNG & RELEASE v0.2.0

#### 🔹 Tính năng 1: Dev A (`lecuong2512`) — `feature/add-student`
1. Tạo branch:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/add-student
   ```
2. Thêm hàm `add_student()` vào `src/main.py`:
   ```python
   def add_student(students: list, student_id: str, name: str, age: int, grade: str) -> list:
       for s in students:
           if s.student_id == student_id:
               print(f"❌ Mã sinh viên {student_id} đã tồn tại!")
               return students
       new_student = Student(student_id, name, age, grade)
       students.append(new_student)
       print(f"Đã thêm sinh viên: {new_student}")
       return students
   ```
   *(Trong `main()`, thêm lệnh gọi: `students = add_student(students, "SV004", "Pham Thi D", 22, "K19")`)*
3. Commit & push:
   ```bash
   git add src/main.py
   git commit -m "feat: add add_student function (Dev A)"
   git push -u origin feature/add-student
   ```
4. **Tạo PR #1 trên GitHub:** Base: `develop` 🠔 Compare: `feature/add-student`.
5. 📸 **Dev B (`tuanbui69`) Review:** Vào PR #1 ➔ Tab *Files changed* ➔ Comment nhận xét code ➔ **Chụp ảnh màn hình làm `05-code-review.png`** ➔ Bấm *Approve*.
6. 📸 **Chụp ảnh trang PR #1 làm `03-pr-add-student.png`** ➔ Bấm **Merge pull request**.

---

#### 🔹 Tính năng 2: Dev B (`tuanbui69`) — `feature/search-student`
1. Tạo branch từ `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/search-student
   ```
2. Thêm hàm `search_student()` vào `src/main.py`:
   ```python
   def search_student(students: list, keyword: str) -> list:
       results = [s for s in students if keyword.lower() in s.name.lower() or keyword.lower() in s.student_id.lower()]
       if results:
           print(f"🔍 Tìm thấy {len(results)} sinh viên với từ khóa '{keyword}':")
           for s in results:
               print(f"  {s}")
       else:
           print(f"❌ Không tìm thấy sinh viên nào với từ khóa '{keyword}'")
       return results
   ```
   *(Trong `main()`, thêm lệnh gọi: `search_student(students, "Van")`)*
3. Commit & push:
   ```bash
   git add src/main.py
   git commit -m "feat: add search_student function (Dev B)"
   git push -u origin feature/search-student
   ```
4. **Tạo PR #2 trên GitHub:** Base: `develop` 🠔 Compare: `feature/search-student`.
5. **Dev C (`lean1835`) Review:** Vào PR #2 viết comment nhận xét ➔ Bấm *Approve*.
6. 📸 **Chụp ảnh trang PR #2 làm `04-pr-search-student.png`** ➔ Bấm **Merge pull request**.

---

#### 🔹 Tính năng 3: Dev C (`lean1835`) — `feature/delete-student`
1. Tạo branch từ `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/delete-student
   ```
2. Thêm hàm `delete_student()` vào `src/main.py`:
   ```python
   def delete_student(students: list, student_id: str) -> list:
       updated = [s for s in students if s.student_id != student_id]
       if len(updated) < len(students):
           print(f"🗑️ Đã xóa sinh viên mã: {student_id}")
       else:
           print(f"❌ Không tìm thấy sinh viên {student_id} để xóa!")
       return updated
   ```
   *(Trong `main()`, thêm lệnh gọi: `students = delete_student(students, "SV001")`)*
3. Commit & push:
   ```bash
   git add src/main.py
   git commit -m "feat: add delete_student function (Dev C)"
   git push -u origin feature/delete-student
   ```
4. **Tạo PR #3 trên GitHub:** Base: `develop` 🠔 Compare: `feature/delete-student`.
5. **Dev A (`lecuong2512`) Review:** Vào PR #3 viết comment nhận xét ➔ Bấm *Approve*.
6. Bấm **Merge pull request**.

---

#### 🏷️ Bước 2.4: TẠO BẢN RELEASE THỨ HAI (v0.2.0)
Sau khi cả 3 tính năng đã được merge đầy đủ vào `develop`:
Dev C (hoặc Dev A) thực hiện:
```bash
git checkout develop
git pull origin develop
git tag -a v0.2.0 -m "Release v0.2.0: Completed add, search, delete features"
git push origin v0.2.0
```
*(Trên GitHub vào mục Releases tạo Release `v0.2.0 - Beta Features Completed`)*

---

### GIAI ĐOẠN 3: TẠO VÀ GIẢI QUYẾT MERGE CONFLICT

> Hai thành viên Dev B và Dev C cùng sửa cùng một dòng trong `README.md` tại cùng thời điểm.

#### 1. Dev B (`tuanbui69`) sửa trước:
```bash
git checkout develop
git pull origin develop
git checkout -b feature/update-readme-b
```
Sửa dòng 3 file `README.md` thành:
```markdown
**Version 1.0.0** — Cập nhật bởi Dev B (tuanbui69)
```
Commit, push và tạo PR merge luôn vào `develop`:
```bash
git add README.md
git commit -m "docs: update version info by Dev B"
git push -u origin feature/update-readme-b
```
*(Tạo PR trên GitHub vào `develop` ➔ Bấm Merge)*

#### 2. Dev C (`lean1835`) cùng sửa dòng đó:
```bash
git checkout develop
git checkout -b feature/update-readme-c
```
Sửa dòng 3 file `README.md` thành:
```markdown
**Version 1.0.0** — Cập nhật bởi Dev C (lean1835)
```
Commit và push:
```bash
git add README.md
git commit -m "docs: update version info by Dev C"
git push -u origin feature/update-readme-c
```
Lên GitHub tạo PR vào `develop` ➔ GitHub báo ⚠️ **"This branch has conflicts that must be resolved"**.

#### 3. Dev C giải quyết conflict ở máy local:
```bash
git checkout develop
git pull origin develop
git merge feature/update-readme-c
```
Git sẽ thông báo: `CONFLICT (content): Merge conflict in README.md`.
- 📸 **Mở file `README.md` trong VS Code ➔ Chụp ảnh màn hình thấy các thẻ `<<<<<<< HEAD`, `=======`, `>>>>>>>` làm ảnh `06-merge-conflict.png`**.
- Chỉnh sửa nội dung dòng 3 kết hợp thông tin cả nhóm:
  ```markdown
  **Version 1.0.0** — Phát triển bởi lecuong2512, tuanbui69 & lean1835
  ```
- Hoàn tất merge và đẩy lên GitHub:
  ```bash
  git add README.md
  git commit -m "merge: resolve README conflict - combine all members"
  git push origin develop
  ```
- 📸 **Chụp màn hình sau khi resolve (`git status` clean) làm ảnh `07-conflict-resolved.png`**.

---

### GIAI ĐOẠN 4: MERGE VÀO MAIN & PHÁT HÀNH RELEASE v1.0.0

#### Bước 4.1: Dev A (`lecuong2512`) Merge vào `main`
```bash
git checkout main
git pull origin main
git merge develop
```

#### Bước 4.2: Cập nhật README.md hoàn chỉnh
Mở `README.md` cập nhật đầy đủ:
```markdown
# Student Manager

Ứng dụng quản lý sinh viên — DevOps Lab 5

**Version 1.0.0** — Phát triển bởi lecuong2512, tuanbui69 & lean1835

## Tính năng
- Quản lý thông tin sinh viên (Student Model)
- Thêm sinh viên mới (add_student)
- Tìm kiếm sinh viên theo tên hoặc mã (search_student)
- Xóa sinh viên theo mã (delete_student)

## Cài đặt & Chạy
```bash
git clone https://github.com/lecuong2512/devops-lab5-student-manager.git
cd devops-lab5-student-manager
python src/main.py
```

## Tác giả
- Lê Việt Cường (`lecuong2512`)
- Bùi Tuấn (`tuanbui69`)
- Lê An (`lean1835`)
```
Commit và đẩy lên:
```bash
git add README.md
git commit -m "docs: finalize README for release v1.0.0"
git push origin main
```

---

#### 🏷️ Bước 4.3: TẠO BẢN RELEASE THỨ BA (v1.0.0 - Official Production)
Dev A thực hiện gắn tag và tạo release chính thức:
```bash
git tag -a v1.0.0 -m "Release v1.0.0: Official Production Release with full features"
git push origin v1.0.0
```
*(Trên GitHub vào **Releases** ➔ Tạo Release `v1.0.0 - Official Production Release`)*

---

### GIAI ĐOẠN 5: CHỤP CÁC ẢNH MINH CHỨNG CÒN LẠI

1. 📸 **Ảnh `08-final-output.png`:** Chạy ứng dụng kiểm tra tất cả tính năng:
   ```bash
   python src/main.py
   ```
2. 📸 **Ảnh `01-git-log.png`:** Xem toàn bộ cây lịch sử Git Flow:
   ```bash
   git log --oneline --graph --all
   ```
3. 📸 **Ảnh `02-git-branch.png`:** Kiểm tra danh sách các nhánh:
   ```bash
   git branch -a
   ```

---

## 📁 DANH SÁCH 8 ẢNH CHỤP CẦN NỘP

Lưu tất cả vào thư mục `screenshots/`:
| Tên ảnh | Nội dung cần thể hiện | Người chụp |
|---|---|---|
| `01-git-log.png` | Lệnh `git log --oneline --graph --all` hiển thị đầy đủ các commit, nhánh và merge | Dev A |
| `02-git-branch.png` | Lệnh `git branch -a` hiển thị đầy đủ các nhánh | Dev A |
| `03-pr-add-student.png` | Giao diện Pull Request #1 trên GitHub | Dev A |
| `04-pr-search-student.png` | Giao diện Pull Request #2 trên GitHub | Dev B |
| `05-code-review.png` | Comment nhận xét Code Review trên PR (có avatar/tên người review) | Dev B hoặc Dev C |
| `06-merge-conflict.png` | File README.md khi đang bị xung đột có các thẻ `<<<<<<< HEAD` | Dev C |
| `07-conflict-resolved.png` | Terminal sau khi resolve conflict và commit thành công | Dev C |
| `08-final-output.png` | Kết quả chạy lệnh `python src/main.py` hiển thị đầy đủ kết quả | Bất kỳ ai |

---

## 📦 ĐÓNG GÓI BÀI NỘP

File zip nộp bài: `Lab5_NhomX_LeVietCuong_BuiTuan_LeAn.zip`
```
Lab5_NhomX_LeVietCuong_BuiTuan_LeAn.zip
├── README.md
├── .gitignore
├── ho_ten_mssv.txt
├── src/
│   ├── student.py
│   └── main.py
└── screenshots/
    ├── 01-git-log.png
    ├── 02-git-branch.png
    ├── 03-pr-add-student.png
    ├── 04-pr-search-student.png
    ├── 05-code-review.png
    ├── 06-merge-conflict.png
    ├── 07-conflict-resolved.png
    └── 08-final-output.png
```

