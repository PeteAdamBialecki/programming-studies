# **SQL**

## **Reporting Examples**

What's Yvette Levy's student ID number?

        SELECT * FROM students WHERE last_name = "Levy" AND first_name = "Yvette";

Generate a list of teachers sorted alphabetically by last name.

        SELECT first_name, last_name FROM teachers
        ORDER BY last_name ASC;

Which students have last names starting with 'A'?

        SELECT * FROM students
        WHERE first_name LIKE 'A%';

## **Joining Examples**

What's the total capacity of the school?

        SELECT SUM(Capacity) FROM rooms;

WHich room has the largest capacity?

        SELECT id, Max(capacity) FROM rooms;

Which subjects are taught in room 19?

        SELECT DISTINCT name FROM classes
        JOIN subjects ON classes.subject_id = subjects.id
        WHERE room_id = 19;

Which teachers teach only students in 8th grade?
        SELECT DISTINCT teachers.id, first_name, last_name FROM teachers
        JOIN classes ON teachers.id = classes.teacher_id
        JOIN subjects ON subjects.id = classes.subject_id
        WHERE grade = 8;

Which teacher teaches 7th grade science?

        SELECT DISTINCT teachers.id, first_name, last_name FROM subjects
        JOIN classes ON subjects.id = classes.subject_id
        JOIN teachers ON teachers.id = classes.teacher_id
        WHERE name = "Science" AND grade = 7;

Which teachers teach elective subjects (subjects without grade levels)?

        SELECT DISTINCT teachers.first_name, teachers.last_name, subjects.name FROM teachers
        JOIN classes ON teachers.id = classes.teacher_id
        JOIN subjects ON subjects.id = classes.subject_id
        WHERE grade IS NULL;

## **Advanced Selecting**

Generate a schedule for Rex Rios.

        SELECT period_id, name, subjects.grade, class_id, teacher_id, room_id FROM students
        JOIN schedule ON schedule.student_id = students.id
        JOIN classes ON classes.id = schedule.class_id
        JOIN subjects ON subjects.id = classes.subject_id
        WHERE first_name = "Rex" AND last_name = "Rios"
        ORDER BY period_id ASC;

How many students have Physical Education during first period?

        SELECT first_name, last_name, student_id, period_id, name FROM students
        JOIN schedule ON schedule.student_id = students.id
        JOIN classes on classes.id = schedule.class_id
        JOIN subjects ON subjects.id= classes.subject_id
        WHERE period_id = 1 AND name = "Physical Education";

Generate a list of students with last names from A to M.

        SELECT * FROM students
        WHERE last_name >= "A" AND last_name < "N"
        ORDER BY last_name DESC;

Do they have room for that many 6th graders?

        SELECT MIN(capacity) FROM classes
        JOIN subjects ON subjects.id = classes.subject_id
        JOIN rooms ON rooms.id = classes.room_id
        WHERE grade = 6;

## **SQL Grouping**

Which teachers teach a class during all 7 periods?

        SELECT teachers.id, first_name, last_name, COUNT(*) FROM teachers
        JOIN classes ON classes.teacher_id = teachers.id
        GROUP BY teachers.id
        HAVING COUNT(*) = 7;

Do any teachers teach multiple subjects? If so, which teachers?

        SELECT teachers.* FROM teachers
        JOIN classes ON classes.teacher_id = teachers.id
        GROUP BY teachers.id HAVING COUNT(DISTINCT subject_id) > 1;

What class does Janis Ambrose teach during each period? Be sure to include all 7 periods in your report!

        WITH janis_classes AS (
            SELECT period_id, subjects.name FROM teachers
            JOIN classes ON classes.teacher_id = teachers.id
            JOIN subjects ON classes.subject_id = subjects.id
            WHERE first_name = "Janis" AND last_name = "Ambrose"
        )
        SELECT periods.id, janis_classes.name FROM periods
        LEFT OUTER JOIN janis_classes ON periods.id = period_id;

Which subject is the least popular, and how many students are taking it?

        WITH subject_counts AS (
            SELECT subjects.name, COUNT(1) 'CT' FROM subjects
            JOIN classes ON subjects.id = classes.subject_id
            JOIN schedule ON classes.id = schedule.class_id
            GROUP BY subject_id
        )
        SELECT name, MIN(ct) FROM subject_counts;

Which students have 5th period science and 7th period art?

        WITH fifth_science AS (
            SELECT student_id FROM students
            JOIN schedule ON students.id = schedule.student_id
            JOIN classes ON classes.id = schedule.class_id
            JOIN subjects ON subjects.id = classes.subject_id
            WHERE period_id = 5 AND subjects.name = "Science"
        ), seventh_art AS (
            SELECT student_id FROM students
            JOIN schedule ON students.id = schedule.student_id
            JOIN classes ON classes.id = schedule.class_id
            JOIN subjects ON subjects.id = classes.subject_id
            WHERE period_id = 7 AND subjects.name = "Art"
        )
        SELECT C.* FROM fifth_science A
        JOIN seventh_art B ON A.student_id = B.student_id
        JOIN students C ON A.student_id = C.id;

Which elective teacher is the most popular (teaches the most students)?

        WITH elective_teachers AS (
            SELECT DISTINCT TEACHERS.ID, FIRST_NAME, LAST_NAME
            FROM TEACHERS
            JOIN CLASSES ON TEACHERS.ID = CLASSES.TEACHER_ID
            JOIN SUBJECTS ON CLASSES.SUBJECT_ID = SUBJECTS.ID
            WHERE GRADE IS NULL
        ), student_counts AS (
            SELECT elective_teachers.id, COUNT(*) 'CT'
            FROM elective_teachers
            JOIN classes ON elective_teachers.id = classes.teacher_id
            JOIN schedule ON classes.id = schedule.class_id
            GROUP BY elective_teachers.id
        )
        SELECT MAX(student_counts.ct), teachers.first_name, teachers.last_name
        FROM student_counts
        JOIN teachers ON student_counts.id = teachers.id;

Which teachers don't have a class during 1st period?

        SELECT teachers.* FROM teachers

        EXCEPT

        SELECT teachers.* FROM teachers
        JOIN classes ON teachers.id = classes.teacher_id
        WHERE period_id = 1;