# sql 语法 - 存储过程

```sql
CREATE DEFINER=`root`@`%` PROCEDURE `proce`()
BEGIN
	#Routine body goes here...
	DECLARE uid INT;
	DECLARE did INT;
	DECLARE rid INT;
	DECLARE rid1 INT;
	DECLARE done int;

	DECLARE cur CURSOR FOR SELECT id,dep_id,role_id from user WHERE dep_id in (SELECT id FROM department WHERE group_id = 10);
	
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET done=1;
	
	OPEN cur;
	
	posLoop:LOOP
	IF done = 1 THEN
		LEAVE posLoop;
	END IF;
	
	FETCH cur INTO uid,did,rid;
	
	SET @rid1=(SELECT role_id FROM user WHERE id = uid);
	
	IF @rid1 > 150 THEN
	  SET @rid1=(CASE rid
        WHEN 170 THEN 70
        WHEN 171 THEN 80
	  END);
	  UPDATE user SET role_id = @rid1 WHERE id = uid;
	END IF;
	
	END LOOP posLoop;
	
	CLOSE cur;
END
```
