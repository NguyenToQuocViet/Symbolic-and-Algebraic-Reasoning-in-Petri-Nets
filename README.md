# MATHEMATICAL MODELING – CO2011

## Symbolic and Algebraic Reasoning in Petri Nets  

**Giảng viên ra đề / hướng dẫn:** Dr. Van-Giang Trinh  
**Khoa:** Faculty of Computer Science and Engineering – HCMUT

---

## Thành viên

| Họ tên                    | MSSV    | Lớp | Email                               | Ghi chú  |
|--------------------------|---------|-----|-------------------------------------|---------|
| Nguyễn Tô Quốc Việt      | 2313898 |     | viet.nguyenluminous@hcmut.edu.vn    |         |
| **Trương Thiên Ân** | **2310190** | **TN02** | **an.truong241105@hcmut.edu.vn** | **Leader** |
| Nguyễn Hồ Nguyên Khôi    | 2420020 |     | khoi.nguyen2420020@hcmut.edu.vn     |         |
| Nguyễn Hoàng Nam         | 2412177 | L04 | nam.nguyen270905@hcmut.edu.vn       |         |
| Nguyễn Thế Nhật Minh     | 2412102 |     | minh.nguyenthenhat@hcmut.edu.vn     |         |

---

## Giới thiệu chung

Bài tập lớn hiện thực và phân tích **1-safe Petri net** cho môn *MATHEMATICAL MODELING – CO2011*, theo chủ đề:

> **Symbolic and Algebraic Reasoning in Petri Nets**

Nhóm triển khai một pipeline đầy đủ:

1. Đọc mô hình Petri net từ file chuẩn **PNML**.
2. Tính toán **reachable markings** bằng phương pháp **explicit (BFS/DFS)**.
3. Biểu diễn Petri net và reachable set theo hướng **symbolic (BDD)**.
4. Kiểm chứng tính chất **Deadlock** bằng phương pháp kết hợp BDD và ILP.
5. Giải quyết bài toán **Tối ưu hóa (Optimization)** và đánh giá hiệu năng tổng thể.

---

## Chi tiết các Task và Hàm hiện thực

### **Task 1 – PNML Parser & PetriNet Model**
- Đọc file `.pnml` theo chuẩn 1-safe PNML.
- Xây dựng cấu trúc dữ liệu cơ sở.
- **Hàm hiện thực:**
  - `PetriNet.from_pnml(filename)`: Parse file XML, trích xuất places, transitions, arcs và initial marking.
  - `__init__(...)`: Khởi tạo đối tượng PetriNet với các ma trận `I` (Input), `O` (Output) và vector `M0`.

### **Task 2 – Explicit Reachability (BFS/DFS)**
- Duyệt không gian trạng thái bằng thuật toán tìm kiếm truyền thống.
- **Hàm hiện thực:**
  - `is_enabled(pn, t_idx, M)`: Kiểm tra xem transition `t` có thể bắn tại marking `M` không (tuân thủ luật 1-safe).
  - `fire(pn, t_idx, M)`: Sinh ra marking mới `M'` sau khi bắn transition `t`.
  - `bfs_reachable(pn)`: Trả về tập reachable markings sử dụng Breadth-First Search (Queue).
  - `dfs_reachable(pn)`: Trả về tập reachable markings sử dụng Depth-First Search (Stack).

### **Task 3 – Symbolic Reachability with BDD**
- Mã hóa Petri net và tính toán tập reachable markings sử dụng Binary Decision Diagrams (BDD) để xử lý bùng nổ trạng thái.
- **Hàm hiện thực:**
  - `get_topology_sorted_order(pn)`: Sắp xếp lại thứ tự các biến (Places) dựa trên cấu trúc đồ thị để tối ưu kích thước cây BDD.
  - `build_BDD(pn)`: Mã hóa Initial Marking và xây dựng danh sách các Transition Relations dưới dạng biểu thức logic BDD.
  - `fast_smoothing(bdd_func, vars_set)`: Hàm khử biến (Existential Quantification) được tối ưu hóa.
  - `compute_BDD_reachability(...)`: Thuật toán duyệt không gian trạng thái symbolic (sử dụng Frontier Set).
  - `bdd_reachable(pn)`: Hàm wrapper trả về BDD đại diện cho tập reachable markings.

### **Task 4 – Deadlock Detection (ILP-based Analysis)**
- Phân tích tính chất hệ thống, cụ thể là tìm kiếm trạng thái Deadlock (nơi hệ thống dừng hoạt động).
- Sử dụng Integer Linear Programming (ILP) để lọc kết quả từ BDD.
- **Hàm hiện thực:**
  - `deadlock_reachable_marking(pn, bdd)`: 
    - Trích xuất các marking tiềm năng từ BDD.
    - Kiểm tra điều kiện enable của tất cả transition cho từng marking.
    - Sử dụng ILP (thư viện PuLP) để chọn ra chính xác một trạng thái deadlock (nếu có).

### **Task 5 – Optimization & Evaluation**
- **Optimization:** Tìm marking đạt được (`reachable marking`) sao cho hàm mục tiêu $c \cdot M$ là lớn nhất.
  - Sử dụng thuật toán **Branch & Cut** kết hợp BDD và LP Relaxation.
  - **Hàm hiện thực:**
    - `solve_lp_relaxation(...)`: Giải bài toán quy hoạch tuyến tính (Relaxed LP) để tìm cận trên.
    - `get_bdd_inferences(...)`: Sinh ra các ràng buộc (Cuts) từ cấu trúc BDD.
    - `max_reachable_marking(pn, bdd, c)`: Thuật toán chính tìm marking tối ưu.
- **Evaluation:**
  - Chạy thực nghiệm trên các mô hình mẫu (Token Ring, FMS, Hospital, Cloud...).
  - So sánh hiệu năng (thời gian, bộ nhớ) giữa Explicit (Task 2) và Symbolic (Task 3).
  - Đánh giá tính đúng đắn của giải thuật Optimization và Deadlock Detection.

---

## Cấu trúc thư mục

```text
.
├── README.md                  # Thông tin dự án và hướng dẫn
├── mm-251-assignment.pdf      # Đề bài
├── pnml_file/                 # Thư mục chứa các file .pnml test case
│   ├── fsm.pnml    # Hệ thống sản xuất
│   ├── hospital.pnml    # Quy trình bệnh viện
│   ├── hotel.pnml       # Hệ thống khách sạn
├── src/                       # Source code chính
│   ├── PetriNet.py            # Model & Parser
│   ├── BFS.py                 # Explicit BFS
│   ├── DFS.py                 # Explicit DFS
│   ├── BDD.py                 # Symbolic Reachability
│   ├── Deadlock.py            # Deadlock Detection (Task 4)
│   └── Optimization.py        # Optimization (Task 5)
├── run.py                     # Script chính để chạy demo tổng hợp
└── requirements.txt           # Danh sách thư viện cần thiết
```
---
## Hướng dẫn cài đặt và chạy chương trình
### Clone repo
```sh
git clone git@github.com:TianAn2411/Symbolic-and-Algebraic-Reasoning-in-Petri-Nets.git\
cd Symbolic-and-Algebraic-Reasoning-in-Petri-Nets
```
### Cài đặt các thư viện cần thiết
```sh
pip install -r requirements.txt
```
** Chú ý: Nên cài C++ desktop development phiên bản mới nhất hoặc chạy bằng [docker](https://hub.docker.com/layers/library/python/3.8-slim/images/sha256-a2f6f359b60fb00e46813670f63ea780f3520d9d060bae4d3d08ec3b0dabd54c) **
### Chạy all test
```sh
python run.py
```
---
## Bảng phân việc

| Task | Người phụ trách | MSSV | Tiến độ | Note                                      |
|:----:|------------------|:----:|:-------:|-------------------------------------------|
| 1    | Quốc Việt          | 2313898| 100% |                                          |
| 2    | Thiên Ân           | 2310190| 100% |                                          |
| 3.1  | Nguyên Khôi        | 2420020| 100% |                                          |
| 3.2  |Hoàng Nam           | 2412177| 100% |                                          |
| 4    |Nhật Minh           | 2412102| 100% |                                          |
| 5    |Thiên Ân & Quốc Việt|        | 100% |                                          |

