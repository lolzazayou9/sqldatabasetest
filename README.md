// วิธี insert แบบ ไม่ต้อง select ใน sql
- INSERT INTO ชื่อตาราง (คอลัมน์1, คอลัมน์2, ... )
VALUES (ค่า1, ค่า2, ... );
- INSERT INTO employees (first_name, last_name, department, salary)
VALUES 
  ('John', 'Doe', 'HR', 50000),
  ('Alice', 'Johnson', 'Finance', 60000),
  ('Bob', 'Smith', 'IT', 55000);
  
// concat ธรรมดา 
- select concat(sdt_name_th," ",sdt_zip_code) as fullname from thailand_db.sub_districts
