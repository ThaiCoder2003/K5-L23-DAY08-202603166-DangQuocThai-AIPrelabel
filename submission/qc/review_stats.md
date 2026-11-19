# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 433; đối tượng sau duyệt: 451; ngưỡng accepted: IoU >= 0.9
- Đã **loại 24 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 418 | 418/433 = 96.5% | 433 box máy |
| edited (sửa) | 2 | 2/433 = 0.5% | 433 box máy |
| deleted (xoá) | 8 | 8/433 = 1.8% | 433 box máy |
| added (vẽ thêm) | 31 | 31/451 = 6.9% | 451 đối tượng sau duyệt |

Trong số deleted + added, **1 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 30.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 249 | 0 | 1 | 9 |
| bicycle | 8 | 0 | 1 | 3 |
| motorbike | 90 | 1 | 0 | 13 |
| car | 44 | 0 | 1 | 2 |
| bus | 3 | 0 | 0 | 1 |
| truck | 6 | 0 | 0 | 0 |
| three_wheeler | 18 | 1 | 5 | 3 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 3; đối tượng gold: 23 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 13 | 12 | 10 | 52.0% (KTC 95%: 33–70%) | 56.5% (KTC 95%: 37–74%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*

| Sau duyệt của bạn (2 ảnh gold có trong nhãn cuối) | 5 | 7 | 8 | 41.7% | 38.5% |

- **Residual** (lỗi của máy còn trong nhãn của bạn): 14 (đếm FP + FN; một lỗi sai lớp hoặc lệch vị trí tính 2)
- **New** (lỗi do bạn tạo ra): 1

## 3. Ảnh bẫy
- Bắt được 5/12 = 41.7% (KTC 95%: 19–68%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - loose: 1/3
  - missing: 1/3
  - extra: 2/3
  - wrong_class: 1/3
  - lọt: img_005.jpg (missing), img_028.jpg (extra), img_022.jpg (wrong_class), img_035.jpg (loose), img_010.jpg (missing), img_003.jpg (wrong_class), img_024.jpg (loose)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
