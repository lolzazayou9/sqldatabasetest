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
///////////////////////////////////////////////////////////////////////////////////////////////////////


1.)เพิ่มข้อมูล INSERT INTO student_insert_test (tin_fname, tin_lname, tin_team, tin_created_at)
VALUES ('jui', 'test', '1', CURRENT_DATE());

2.)อัปเดตข้อมูล 
update student_update_test 
set student_update_test.tup_fname = 'jui', student_update_test.tup_lname = 'maiz' , student_update_test.tup_updated_at = now()	
where student_update_test.tup_id = 18;

3.)รวมชื่อ fname กับ lname มาเป็น name
select concat(tup_fname," ",tup_lname) as fullName from student_update_test

4.)จำนวนอำเภอและตำบลในแต่ละจังหวัด
select provinces.pv_name_th , count(DISTINCT districts.dt_id),count(DISTINCT sub_districts.sdt_id)from provinces
JOIN districts on provinces.pv_id = districts.dt_pv_id
JOIN sub_districts on districts.dt_id = sub_districts.sdt_dt_id
group by provinces.pv_name_th

5.)การ join ข้าม database เพื่อตรวจสอบที่อยู่ของนิสิต
select concat(mock_reg_db.user_students.usst_fname, " " , mock_reg_db.user_students.usst_lname) as Namer, mock_reg_db.user_addresses.usa_building_number ,mock_reg_db.user_addresses.usa_building_name, mock_reg_db.user_addresses.usa_moo , mock_reg_db.user_addresses.usa_road, sub_districts.sdt_name_th , districts.dt_name_th ,provinces.pv_name_th,sub_districts.sdt_zip_code from provinces
JOIN districts on provinces.pv_id = districts.dt_pv_id
JOIN sub_districts on districts.dt_id = sub_districts.sdt_dt_id
join mock_reg_db.user_addresses on sub_districts.sdt_dt_id = mock_reg_db.user_addresses.usa_sdt_id
join mock_reg_db.user_students on mock_reg_db.user_addresses.usa_id = mock_reg_db.user_students.usst_usa_id

6.)ตรวจสอบห้องที่มีการเรียนการสอนในวันจันทร์สำหรับหลักสูตรที่กำหนด
select mock_reg_db.courses.cs_code , mock_reg_db.courses.cs_name , mock_reg_db.rooms.rm_name , mock_reg_db.courses_timetable.ct_time_start , mock_reg_db.courses_timetable.ct_time_end from mock_reg_db.courses
join mock_reg_db.courses_offered on mock_reg_db.courses.cs_id = mock_reg_db.courses_offered.co_cs_id
join mock_reg_db.courses_timetable on mock_reg_db.courses_offered.co_id = mock_reg_db.courses_timetable.ct_co_id
join mock_reg_db.rooms on mock_reg_db.courses_timetable.ct_rm_id = mock_reg_db.rooms.rm_id
where mock_reg_db.courses.cs_code ="CS101" or mock_reg_db.courses.cs_code ="CS102" or mock_reg_db.courses.cs_code ="CS201" or mock_reg_db.courses.cs_code ="CS202" or mock_reg_db.courses_timetable.ct_day = "Monday"	

7.) หลักสูตรที่เปิดใน 2024 และสอนในวันจันทร์
select mock_reg_db.courses.cs_code, mock_reg_db.courses.cs_name, mock_reg_db.curriculums.cl_name from mock_reg_db.courses
join mock_reg_db.curriculums on mock_reg_db.courses.cs_cl_id = mock_reg_db.curriculums.cl_id
join mock_reg_db.courses_offered on mock_reg_db.courses.cs_id = mock_reg_db.courses_offered.co_cs_id
join mock_reg_db.courses_timetable on mock_reg_db.courses_offered.co_id = mock_reg_db.courses_timetable.ct_co_id
join mock_reg_db.rooms on mock_reg_db.courses_timetable.ct_rm_id = mock_reg_db.rooms.rm_id
where mock_reg_db.courses_offered.co_year = 2024 or mock_reg_db.courses_timetable.ct_day = "Monday";

8.)แสดงรายชื่อนิสิตที่มีอาจารย์ที่ปรึกษา
คำอธิบาย: หานิสิตที่มีอาจารย์ที่ปรึกษาดูแล และแสดงว่ามีอาจารย์ที่ปรึกษากี่คน
SELECT 
	mock_reg_db.user_students.usst_id,
    CONCAT(mock_reg_db.user_students.usst_fname, " ",
    mock_reg_db.user_students.usst_lname) as full_name,
    count(mock_reg_db.student_advisors.sta_ussf_id) as Advisor_count
FROM
    mock_reg_db.user_students
join mock_reg_db.student_advisors on mock_reg_db.user_students.usst_id = mock_reg_db.student_advisors.sta_usst_id
group by mock_reg_db.user_students.usst_id

9.)หานิสิตที่ไม่มีอาจารย์ที่ปรึกษาดูแล  // left join คือการดึงข้อมูลจากตารางแรกมาทั้งหมดโดยไม่สนเงื่อนไขทั้งหมด
SELECT 
	mock_reg_db.user_students.usst_id,
    CONCAT(mock_reg_db.user_students.usst_fname, " ",
    mock_reg_db.user_students.usst_lname) as full_name,
    count(mock_reg_db.student_advisors.sta_ussf_id) as Advisor_count
FROM
    mock_reg_db.user_students
left join mock_reg_db.student_advisors on mock_reg_db.user_students.usst_id = mock_reg_db.student_advisors.sta_usst_id
group by mock_reg_db.user_students.usst_id
having Advisor_count = 0;

10)ค้นหาจำนวนนิสิตแยกตามหลักสูตร
คำอธิบาย: แสดงจำนวนนิสิตในแต่ละหลักสูตร
SELECT 
    mock_reg_db.curriculums.cl_name,
    count(mock_reg_db.courses_enrolled.ce_id)
FROM 
    mock_reg_db.curriculums
JOIN
	mock_reg_db.courses on mock_reg_db.curriculums.cl_id = mock_reg_db.courses.cs_cl_id
JOIN 
	mock_reg_db.courses_enrolled on mock_reg_db.courses.cs_id = mock_reg_db.courses_enrolled.ce_co_id
GROUP BY
	mock_reg_db.curriculums.cl_name

11.)ค้นหารายวิชาที่มีนิสิตลงทะเบียนมากกว่า 5 คน
คำอธิบาย: หารหัสวิชา และชื่อวิชาที่มีนิสิตลงทะเบียนมากกว่า 5 คน
SELECT 
    mock_reg_db.courses.cs_code,
    mock_reg_db.courses.cs_name,
    count(mock_reg_db.courses_enrolled.ce_id) as total_students
FROM 
    mock_reg_db.courses
JOIN
	mock_reg_db.courses_enrolled on mock_reg_db.courses.cs_id = mock_reg_db.courses_enrolled.ce_co_id
GROUP BY
	mock_reg_db.courses.cs_code
HAVING
	total_students > 5

12.)แสดงรายวิชาที่ไม่ได้เปิดในเทอมนี้แต่เปิดในปีที่แล้ว
คำอธิบาย: แสดงรหัสวิชา และชื่อวิชาที่ไม่ได้เปิดสอนในปีนี้ แต่เคยเปิดในปีที่แล้ว
SELECT 
    mock_reg_db.courses.cs_code,
    mock_reg_db.courses.cs_name
FROM 
    mock_reg_db.courses
JOIN
    mock_reg_db.courses_offered ON mock_reg_db.courses.cs_id = mock_reg_db.courses_offered.co_cs_id
WHERE
    mock_reg_db.courses_offered.co_year = YEAR(CURRENT_DATE) - 1
GROUP BY
    mock_reg_db.courses.cs_code

13.)แปลงเกรดจากตัวอักษรเป็นตัวเลข
คำอธิบาย: แสดงข้อมูล ชื่อ-สกุล พร้อมเกรดของนิสิตทุกคนที่เรียนในรายวิชา CS101 โดยเรียงลำดับจากเกรดมากสุดไปน้อยสุด พร้อมแปลงเกรดจาก A, B+, B, C+, C, D+, D เป็นค่าตัวเลข โดย  A = 4
    B+ = 3.5
    B = 3
    C+ = 2.5
    C = 2
    D+ = 1.5
    D = 1
    อื่นๆ = 0

SELECT 
    CONCAT(mock_reg_db.user_students.usst_fname, ' ', mock_reg_db.user_students.usst_lname) AS full_name,
    CASE 
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'A' THEN 4
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'B+' THEN 3.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'B' THEN 3
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'C+' THEN 2.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'C' THEN 2
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'D+' THEN 1.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'D' THEN 1
        ELSE 0
    END AS grade_point 
FROM 
    mock_reg_db.user_students
JOIN 
    mock_reg_db.courses_enrolled ON mock_reg_db.user_students.usst_id = mock_reg_db.courses_enrolled.ce_usst_id
JOIN
    mock_reg_db.courses_offered ON mock_reg_db.courses_enrolled.ce_co_id = mock_reg_db.courses_offered.co_id
JOIN
    mock_reg_db.courses ON mock_reg_db.courses_offered.co_cs_id = mock_reg_db.courses.cs_id
WHERE 
    mock_reg_db.courses.cs_code like 'CS101'

14.)แสดงรายชื่อนิสิตที่มีคะแนนเฉลี่ยมากกว่า 3.5
คำอธิบาย: แสดงรายชื่อนิสิตที่มีคะแนนเฉลี่ยมากกว่า 3.5 พร้อมเกรดเฉลี่ยที่ได้ (อ้างอิงการแปลงเกรดจากข้อ 13)
SELECT 
    CONCAT(mock_reg_db.user_students.usst_fname, ' ', mock_reg_db.user_students.usst_lname) AS full_name,
    CASE 
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'A' THEN 4
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'B+' THEN 3.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'B' THEN 3
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'C+' THEN 2.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'C' THEN 2
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'D+' THEN 1.5
        WHEN mock_reg_db.courses_enrolled.ce_grade = 'D' THEN 1
        ELSE 0
    END AS grade_point 
FROM 
    mock_reg_db.user_students
JOIN 
    mock_reg_db.courses_enrolled ON mock_reg_db.user_students.usst_id = mock_reg_db.courses_enrolled.ce_usst_id
JOIN
    mock_reg_db.courses_offered ON mock_reg_db.courses_enrolled.ce_co_id = mock_reg_db.courses_offered.co_id
JOIN
    mock_reg_db.courses ON mock_reg_db.courses_offered.co_cs_id = mock_reg_db.courses.cs_id
group by mock_reg_db.user_students.usst_id
having grade_point > 3.5

