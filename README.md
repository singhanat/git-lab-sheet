# Lab: Professional Source Code Management using Git

**วิชา:** Professional Source Code Management using Git  
**เวลา:** 3 ชั่วโมง (บรรยาย + ปฏิบัติ)  
**Project ที่ใช้ตลอด Lab:** ระบบตัดเกรดนักศึกษา (Python)

---

## เครื่องสำหรับ Lab

https://hub.datacaster.org

---

## สิ่งที่จะได้เรียนรู้

- ติดตั้งและ config Git บนเครื่อง
- สร้าง local repository และ commit งานจริง
- เชื่อมต่อกับ GitHub ผ่าน Personal Access Token
- จัดการ branch, merge, conflict
- ใช้ stash, amend, log อย่างมืออาชีพ
- ตั้งค่า .gitignore ให้เหมาะกับ project

---

## Part 0 — เตรียมเครื่องก่อนเริ่ม

### 0.1 ตรวจสอบว่ามี Git หรือยัง

```bash
git --version
```

ถ้าไม่มี:
- **Windows:** ดาวน์โหลดจาก https://git-scm.com/download/win
- **macOS:** `brew install git` หรือ `xcode-select --install`
- **Linux (Ubuntu):** `sudo apt install git`

---

### 0.2 ตั้งค่า Identity (ทำครั้งเดียว ใช้ได้ทุก repo)

Git จะแนบชื่อและ email เข้ากับทุก commit ที่สร้าง ตั้งค่าให้ตรงกับ GitHub account

```bash
git config --global user.name "ชื่อของคุณ"
git config --global user.email "your_email@example.com"
```

ตั้งค่า editor ที่ใช้แก้ commit message (เลือกอย่างใดอย่างหนึ่ง):

```bash
# VS Code (แนะนำ)
git config --global core.editor "code --wait"

# หรือ nano (เบากว่า)
git config --global core.editor "nano"
```

ตั้งค่า default branch ให้เป็น `main`:

```bash
git config --global init.defaultBranch main
```

ดูการตั้งค่าทั้งหมด:

```bash
git config --list --global
```

** Checkpoint:** ต้องเห็น `user.name` และ `user.email` ของคุณปรากฏในรายการ

---

### 0.3 ตั้งค่า Authentication กับ GitHub (Personal Access Token)

GitHub ยกเลิกการใช้ password ตั้งแต่ปี 2021 — ต้องใช้ **Personal Access Token (PAT)** แทน

#### สร้าง PAT บน GitHub

1. ไปที่ GitHub → คลิกรูปโปรไฟล์ → **Settings**
2. เลื่อนลงไปที่ **Developer settings** (ด้านล่างสุด)
3. เลือก **Personal access tokens → Tokens (classic)**
4. คลิก **Generate new token (classic)**
5. ตั้งชื่อ เช่น `git-lab-token`
6. กำหนด Expiration: **90 days**
7. ติ๊ก scope: **repo** (เลือกทั้งหมดใน repo)
8. คลิก **Generate token**
9. **คัดลอก token ทันที** — จะไม่สามารถดูได้อีก

#### บันทึก Token ให้ Git จำ (Credential Cache)

**macOS / Linux:**

```bash
git config --global credential.helper store
```

> ครั้งแรกที่ push Git จะถาม username (GitHub username) และ password (ใส่ PAT แทน) — หลังจากนั้นจะจำให้อัตโนมัติ

**Windows:**

```bash
git config --global credential.helper manager
```

> Windows Git Credential Manager จะเปิดหน้าต่างให้ login GitHub ผ่าน browser อัตโนมัติ

---

## Part 1 — Version Control Basics: สร้าง Repo แรก

### 1.1 สร้าง Project Directory

```bash
mkdir grade-calculator
cd grade-calculator
```

### 1.2 สร้าง Local Repository

```bash
git init
```

ผลลัพธ์:
```
Initialized empty Git repository in .../grade-calculator/.git/
```

โฟลเดอร์ `.git/` คือหัวใจของ repo — ห้ามลบ

### 1.3 สร้างไฟล์ Python แรก

สร้างไฟล์ `grade.py`:

```python
# grade.py — ระบบตัดเกรดนักศึกษา

def calculate_grade(score):
    """รับคะแนน 0-100 แล้วคืนค่าเกรด"""
    if score >= 80:
        return "A"
    elif score >= 70:
        return "B"
    elif score >= 60:
        return "C"
    elif score >= 50:
        return "D"
    else:
        return "F"

if __name__ == "__main__":
    score = float(input("กรอกคะแนน (0-100): "))
    grade = calculate_grade(score)
    print(f"เกรดที่ได้: {grade}")
```

ทดสอบว่า program ทำงานได้:

```bash
python grade.py
```

### 1.4 ดูสถานะ Repository

```bash
git status
```

ผลลัพธ์ (ไฟล์ยังไม่ถูก track):
```
Untracked files:
    grade.py
```

### 1.5 เพิ่มไฟล์เข้า Staging และ Commit แรก

```bash
git add grade.py
git status
```

ผลลัพธ์ (ไฟล์อยู่ใน staging แล้ว):
```
Changes to be committed:
    new file: grade.py
```

```bash
git commit -m "feat: add grade calculator with A-F scale"
```

** Checkpoint:** `git log --oneline` ต้องแสดง commit แรกของคุณ

---

## Part 2 — เอา Repo ขึ้น GitHub

### 2.1 สร้าง Repository บน GitHub

1. ไปที่ github.com → คลิก **New repository** (ปุ่ม +)
2. ตั้งชื่อ: `grade-calculator`
3. เลือก **Public**
4. **อย่าติ๊ก** Add README, .gitignore, license (เราจะสร้างเองใน local)
5. คลิก **Create repository**

### 2.2 เชื่อม Local กับ Remote

คัดลอก URL ของ repo (ที่ขึ้นต้นด้วย `https://`):

```bash
git remote add origin https://github.com/YOUR_USERNAME/grade-calculator.git
```

ตรวจสอบ remote:

```bash
git remote -v
```

ผลลัพธ์:
```
origin  https://github.com/YOUR_USERNAME/grade-calculator.git (fetch)
origin  https://github.com/YOUR_USERNAME/grade-calculator.git (push)
```

### 2.3 Push ขึ้น GitHub

```bash
git push -u origin main
```

> ถ้า Git ถามชื่อผู้ใช้และรหัสผ่าน: ใส่ **GitHub username** และ **PAT** (ที่สร้างใน Part 0.3)  
> หลังจากนี้ไม่ต้องใส่อีก

**-u** ย่อมาจาก `--set-upstream` — ทำให้ครั้งต่อไปพิมพ์แค่ `git push` ได้เลย

** Checkpoint:** รีเฟรชหน้า GitHub — ต้องเห็น `grade.py` ปรากฏบน repo

---

## Part 3 — Gitignore

### 3.1 สร้าง .gitignore

ก่อน commit เพิ่มเติม ตั้งค่า .gitignore ก่อนเสมอ

สร้างไฟล์ `.gitignore`:

```
# Python
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/

# Virtual environment
venv/
.venv/
env/

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# ข้อมูลนักศึกษา (ข้อมูลจริง ห้าม commit)
students_data.csv
*.secret
```

### 3.2 ทดสอบ .gitignore

```bash
mkdir __pycache__
touch __pycache__/grade.cpython-311.pyc
git status
```

โฟลเดอร์ `__pycache__` ต้องไม่ปรากฏใน status

ตรวจสอบว่า pattern ทำงาน:

```bash
git check-ignore -v __pycache__/
```

### 3.3 Commit .gitignore

```bash
git add .gitignore
git commit -m "chore: add .gitignore for Python project"
git push
```

** Checkpoint:** `.gitignore` ปรากฏบน GitHub แต่ `__pycache__` ไม่ปรากฏ

---

## Part 4 — Git Basic Commands: พัฒนา Feature แรก

### 4.1 เพิ่ม Feature: คำนวณ GPA

แก้ไข `grade.py` — เพิ่ม function ใหม่:

```python
# grade.py — ระบบตัดเกรดนักศึกษา

def calculate_grade(score):
    """รับคะแนน 0-100 แล้วคืนค่าเกรด"""
    if score >= 80:
        return "A"
    elif score >= 70:
        return "B"
    elif score >= 60:
        return "C"
    elif score >= 50:
        return "D"
    else:
        return "F"

def calculate_gpa(scores):
    """รับ list คะแนนหลายวิชา แล้วคืน GPA (0.00-4.00)"""
    grade_points = {"A": 4.0, "B": 3.0, "C": 2.0, "D": 1.0, "F": 0.0}
    grades = [calculate_grade(s) for s in scores]
    gpa = sum(grade_points[g] for g in grades) / len(grades)
    return round(gpa, 2)

if __name__ == "__main__":
    print("=== ระบบตัดเกรด ===")
    score = float(input("กรอกคะแนน (0-100): "))
    grade = calculate_grade(score)
    print(f"เกรดที่ได้: {grade}")
```

### 4.2 ดู Diff ก่อน Commit

```bash
git diff
```

> แสดงสิ่งที่เปลี่ยนแปลงตั้งแต่ commit ล่าสุด (สีเขียว = เพิ่ม, แดง = ลบ)

```bash
git add grade.py
git diff --staged
```

> `--staged` ดู diff ของสิ่งที่อยู่ใน staging แล้ว

```bash
git commit -m "feat: add GPA calculator function"
git push
```

** Checkpoint:** `git log --oneline` ต้องมี 3 commits

---

## Part 5 — Branching: พัฒนา Feature แบบแยก Branch

### 5.1 สร้าง Branch สำหรับ Feature ใหม่

เราจะเพิ่ม feature แสดงผลแบบ report — แต่ทำใน branch แยก ไม่กระทบ main

```bash
git checkout -b feature/report
```

ตรวจสอบ branch ปัจจุบัน:

```bash
git branch
```

ผลลัพธ์ (`*` = branch ที่อยู่ตอนนี้):
```
* feature/report
  main
```

### 5.2 พัฒนา Feature บน Branch ใหม่

สร้างไฟล์ `report.py`:

```python
# report.py — แสดงผลรายงานเกรด

from grade import calculate_grade, calculate_gpa

def print_report(student_name, scores):
    """พิมพ์รายงานเกรดของนักศึกษา"""
    subjects = ["คณิตศาสตร์", "ภาษาอังกฤษ", "วิทยาการคอมพิวเตอร์"]
    print(f"\n{'='*40}")
    print(f"  รายงานเกรด: {student_name}")
    print(f"{'='*40}")
    for subject, score in zip(subjects, scores):
        grade = calculate_grade(score)
        print(f"  {subject:<25} {score:>5.1f}  [{grade}]")
    print(f"{'─'*40}")
    gpa = calculate_gpa(scores)
    print(f"  {'GPA':<25} {gpa:>7.2f}")
    print(f"{'='*40}\n")

if __name__ == "__main__":
    name = input("ชื่อนักศึกษา: ")
    scores = []
    for subject in ["คณิตศาสตร์", "ภาษาอังกฤษ", "วิทยาการคอมพิวเตอร์"]:
        s = float(input(f"คะแนน {subject}: "))
        scores.append(s)
    print_report(name, scores)
```

```bash
git add report.py
git commit -m "feat: add student grade report module"
```

สร้างไฟล์ `test_grade.py`:

```python
# test_grade.py — ทดสอบ functions

from grade import calculate_grade, calculate_gpa

def test_calculate_grade():
    assert calculate_grade(85) == "A"
    assert calculate_grade(75) == "B"
    assert calculate_grade(65) == "C"
    assert calculate_grade(55) == "D"
    assert calculate_grade(40) == "F"
    print("calculate_grade: ผ่านทุก test")

def test_calculate_gpa():
    assert calculate_gpa([85, 75, 65]) == 3.0
    assert calculate_gpa([40, 40, 40]) == 0.0
    print("calculate_gpa: ผ่านทุก test")

if __name__ == "__main__":
    test_calculate_grade()
    test_calculate_gpa()
    print("\n🎉 ผ่านทุก test!")
```

```bash
git add test_grade.py
git commit -m "test: add unit tests for grade and GPA functions"
```

** Checkpoint:** `git log --oneline` บน branch นี้มี commit มากกว่า main 2 commits

---

## Part 6 — Merging: รวม Branch กลับ main

### 6.1 กลับไป main และ Merge

```bash
git checkout main
git merge feature/report
git push
```

### 6.2 ทดลอง Conflict (จำลองสถานการณ์จริง)

สร้าง branch ใหม่:

```bash
git checkout -b feature/grade-plus
```

แก้ `grade.py` — เปลี่ยน threshold เกรด A เป็น **85**:

```python
def calculate_grade(score):
    if score >= 85:   # เปลี่ยนจาก 80 เป็น 85
        return "A"
    ...
```

```bash
git add grade.py
git commit -m "feat: raise grade A threshold to 85"
git checkout main
```

แก้ `grade.py` บน main — เปลี่ยน threshold เป็น **75** (คนละค่า):

```python
def calculate_grade(score):
    if score >= 75:   # เปลี่ยนจาก 80 เป็น 75
        return "A"
    ...
```

```bash
git add grade.py
git commit -m "feat: lower grade A threshold to 75"
git merge feature/grade-plus
```

จะเกิด conflict:
```
CONFLICT (content): Merge conflict in grade.py
Automatic merge failed; fix conflicts then commit the result.
```

### 6.3 แก้ไข Conflict

เปิด `grade.py` จะเห็น conflict markers:

```python
<<<<<<< HEAD
    if score >= 75:
=======
    if score >= 85:
>>>>>>> feature/grade-plus
```

แก้ไขให้เหลือค่าที่ต้องการ (ตกลงใช้ **80** เหมือนเดิม):

```python
    if score >= 80:
```

จากนั้น:

```bash
git add grade.py
git commit -m "merge: resolve conflict on grade A threshold, use 80"
git push
```

ลบ branch ที่ไม่ต้องการแล้ว:

```bash
git branch -d feature/grade-plus
git branch -d feature/report
```

** Checkpoint:** `git log --oneline --graph` ต้องเห็นประวัติแบบ graph

---

## Part 7 — Git Log: ตรวจสอบประวัติ

### 7.1 ดู Log รูปแบบต่าง ๆ

```bash
# แบบย่อ
git log --oneline

# แบบ graph ดู branch
git log --oneline --graph --all

# กรองตามผู้แก้ไข
git log --oneline --author="ชื่อของคุณ"

# ดู 3 commits ล่าสุด
git log -n 3

# ดูรายละเอียด commit หนึ่ง ๆ
git show HEAD
```

### 7.2 สร้าง Alias สะดวก

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
git lg
```

### 7.3 เปรียบเทียบ Commits

```bash
# ความต่างระหว่าง commit ล่าสุดกับก่อนหน้า
git diff HEAD~1 HEAD

# ความต่างระหว่าง 2 commits (ใช้ hash จาก git log)
git diff <hash1> <hash2>
```

** Checkpoint:** `git lg` แสดง graph ครบทุก branch และ merge point

---

## Part 8 — Move and Remove: จัดการไฟล์

### 8.1 จัดระเบียบ Project Structure

```bash
mkdir src
git mv grade.py src/grade.py
git mv report.py src/report.py
git status
```

ผลลัพธ์:
```
Changes to be committed:
    renamed: grade.py -> src/grade.py
    renamed: report.py -> src/report.py
```

```bash
git commit -m "refactor: move source files to src/ directory"
```

### 8.2 ลบไฟล์ผ่าน Git

```bash
touch temp_notes.txt
git add temp_notes.txt
git commit -m "temp: quick notes"

git rm temp_notes.txt
git commit -m "chore: remove temp notes file"
```

### 8.3 ลบออกจาก Tracking แต่ยังเก็บไฟล์ไว้

```bash
touch local_config.txt
git add local_config.txt
git commit -m "config: add local config"

git rm --cached local_config.txt
echo "local_config.txt" >> .gitignore
git add .gitignore
git commit -m "chore: untrack local_config, add to gitignore"
git push
```

ไฟล์ `local_config.txt` ยังอยู่บนเครื่อง แต่ Git ไม่ track แล้ว

---

## Part 9 — Amend: แก้ไข Commit ล่าสุด

### 9.1 แก้ Commit Message ที่พิมพ์ผิด

```bash
git commit -m "fet: oops typo in commit message"
git commit --amend -m "feat: fix commit message typo example"
```

### 9.2 เพิ่มไฟล์ที่ลืม Stage

เพิ่ม function ใหม่ใน `src/grade.py`:

```python
def calculate_weighted_gpa(scores, credits):
    """คำนวณ GPA แบบถ่วงน้ำหนักตาม credit"""
    grade_points = {"A": 4.0, "B": 3.0, "C": 2.0, "D": 1.0, "F": 0.0}
    total_points = sum(grade_points[calculate_grade(s)] * c
                       for s, c in zip(scores, credits))
    total_credits = sum(credits)
    return round(total_points / total_credits, 2)
```

```bash
git add src/grade.py
git commit -m "feat: add weighted GPA calculation"
```

เพิ่ม test แล้วเพิ่งนึกได้ว่าลืม — ใช้ amend:

```python
# เพิ่มใน test_grade.py
def test_weighted_gpa():
    assert calculate_weighted_gpa([85, 75], [3, 1]) == 3.5
    print("calculate_weighted_gpa: ผ่านทุก test")
```

```bash
git add test_grade.py
git commit --amend --no-edit
```

ผลลัพธ์: ไม่มี commit ใหม่ — ไฟล์ถูกเพิ่มเข้า commit เดิม

### 9.3 ยกเลิก Commit ล่าสุด (แบบ safe)

```bash
# ยกเลิก commit แต่เก็บ changes ไว้ใน staging
git reset --soft HEAD~1

git status        # ยืนยันว่า changes ยังอยู่
git commit -m "feat: add weighted GPA with tests"
git push
```

** Checkpoint:** `git log --oneline` ต้องไม่มี commit message ที่ผิดพลาด

---

## Part 10 — Stash: พักงานชั่วคราว

### 10.1 จำลองสถานการณ์ต้อง Switch Branch ด่วน

กำลังเพิ่ม feature ค้างไว้ใน `src/grade.py`:

```python
def get_honor_roll(students_scores):
    # TODO: กำลังทำอยู่ ยังไม่เสร็จ
    pass
```

แต่มีคนรายงาน bug ด่วน — ต้อง switch ทันที:

```bash
git status   # มีไฟล์ที่แก้ไขค้างอยู่ ยัง switch ไม่ได้สะดวก

git stash save "wip: honor roll feature"
git status   # clean working directory แล้ว
```

### 10.2 แก้ Bug บน Main

```bash
git checkout main
```

แก้ edge case คะแนน invalid ใน `src/grade.py`:

```python
def calculate_grade(score):
    if not (0 <= score <= 100):
        raise ValueError(f"คะแนนต้องอยู่ระหว่าง 0-100 ได้รับ: {score}")
    if score >= 80:
        return "A"
    ...
```

```bash
git add src/grade.py
git commit -m "fix: add input validation for score range"
git push
```

### 10.3 กลับมาทำงานต่อ

```bash
git stash list
# stash@{0}: wip: honor roll feature

git stash pop
git status   # งานกลับมาแล้ว
```

```bash
git add src/grade.py
git commit -m "feat: add honor roll function skeleton"
git push
```

### 10.4 คำสั่ง Stash เพิ่มเติม

```bash
git stash list                 # ดู stash ทั้งหมด
git stash apply stash@{0}      # นำออกมาโดยไม่ลบ
git stash drop stash@{0}       # ลบ stash ที่ระบุ
git stash clear                # ล้างทั้งหมด
git stash -u                   # stash รวม untracked files ด้วย
```

** Checkpoint:** `git log --oneline` มี commit ของทั้ง bug fix และ feature ใหม่

---

## 🏁 Part 11 — สรุป: ดู Project ทั้งหมด

### 11.1 ดูประวัติทั้งหมดแบบ Graph

```bash
git log --oneline --graph --all --decorate
```

### 11.2 ดูสถิติ

```bash
# ดูจำนวนบรรทัดที่เพิ่ม/ลบในแต่ละ commit
git log --stat --oneline
```

### 11.3 โครงสร้าง Project สุดท้าย

```
grade-calculator/
├── .gitignore
├── src/
│   ├── grade.py          # core functions
│   └── report.py         # report display
└── test_grade.py         # unit tests
```

---

## 📝 แบบฝึกหัดเพิ่มเติม (ทำหลัง Lab)

**ระดับ 1 — ทบทวน**
- [ ] สร้าง repo ใหม่สำหรับ project "เครื่องคิดเลขพื้นฐาน" (บวก ลบ คูณ หาร)
- [ ] ทำ commit ทุกครั้งที่เพิ่ม operation ใหม่
- [ ] Push ขึ้น GitHub

**ระดับ 2 — ฝึก Branch**
- [ ] สร้าง branch `feature/history` สำหรับเก็บประวัติการคำนวณ
- [ ] merge กลับ main และ push

**ระดับ 3 — ท้าทาย**
- [ ] ชวนเพื่อน 1 คน collaborate บน repo เดียวกัน
- [ ] แต่ละคน commit บน branch ของตัวเอง
- [ ] ฝึก merge และแก้ conflict จริง ๆ

---

## Tips & Tricks สำหรับมืออาชีพ

```bash
# ดูว่าใครแก้ไขบรรทัดไหนในไฟล์
git blame src/grade.py

# คืนค่าไฟล์ที่แก้ไขไปแล้วกลับสู่สถานะ commit ล่าสุด
git restore src/grade.py

# ดู commit ที่ลบไฟล์ไปแล้ว
git log --all --full-history -- "path/to/deleted_file.py"

# คืนค่าไฟล์จาก commit ที่ระบุ
git checkout <hash> -- src/grade.py
```

---

## Commit Message Convention ที่แนะนำ

| Prefix | ใช้เมื่อ |
|--------|---------|
| `feat:` | เพิ่ม feature ใหม่ |
| `fix:` | แก้ bug |
| `docs:` | แก้ documentation |
| `refactor:` | ปรับโครงสร้าง ไม่เปลี่ยน behavior |
| `test:` | เพิ่ม/แก้ test |
| `chore:` | งาน maintenance (config, gitignore ฯลฯ) |
| `style:` | แก้ formatting ไม่กระทบ logic |

ตัวอย่างที่ดี:
```
feat: add weighted GPA calculation with credit hours
fix: validate score input range 0-100
refactor: move source files to src/ directory
```
