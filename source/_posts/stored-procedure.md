---
title: stored procedure
date: 2019-01-03 15:40:21
tags: mysql
---
**下面是之前写的支付流程的存储过程,通过触发器来触发的,注释部分用到的是cursor,cursor这个东西不错,简单来说就是可以通过将查询结果赋值给一个变量 ，然后在另一个sql中去引用这个变量,其实个人觉得最好不要:**

	```
	begin
	DECLARE currentTime INT DEFAULT UNIX_TIMESTAMP();
	DECLARE update_count INT DEFAULT 0;
	DECLARE update_members INT DEFAULT 0;
	DECLARE _event_id INT DEFAULT 0;
	DECLARE _event_coin INT DEFAULT 0;
	DECLARE	_event_rebate INT DEFAULT 0;
	DECLARE	_user_coin DECIMAL(10,3);
	DECLARE	info_str VARCHAR(255);
	DECLARE  no_more_record INT DEFAULT 0;
	
	#declare cur_yb cursor for
	#SELECT id, coin, rebate FROM blast_events_fanxian WHERE `coin` <= _rechargeAmount order by coin desc LIMIT 1;
	#DECLARE  CONTINUE HANDLER FOR NOT FOUND  SET  no_more_record = 1;
	
	#open cur_yb;
	#WHILE no_more_record != 1 DO
	#fetch cur_yb into _event_id, _event_coin, _event_rebate;
	
	#END WHILE;
	#close cur_yb;
	
	IF _rechargeAmount IS NULL THEN
		SET _rechargeAmount=0;
	END IF;
	IF _coin IS NULL THEN
		SET _coin=0;
	END IF;
	IF _fcoin IS NULL THEN
		SET _fcoin=0;
	END IF;
  	IF _status IS NULL THEN
		SET _status=0;
	END IF;
	IF(_status>0)THEN
		UPDATE blast_member_recharge SET rechargeAmount = _rechargeAmount, state = 1, info = '自动充值', rechargeTime = currentTime, actionIP = _actionIP, coin = _coin, fcoin = _fcoin WHERE `id` = _member_recharge_id;	
		SELECT ROW_COUNT() INTO update_count;
		IF(update_count>0)THEN
			call setCoin(_rechargeAmount, 0, _userid, 1, 0, '充值', _member_recharge_id, _rechargeId, '');//此处为另一个存储过程
			#IF(_rechargeAmount >= _event_coin AND _event_coin >0 )THEN
				#UPDATE blast_members SET coin = coin + _event_rebate, rebate_sum = rebate_sum + _event_rebate  WHERE `uid` = _userid;
				#SELECT ROW_COUNT() INTO update_members;
				#IF(update_members>0)THEN
					#SELECT coin INTO _user_coin FROM blast_members WHERE `uid` = _userid;
					#SET @info_str = CONCAT('充值',_rechargeAmount,'元赠送',_event_rebate,'元');
					#INSERT INTO blast_coin_log(rebate, userCoin, uid, liqType, actionTime, info, extfield0, extfield1, extfield2) VALUES(_event_rebate, _user_coin, _userid, 151, currentTime, @info_str, _member_recharge_id, _rechargeId, _event_id);
				#END IF;
			#END IF;
			
			UPDATE blast_members SET deposit_sum = deposit_sum + _rechargeAmount, deposit_count = deposit_count + 1  WHERE `uid` = _userid ;		
			INSERT INTO blast_admin_log(type, actionTime, actionIP, action, extfield0, extfield1) VALUES(2, currentTime, _actionIP, _rechargeId, _userid, _username);			
		END IF;
	END IF;
	end

	//以下为传入存储过程的相应参数:
	_member_recharge_id int, _userid int, _rechargeAmount float,  _rechargeTime int, _rechargeId int, _actionIP varchar(20) character set utf8, _coin float, _fcoin float, _username varchar(255) character set utf8, _status int

	```