## Bài tập 5
##### SUBJECT: Trigger on mssql
A. Trình bày lại đầu bài của đồ án PT&TKHT:
1. Mô tả bài toán của đồ án PT&TKHT, 
   đưa ra yêu cầu của bài toán đó
2. Cơ sở dữ liệu của Đồ án PT&TKHT :
   Có database với các bảng dữ liệu cần thiết (3nf),
   Các bảng này đã có PK, FK, CK cần thiết
 
B. Nội dung Bài tập 05:
1. Dựa trên cơ sở là csdl của Đồ án
2. Tìm cách bổ xung thêm 1 (hoặc vài) trường phi chuẩn
   (là trường tính toán đc, nhưng thêm vào thì ok hơn,
    ok hơn theo 1 logic nào đó, vd ok hơn về speed)
   => Nêu rõ logic này!
3. Viết trigger cho 1 bảng nào đó, 
   mà có sử dụng trường phi chuẩn này,
   nhằm đạt được 1 vài mục tiêu nào đó.
   => Nêu rõ các mục tiêu 
4. Nhập dữ liệu có kiểm soát, 
   nhằm để test sự hiệu quả của việc trigger auto run.
5. Kết luận về Trigger đã giúp gì cho đồ án của em.
### Bài làm:
##### 1. bài toán:
   xây dựng hệ thống trông giữ xe
   
##### 2. Cơ sở dữ liệu:
###### Các bảng, khóa chính, khóa ngoại
![1](https://github.com/user-attachments/assets/2c2fcd74-fa41-47ec-a732-cddcb76483e9)
###### Nhập dữ liệu vào các bảng
![b1](https://github.com/user-attachments/assets/15f6fc6b-6111-4b3d-9c16-002e90a0010e)
![b2](https://github.com/user-attachments/assets/c0d3e4d3-b916-4379-832b-36c69b31fa32)
![image](https://github.com/user-attachments/assets/7fe25768-ac8d-412e-89a4-f92dee9fc6df)
###### Thêm trường sl xe và sl trong vào bảng ô
![sua bang o](https://github.com/user-attachments/assets/4ab5f1a5-59ef-48a6-af6c-41ed8f871b1e)

##### Trigger:
###### tạo Trigger tự động tính số lượng xe trông ô
###### Trigger chạy khi nhập bản ghi cho bảng Vitrixe
```sql
CREATE TRIGGER TR_AfterInsertViTriXe
ON dbo.vitrixe
AFTER INSERT
AS
BEGIN
    UPDATE o
    SET
        sl_xe = (SELECT COUNT(*) FROM dbo.vitrixe WHERE ma_o = inserted.ma_o),
        sl_trong = 6 - (SELECT COUNT(*) FROM dbo.vitrixe WHERE ma_o = inserted.ma_o)
    FROM o
    INNER JOIN inserted ON o.ma_o = inserted.ma_o;
END;
GO

CREATE TRIGGER TR_AfterDeleteViTriXe
ON dbo.vitrixe
AFTER DELETE
AS
BEGIN
    UPDATE o
    SET
        sl_xe = o.sl_xe - (SELECT COUNT(*) FROM deleted WHERE deleted.ma_o = o.ma_o),
        sl_trong = o.sl_trong + (SELECT COUNT(*) FROM deleted WHERE deleted.ma_o = o.ma_o)
    FROM o
    INNER JOIN deleted ON o.ma_o = deleted.ma_o;
END;
GO
```
##### chạy thử:
###### nhập giá trị cho bảng
![nhap gtri thu](https://github.com/user-attachments/assets/a05164fa-50cb-417b-810d-9ebec8742dde)
###### kết quả:
![nhap gtri thu - trigger hd](https://github.com/user-attachments/assets/bdc2b0f2-ccd5-449d-86f3-0f451eb165d1)
###### chạy thử lần 2:
![nhap gtri thu 2](https://github.com/user-attachments/assets/acf16415-e679-4c5f-981b-fdeea98d2fdb)
![nhap gtri thu - trigger hd 2](https://github.com/user-attachments/assets/74406ee7-0885-46ce-91a8-24401a7aa3fa)

##### Kết luận: trigger hoạt động, giúp tự động tính toán số lượng xe từ các phần nhỏ, từ đó giúp tính tổng số xe
