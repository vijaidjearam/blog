---
layout: post
date: 2023-05-10 12:14:00
title: 2023-05-10-GLPI_sql_query_for_metabase
category: Glpi
tags: glpi inventory metabase
---

SELECT glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle FROM `glpi_computers`
LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id
WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2))
ORDER BY glpi_computers.name ASC
LIMIT 2;


SELECT glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle FROM `glpi_computers`
LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id
WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2))

GROUP BY Age
ORDER BY COUNT(glpi_computers.id) DESC, Salle ASC,Age DESC, Batiment ASC;




CREATE TEMPORARY TABLE TMP  
SELECT glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle 
FROM `glpi_computers`
LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id
WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2));

SELECT
        Salle,
    COUNT(CASE WHEN Age= '9' THEN name ELSE NULL END) AS 9
FROM  TMP
GROUP BY  
        Salle; 
        
        
CREATE TEMPORARY TABLE TMP         
SELECT glpi_computers.id, glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle, MID(glpi_computers.name,8,5) AS Salle1 
FROM `glpi_computers`
LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id
WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2));

SELECT
        Salle1,
    COUNT(CASE WHEN Age= '9' THEN id ELSE NULL END) AS '9',
    COUNT(CASE WHEN Age= '8' THEN id ELSE NULL END) AS '8',
    COUNT(CASE WHEN Age= '7' THEN id ELSE NULL END) AS '7',
    COUNT(CASE WHEN Age= '6' THEN id ELSE NULL END) AS '6',
    COUNT(CASE WHEN Age= '5' THEN id ELSE NULL END) AS '5',
    COUNT(CASE WHEN Age= '4' THEN id ELSE NULL END) AS '4',
    COUNT(CASE WHEN Age= '3' THEN id ELSE NULL END) AS '3',
    COUNT(CASE WHEN Age= '2' THEN id ELSE NULL END) AS '2',
    COUNT(CASE WHEN Age= '1' THEN id ELSE NULL END) AS '1',
    COUNT(CASE WHEN Age= '0' THEN id ELSE NULL END) AS '0',
    COUNT(id) AS Total
FROM  TMP
GROUP BY  
        Salle1; 
        



CREATE TEMPORARY TABLE TMP         
SELECT glpi_computers.id, glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle, MID(glpi_computers.name,8,5) AS Salle1 
FROM `glpi_computers`
LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id
WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2)); 


SET @sql = NULL;
SELECT
GROUP_CONCAT(
DISTINCT CONCAT(
  'COUNT(
  CASE WHEN Age = ',Age,' THEN id ELSE NULL END) 
  AS ','`',Age,'`')
)
INTO @sql
FROM TMP;

 
SET @sql = CONCAT('SELECT Salle1,', @sql,' FROM TMP GROUP BY Salle1');
SELECT @sql;
 
PREPARE stmt FROM @sql;
EXECUTE stmt;
DEALLOCATE PREPARE stmt;

------------------------------------------------------------------------------------------------------------------------------------
CREATE TEMPORARY TABLE TMP         
SELECT glpi_computers.id, glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle, MID(glpi_computers.name,8,5) AS Salle1 
FROM `glpi_computers`
LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id
WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2)); 


SET @sql = NULL;
SELECT
GROUP_CONCAT(
DISTINCT CONCAT(
  'COUNT(
  CASE WHEN Age = ',Age,' THEN id ELSE NULL END) 
  AS ','`',Age,'`')
)
INTO @sql
FROM TMP;


SET @sql = CONCAT('CREATE TEMPORARY TABLE TMP1 SELECT Salle1,', @sql,' FROM TMP GROUP BY Salle1');
SELECT @sql;
PREPARE stmt FROM @sql;
EXECUTE stmt;
DEALLOCATE PREPARE stmt;
SELECT * FROM TMP1

------------
```SQL
CREATE TEMPORARY TABLE TMP
SELECT glpi_computers.id, glpi_computers.name, glpi_infocoms.warranty_date, TIMESTAMPDIFF(YEAR, glpi_infocoms.warranty_date, CURDATE()) AS Age, MID(glpi_computers.name,8,1) AS Batiment, MID(glpi_computers.name,9,4) AS Salle, MID(glpi_computers.name,8,5) AS Salle1 FROM glpi_computers LEFT JOIN glpi_infocoms ON glpi_computers.id = glpi_infocoms.items_id WHERE ((glpi_computers.name LIKE '028F2%') AND (glpi_computers.states_id=2));

SET @sql = NULL; SELECT GROUP_CONCAT( DISTINCT CONCAT( 'COUNT( CASE WHEN Age = ',Age,' THEN 1 ELSE NULL END) AS ','`',Age,'`') ) INTO @sql FROM TMP;

SET @sql = CONCAT('CREATE TEMPORARY TABLE TMP1 SELECT Salle1,', @sql,' FROM TMP GROUP BY Salle1'); SELECT @sql; PREPARE stmt FROM @sql; EXECUTE stmt; DEALLOCATE PREPARE stmt; SELECT * FROM TMP1
```
