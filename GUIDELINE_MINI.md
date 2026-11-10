# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con dùng để chở người, gồm sedan, SUV, taxi và xe bán tải. | Không gồm xe tải, xe buýt hoặc xe máy. Pickup thuộc `car`. |
| `truck` | Xe tải chở hàng hoặc xe đầu kéo. | Thường có thùng hàng, cabin tách rõ; không nhầm với pickup. |
| `bus` | Xe buýt hoặc minibus chủ yếu chở hành khách. | Thân xe lớn/dài, nhiều cửa sổ; không phải van. |
| `van` | Xe van hoặc minivan chở người hay hàng. | Nhỏ hơn bus, thân xe kín/liền khối hơn truck; không phải xe con thông thường. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: vẽ sát phần thân xe nhìn thấy được, không lấy thêm nền, mặt đường hoặc xe bên cạnh.
- Vật thể bị che khuất: vẫn gán một box cho phần còn nhìn thấy nếu có thể xác định chắc lớp xe.
- Vật thể bị cắt bởi rìa ảnh: box kéo đến đúng rìa ảnh; không đoán hay vẽ phần xe nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: không gán nếu không thể nhận diện chắc lớp; ghi lại để hỏi giảng viên.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | Một xe bị cột điện che mất một phần thân. | Gán `car` với box quanh phần thấy được. | Hình dáng xe con vẫn đủ rõ; không đoán phần bị che. |
| `drive_014` | Một xe bị cắt ở mép ảnh. | Gán `truck`, box chạm mép ảnh. | Chỉ ôm phần xe nằm trong ảnh. |
| `drive_030` | Nhiều phương tiện rất nhỏ và nhòe ở xa. | Không gán. | Không xác định chắc class trong bốn lớp. |
