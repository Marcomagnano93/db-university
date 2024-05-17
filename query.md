1. Selezionare tutti gli studenti nati nel 1990 (160)

SELECT *
FROM `students`
WHERE `date_of_birth` BETWEEN '1990-01-01' AND '1991-01-01';

2. Selezionare tutti i corsi che valgono più di 10 crediti (479)

SELECT *
FROM `courses`
WHERE `cfu` > '10';

3. Selezionare tutti gli studenti che hanno più di 30 anni

SELECT *
FROM `students`
WHERE DATE_ADD(`date_of_birth`, INTERVAL 31 year) <= CURRENT_DATE;

oppure TIMESTAMPDIFF(YEAR, data 1, data 2)

4. Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di
laurea (286)

SELECT *
FROM `courses`
WHERE `period` = 'I semestre' AND `year` = '1';

5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del
20/06/2020 (21)

SELECT *
FROM `exams`
WHERE DATE("2020-06-20") = `date` AND TIME("14:00:00") < `hour`;

6. Selezionare tutti i corsi di laurea magistrale (38)

SELECT *
FROM `degrees`
WHERE `level` = 'magistrale';

7. Da quanti dipartimenti è composta l'università? (12)

SELECT COUNT(*) AS `number_of_departments`
FROM `departments`;

8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50)`

SELECT *
FROM `teachers`
WHERE `phone` IS NULL;




<!-- JOIN -->

1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia

SELECT `students`.`surname`, `students`.`name`, `students`.`degree_id`, `students`.`email`
FROM `students`
INNER JOIN `degrees`
ON `students`.`degree_id` = `degrees`.`id`
WHERE `degrees`.`name` = 'Corso di Laurea in Economia';

2. Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di
Neuroscienze

SELECT `degrees`.*, `departments`.`name` AS 'department_name'
FROM `degrees`
INNER JOIN `departments`
ON `degrees`.`department_id` = `departments`.`id`
WHERE `degrees`.`level` = 'magistrale'
AND `departments`.`name` = 'Dipartimento di Neuroscienze';

3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)

SELECT `courses`.`name` AS 'course_name'
FROM `courses`
INNER JOIN `course_teacher`
ON `course_teacher`.`course_id` = `courses`.`id`
INNER JOIN `teachers`
ON `course_teacher`.`teacher_id` = `teachers`.`id`
WHERE `teachers`.`id` = '44';

4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui
sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e
nome

SELECT `students`.`surname` AS 'student_surname', `students`.`name` AS 'student_name', `degrees`.*, `departments`.*
FROM `students`
INNER JOIN `degrees`
ON `students`.`degree_id` = `degrees`.`id`
INNER JOIN `departments`
ON `degrees`.`department_id` = `departments`.`id`
ORDER BY `student_surname`, `student_name`;

5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti

SELECT `degrees`.`name` AS 'degree', `courses`.`name` AS 'courses', `teachers`.`surname` AS 'teacher'
FROM `degrees`
INNER JOIN `courses`
ON `courses`.`degree_id` = `degrees`.`id`
INNER JOIN `course_teacher`
ON `course_teacher`.`course_id` = `courses`.`id`
INNER JOIN `teachers`
ON `course_teacher`.`teacher_id` = `teachers`.`id`;

6. Selezionare tutti i docenti che insegnano nel Dipartimento di
Matematica (54)

SELECT DISTINCT `teachers`.*, `departments`.`name` AS 'department_name'
FROM `teachers`
INNER JOIN `course_teacher`
ON `course_teacher`.`teacher_id` = `teachers`.`id`
INNER JOIN `courses`
ON `course_teacher`.`course_id` = `courses`.`id`
INNER JOIN `degrees`
ON `courses`.`degree_id` = `degrees`.`id`
INNER JOIN `departments`
ON `degrees`.`department_id` = `departments`.`id`
WHERE `departments`.`name` = 'Dipartimento di Matematica';

7. BONUS: Selezionare per ogni studente il numero di tentativi sostenuti
per ogni esame, stampando anche il voto massimo. Successivamente,
filtrare i tentativi con voto minimo 18

SELECT `students`.`surname` AS 'student_surname', `courses`.`name` AS 'course_name', COUNT(*) AS 'student_exam_aptent', MAX(`exam_student`.`vote`) AS 'max_vote'
FROM `students`
JOIN `exam_student`
ON `exam_student`.`student_id` = `students`.`id`
JOIN `exams`
ON `exam_student`.`exam_id` = `exams`.`id`
JOIN `courses`
ON `exams`.`course_id` = `courses`.`id`
GROUP BY `students`.`id`,  `courses`.`id`
HAVING `exam_student`.`vote` >= 18;


<!-- GROUP BY -->

1. Contare quanti iscritti ci sono stati ogni anno

SELECT COUNT(`students`.`id`) AS 'students', YEAR(`students`.`enrolment_date`) AS 'year'
FROM `students`
GROUP BY `year`;

2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio

SELECT COUNT(`teachers`.`id`) AS 'teachers', `teachers`.`office_address` AS 'address'
FROM `teachers`
GROUP BY `address`;

3. Calcolare la media dei voti di ogni appello d'esame

SELECT COUNT(`exams`.`id`), AVG(`exam_student`.`vote`) AS 'avarage_vote'
FROM `exams`
INNER JOIN `exam_student`
ON `exam_student`.`exam_id` = `exams`.`id`
GROUP BY `exams`.`id`;

4. Contare quanti corsi di laurea ci sono per ogni dipartimento

SELECT COUNT(`degrees`.`department_id`) AS 'degrees', `departments`.`name` AS 'department'
FROM `degrees`
INNER JOIN `departments`
ON `degrees`.`department_id` = `departments`.`id`
GROUP BY `degrees`.`department_id`;


