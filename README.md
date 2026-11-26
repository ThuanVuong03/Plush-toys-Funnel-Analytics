# Phân Tích Hiệu Suất Sản Phẩm & Tối Ưu Hóa Phễu Chuyển Đổi Plush toys

Dự án nghiên cứu chuyên sâu về hiệu suất kinh doanh, doanh thu, tỷ lệ hoàn tiền và tối ưu hóa phễu chuyển đổi (Conversion Rate Optimization - CRO) với dữ liệu kinh doanh và lưu lượng của sản phẩm thú nhồi bông. Thông qua việc phân tích chuỗi dữ liệu hành vi người dùng và đơn hàng, dự án xác định các sản phẩm chủ lực, phát hiện các điểm nghẽn trong quy trình mua hàng và đề xuất chiến lược kinh doanh nhằm tối đa hóa doanh thu thuần.

---

## 📌 Mục Tiêu Dự Án

1. **Phân Tích Doanh Thu & Tỷ Lệ Hoàn Tiền (Revenue & Refund Analysis)**:
   - Đánh giá Tổng doanh thu (Gross Revenue), Tỷ lệ hoàn tiền (Refund Rate) và Doanh thu thuần (Net Revenue) của từng sản phẩm.
   - Nhận diện sản phẩm đóng góp doanh thu lớn nhất và sản phẩm đang gặp vấn đề về chất lượng (tỷ lệ hoàn tiền cao).

2. **Phân Tích Phễu Chuyển Đổi Theo Sản Phẩm (Product-Specific Funnel Analysis)**:
   - So sánh chi tiết trải nghiệm mua sắm giữa hai sản phẩm chủ lực: **"The Original Mr. Fuzzy"** và **"The Forever Love Bear"**.
   - Đo lường mức độ quan tâm của khách hàng (từ trang danh mục đến trang chi tiết sản phẩm) và tỷ lệ "chốt đơn" qua từng bước trong phễu 4 chặng.

3. **Đề Xuất Chiến Lược Kinh Doanh (Business Recommendations)**:
   - Đề xuất phân bổ ngân sách quảng cáo tối ưu.
   - Xây dựng giải pháp cải thiện các bước điểm nghẽn nhằm giảm thiểu tỷ lệ thoát đơn (drop-off rate).

---

## 📁 Dữ Liệu Sử Dụng

Dự án khai thác 6 tập dữ liệu quan hệ được trích xuất từ hệ thống E-commerce:

| Tên Tập Dữ Liệu | Số Dòng | Số Cột | Nội Dung Chính |
| :--- | :--- | :--- | :--- |
| `website_sessions.csv` | 472,871 | 9 | Thông tin phiên truy cập (Session ID, User ID, UTM source/campaign/content, Device type, Referer). |
| `website_pageviews.csv` | 1,188,124 | 4 | Chi tiết từng lượt xem trang (Pageview ID, Session ID, URL, Created at). |
| `orders.csv` | 32,313 | 8 | Thông tin đơn hàng (Order ID, Session ID, User ID, Primary Product ID, Price, COGS). |
| `order_items.csv` | 40,025 | 7 | Chi tiết từng sản phẩm trong đơn (Order Item ID, Order ID, Product ID, Primary Item indicator, Price, COGS). |
| `order_item_refunds.csv` | 1,731 | 5 | Dữ liệu hoàn tiền (Refund ID, Order Item ID, Refund Amount USD, Created at). |
| `products.csv` | 4 | 3 | Danh mục 4 sản phẩm chính của hệ thống. |

---

## ⚙️ Quy Trình & Phương Pháp Xử Lý Dữ Liệu

Dự án áp dụng quy trình phân tích dữ liệu chuẩn hóa từ kiểm tra chất lượng dữ liệu đến phân tích phễu theo chuỗi thời gian:

1. **Kiểm Tra Tổng Thể Dữ Liệu (`check_dataframe`)**:
   - Xây dựng hàm tái sử dụng để kiểm tra tự động metadata, kiểu dữ liệu, dòng trùng lặp (duplicates), giá trị thiếu (missing values/NA) và phát hiện biến dị biệt (outliers) theo phương pháp khoảng biến thiên tứ phân vị (IQR Method với ngưỡng 1.5).
   - Ghi nhận: Không có dòng trùng lặp hoàn toàn trong các bảng chính. Bảng `website_sessions` có chứa thông tin missing ở các trường UTM campaign/source/content (17.62%) và referer (8.44%), phản ánh các phiên truy cập tự nhiên (organic search/direct).

2. **Chuẩn Hóa Dữ Liệu & Thời Gian**:
   - Chuyển đổi định dạng `created_at` sang kiểu `datetime` để hỗ trợ lọc và truy vấn theo chuỗi thời gian chính xác.

3. **Thuật Toán Phân Tích Phễu 4 Chặng Tuần Tự (Sequential Funnel Tracking)**:
   - Xác định mốc thời gian ra mắt của sản phẩm *"The Forever Love Bear"* (`2013-01-06 13:27:48`) làm mốc bắt đầu so sánh công bằng cho cả hai sản phẩm.
   - Phân tích 4 chặng chuyển đổi tuân thủ nghiêm ngặt thứ tự thời gian (`timestamp` của bước sau phải lớn hơn bước trước):
     - **Chặng 1 (Product)**: Xem danh mục sản phẩm (`/products`).
     - **Chặng 2 (Detail)**: Xem trang chi tiết sản phẩm (`/the-original-mr-fuzzy` hoặc `/the-forever-love-bear`) sau khi xem danh mục.
     - **Chặng 3 (Cart)**: Thêm vào giỏ hàng (`/cart`) sau khi xem trang chi tiết.
     - **Chặng 4 (Thank)**: Hoàn tất đơn hàng (`/thank-you-for-your-order`) sau khi vào giỏ hàng.

---

## 📊 Kết Quả Phân Tích Chính

### 1. Phân Tích Doanh Thu & Hoàn Tiền Theo Sản Phẩm

| Product ID | Tên Sản Phẩm | Gross Revenue (USD) | Net Revenue (USD) | Tỷ Lệ Hoàn Tiền (%) | Số Lượng Hoàn Tiền | Tổng Số Lượng Bán |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: |
| **1** | **The Original Mr. Fuzzy** | \$1,211,057.74 | **\$1,149,220.11** | 5.11% | 1,237 | 24,226 |
| **2** | **The Forever Love Bear** | \$347,702.04 | \$339,963.33 | 2.23% | 129 | 5,796 |
| **3** | **The Birthday Sugar Panda** | \$229,260.15 | \$215,417.16 | **6.04%** | 301 | 4,985 |
| **4** | **The Hudson River Mini bear** | \$150,489.82 | \$148,570.46 | 1.28% | 64 | 5,018 |

**Nhận xét chính**:
- **Sản phẩm chủ lực**: **The Original Mr. Fuzzy** là sản phẩm mang lại doanh thu thuần cao nhất toàn hệ thống (**\$1,149,220.11**), vượt trội so với các sản phẩm còn lại.
- **Cảnh báo chất lượng**: **The Birthday Sugar Panda** ghi nhận tỷ lệ hoàn tiền cao nhất (**6.04%**), theo sau là **The Original Mr. Fuzzy** (**5.11%**). Đây là các mức hoàn tiền đáng chú ý cần được kiểm tra nguyên nhân gốc rễ (mô tả sản phẩm, kỳ vọng khách hàng, hoặc khâu vận chuyển).

---

### 2. So Sánh Phễu Chuyển Đổi 4 Chặng (Tính từ ngày 06/01/2013)

Giai đoạn so sánh ghi nhận tổng cộng **231,191 phiên** truy cập vào trang danh mục `/products`.

#### Bảng Phễu Chuyển Đổi Chi Tiết:

| Chặng trong Phễu | URL / Hành Vi | The Original Mr. Fuzzy (Sessions / CR %) | The Forever Love Bear (Sessions / CR %) |
| :--- | :--- | :---: | :---: |
| **1. Product** | `/products` | 231,191 (100.00%) | 231,191 (100.00%) |
| **2. Detail** | Trang chi tiết sản phẩm | 140,850 (**60.92%**) | 26,032 (**11.26%**) |
| **3. Cart** | `/cart` | 60,532 (**42.98%**) | 14,485 (**55.64%**) |
| **4. Thank** | `/thank-you-for-your-order` | 21,207 (**35.03%**) | 4,803 (**33.16%**) |

**So sánh chi tiết**:
- **Mức độ quan tâm ban đầu (Product -> Detail)**: **The Original Mr. Fuzzy** thu hút sự quan tâm áp đảo với **60.92%** lượt chuyển hướng từ danh mục sang trang chi tiết, gấp hơn 5 lần so với **11.26%** của **The Forever Love Bear**.
- **Khả năng kích thích mua hàng (Detail -> Cart)**: Khi người dùng đã vào trang chi tiết, **The Forever Love Bear** đạt tỷ lệ chuyển đổi vào giỏ hàng tốt hơn hẳn (**55.64%** so với **42.98%** của Mr. Fuzzy).
- **Tỷ lệ hoàn tất đơn hàng (Cart -> Thank)**: Cả hai sản phẩm đều gặp điểm nghẽn tại chặng thanh toán cuối cùng, chỉ chốt đơn thành công khoảng 1/3 số phiên vào giỏ hàng (**35.03%** cho Mr. Fuzzy và **33.16%** cho Love Bear).

---

## 💡 Đề Xuất Chiến Lược Kinh Doanh (Business Recommendations)

1. **Phân Bổ Ngân Sách Quảng Cáo Cân Bằng**:
   - Duy trì nguồn ngân sách chính cho **The Original Mr. Fuzzy** để bảo vệ dòng doanh thu chủ lực.
   - Trích một phần ngân sách chạy thử nghiệm (A/B testing) cho **The Forever Love Bear** nhằm tối ưu hóa trang danh mục và tăng tỷ lệ truy cập trang chi tiết, khai thác lợi thế chuyển đổi giỏ hàng cao (55.64%).

2. **Tối Ưu Hóa Các Điểm Nghẽn Trên Phễu**:
   - **Tối ưu chặng Detail -> Cart**: Cải thiện hình ảnh, mô tả sản phẩm và đánh giá người dùng trên trang chi tiết của **The Original Mr. Fuzzy** để tăng tỷ lệ thêm vào giỏ hàng từ 42.98% lên mức cao hơn.
   - **Cải thiện chặng Cart -> Thank (Checkout Process)**: Do 2/3 khách hàng bỏ giỏ hàng ở chặng cuối (chỉ ~33-35% hoàn thành đơn), cần rà soát lại quy trình thanh toán: chi phí vận chuyển phát sinh, tính dễ sử dụng của giao diện checkout, phương thức thanh toán và các chương trình ưu đãi bỏ giỏ hàng (abandoned cart recovery).

3. **Rà Soát Nguyên Nhân Hoàn Tiền**:
   - Tập trung phân tích dữ liệu phản hồi và lý do hoàn tiền của **The Birthday Sugar Panda** (6.04%) và **The Original Mr. Fuzzy** (5.11%) để xử lý triệt me vấn đề chất lượng hoặc sai lệch kỳ vọng sản phẩm.

---

## 🛠️ Công Nghệ & Thư Viện Sử Dụng

- **Ngôn ngữ**: Python 3.13.18
- **Xử lý & Phân tích dữ liệu**: `pandas` (v2.2.3), `numpy` (v2.4.6)
- **Trực quan hóa dữ liệu**: `matplotlib` (v3.10.0), `seaborn`
- **Môi trường thực thi**: Google Colab / Jupyter Notebook

---

## 🚀 Hướng Dẫn Chạy Notebook

1. **Mở Notebook**:
   - Mở file `Funnel Analysis.ipynb` trên Google Colab hoặc Jupyter Notebook.

2. **Cài đặt thư viện**:
   - Đảm bảo môi trường đã có `pandas`, `numpy`, `matplotlib`, `seaborn`.

3. **Thực thi**:
   - Chạy tuần tự tất cả các cell code trong notebook để tái hiện lại kết quả phân tích.

---

## 📜 Giấy Phép (License)

Dự án được phát triển phục vụ mục đích học tập và nghiên cứu phân tích dữ liệu E-commerce.
