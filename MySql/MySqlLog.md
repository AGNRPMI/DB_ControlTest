## Задание
6. Нарисовать диаграмму, в которой есть класс родительский класс, домашние
животные и вьючные животные, в составы которых в случае домашних
животных войдут классы: собаки, кошки, хомяки, а в класс вьючные животные
войдут: Лошади, верблюды и ослы).
![картинка](/MySql/img/01.png)
![картинка](/MySql/img/02.png)
![картинка](/MySql/img/03.png)

1. В подключенном MySQL репозитории создать базу данных "Друзья человека”

```sql
CREATE DATABASE friendsofhuman;
```

8. Создать таблицы с иерархией из диаграммы в БД

```sql
USE friendsofhuman;
CREATE TABLE `animal` (
	`id` INT(5) NOT NULL AUTO_INCREMENT,
	`type` ENUM('pet', 'burden'),
	PRIMARY KEY (`id`)
);

CREATE TABLE `pet` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `animal` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `burden` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `animal` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `dog` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	`date` DATE NULL,
	`commands` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `pet` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `cat` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	`date` DATE NULL,
	`commands` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `pet` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `hamster` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	`date` DATE NULL,
	`commands` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `pet` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `horse` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	`date` DATE NULL,
	`commands` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `burden` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `camel` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	`date` DATE NULL,
	`commands` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `burden` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `donkey` (
	`id` INT(5) NULL,
	`name` TEXT NULL,
	`date` DATE NULL,
	`commands` TEXT NULL,
	FOREIGN KEY (`id`) REFERENCES `burden` (`id`) ON UPDATE CASCADE ON DELETE CASCADE
);
```


9. Заполнить низкоуровневые таблицы именами(животных), командами которые они выполняют и датами рождения

```sql
INSERT INTO friendsofhuman.animal (`type`) VALUES ('pet');
INSERT INTO friendsofhuman.pet (`id`, `name`) VALUES (LAST_INSERT_ID(), 'dog');
INSERT INTO friendsofhuman.`dog` (`id`, `name`, `date`, `commands`) VALUES (LAST_INSERT_ID(), 'pesel', '2020-01-01', 'bark');

INSERT INTO friendsofhuman.animal (`type`) VALUES ('pet');
INSERT INTO friendsofhuman.pet (`id`, `name`) VALUES (LAST_INSERT_ID(), 'cat');
INSERT INTO friendsofhuman.`cat` (`id`, `name`, `date`, `commands`) VALUES (LAST_INSERT_ID(), 'kisulya', '2021-01-01', 'purr');

INSERT INTO friendsofhuman.animal (`type`) VALUES ('pet');
INSERT INTO friendsofhuman.pet (`id`, `name`) VALUES (LAST_INSERT_ID(), 'hamster');
INSERT INTO friendsofhuman.`hamster` (`id`, `name`, `date`, `commands`) VALUES (LAST_INSERT_ID(), 'Homyak Bozhiy Rastropovich', '2024-04-15', 'submit to the award');

INSERT INTO friendsofhuman.animal (`type`) VALUES ('burden');
INSERT INTO friendsofhuman.burden (`id`, `name`) VALUES (LAST_INSERT_ID(), 'horse');
INSERT INTO friendsofhuman.`horse` (`id`, `name`, `date`, `commands`) VALUES (LAST_INSERT_ID(), 'bucephalus', '2022-12-12', 'jump on an elephant');

INSERT INTO friendsofhuman.animal (`type`) VALUES ('burden');
INSERT INTO friendsofhuman.burden (`id`, `name`) VALUES (LAST_INSERT_ID(), 'camel');
INSERT INTO friendsofhuman.`camel` (`id`, `name`, `date`, `commands`) VALUES (LAST_INSERT_ID(), 'vasya', '2010-01-01', 'spit');

INSERT INTO friendsofhuman.animal (`type`) VALUES ('burden');
INSERT INTO friendsofhuman.burden (`id`, `name`) VALUES (LAST_INSERT_ID(), 'donkey');
INSERT INTO friendsofhuman.`donkey` (`id`, `name`, `date`, `commands`) VALUES (LAST_INSERT_ID(), 'moisey', '2023-01-01', 'carrot');

```


10.  Удалив из таблицы верблюдов, т.к. верблюдов решили перевезти в другой питомник на зимовку. Объединить таблицы лошади, и ослы в одну таблицу.

```sql
DELETE FROM `animal` WHERE id IN (SELECT id FROM `camel`);
SELECT * FROM `horse`
UNION
SELECT * FROM `donkey`;
```

11.   Создать новую таблицу “молодые животные” в которую попадут все
животные старше 1 года, но младше 3 лет и в отдельном столбце с точностью
до месяца подсчитать возраст животных в новой таблице

```sql
CREATE TEMPORARY TABLE `young_animal` (
	`id` INT(11) NOT NULL AUTO_INCREMENT,
	`type_an` ENUM('dog', 'cat', 'hamster', 'horse', 'donkey'),
	`name` TEXT NULL,
	`age` DECIMAL(5,2),
	PRIMARY KEY (`id`)
);

INSERT INTO `young_animal` (`type_an`, `name`, `age`)
SELECT type_an, name, TIMESTAMPDIFF(MONTH,`date`,CURDATE()) AS age FROM 
    (SELECT 'dog' as type_an, `name`, `date` FROM `dog`
     UNION ALL SELECT 'cat', `name`, `date` FROM `cat`
     UNION ALL SELECT 'hamster', `name`, `date` FROM `hamster`
     UNION ALL SELECT 'horse', `name`, `date` FROM `horse`     
     UNION ALL SELECT 'donkey', `name`, `date` FROM `donkey`
    ) AS animal
WHERE `date` BETWEEN DATE_SUB(NOW(), INTERVAL 3 YEAR) AND DATE_SUB(NOW(), INTERVAL 1 YEAR);

SELECT * FROM young_animal;
```

12.  Объединить все таблицы в одну, при этом сохраняя поля, указывающие на
прошлую принадлежность к старым таблицам.

```sql
SELECT 
    a.id AS animal_id,
    a.type AS animal_type,
    p.id AS pet_id,
    p.name AS pet_name,
    b.id AS burden_id,
    b.name AS burden_name,
    d.id AS dog_id,
    d.name AS dog_name,
    d.date AS dog_date,
    d.commands AS dog_commands,
    c.id AS cat_id,
    c.name AS cat_name,
    c.date AS cat_date,
    c.commands AS cat_commands,
    h.id AS hamster_id,
    h.name AS hamster_name,
    h.date AS hamster_date,
    h.commands AS hamster_commands,
    ho.id AS horse_id,
    ho.name AS horse_name,
    ho.date AS horse_date,
    ho.commands AS horse_commands,
    ca.id AS camel_id,
    ca.name AS camel_name,
    ca.date AS camel_date,
    ca.commands AS camel_commands,
    do.id AS donkey_id,
    do.name AS donkey_name,
    do.date AS donkey_date,
    do.commands AS donkey_commands
FROM animal a
LEFT JOIN pet p ON a.id = p.id
LEFT JOIN burden b ON a.id = b.id
LEFT JOIN dog d ON p.id = d.id
LEFT JOIN cat c ON p.id = c.id
LEFT JOIN hamster h ON p.id = h.id
LEFT JOIN horse ho ON p.id = ho.id
LEFT JOIN camel ca ON p.id = ca.id
LEFT JOIN donkey do ON p.id = do.id;



