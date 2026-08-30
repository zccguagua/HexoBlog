---
title: SQL语法
date: 2018-10-07 23:38:03
tags: 
        - SQL
---


![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/girl_smile.jpg "Image")


### 简单查询
```
<resultMap id="login" type="com.abc.app.model.LoginBean"></resultMap>

<select id="searchLogin" parameterType="Integer" resultMap="login">
	select * from identify where idcard=#{idcard};
</select>

#查询总数
SELECT COUNT(*) FROM inpatient

```
### 过滤查询
key不为空+模糊查询+limit
```
select `code`,`name`,abbr,sex,birthday,checkin_date,checkout_date from inpatient
    <where>
	    <if test="key != null">
		`code` LIKE concat('%',#{key},'%') 
	    </if>
    </where>
	ORDER BY checkin_date desc limit #{cp},10;
```


### 批量添加
```
	<insert id="insertInpatient" parameterType="com.abc.app.model.inpatient">
    		insert into `inpatient`
    		(`code`,`name`,abbr,sex,birthday,checkin_date,checkout_date)values
    		<foreach collection="list" item="item1" index="index1"
    			separator=",">
    			(#{item1.code},#{item1.name},null,#{item1.sex},#{item1.birthday},DATE_SUB(now(),INTERVAL
    			10
    			DAY),null)
    		</foreach>
    	</insert>
 ```
 
 ### 删除
 ```
 <delete id="deleteInpation" parameterType="String">
 		delete from
 		inpatient where `code`=#{code};
 	</delete>
 ```
 ### 修改
 ```
 	<update id="changeInpation" parameterType="Map">
 		update inpatient
 		set
 		checkout_date=now(),last_modify=now(),nurse=#{nurse}
 		where
 		`code`=#{code};
 	</update>
 ```
 
 ### 更新1-54随机数
 ```
 	<update id="updateRandom" parameterType="String">
 		update web_dict set
 		random = (Select Round(Rand()*(53)+1,0)) where id=1;
 	</update>
 ```
 
### 查找过去七天的登陆数
 为解决日期登陆数为空时不显示0条数据问题
 
 ```
    CREATE TABLE num (i int);-- 创建一个表用来储存0-9的数字
    INSERT INTO num (i) VALUES (0), (1), (2), (3), (4), (5), (6), (7), (8), (9);-- 生成0-9的数字，方便以后计算时间 
    
    CREATE TABLE  if not exists calendar(day_by_day date); -- 生成一个存储日期的表，datalist是字段名 -- 这里是生成并插入日期数据 
    INSERT INTO calendar(day_by_day) SELECT
        adddate(
            ( 
                DATE_FORMAT("2018-9-1", '%Y-%m-%d') 
            ),
            numlist.id
        ) AS `date`
    FROM
        (
            SELECT
                n1.i + n10.i * 10 + n100.i * 100 + n1000.i * 1000+ n10000.i * 10000 AS id
            FROM
                num n1
            CROSS JOIN num AS n10
            CROSS JOIN num AS n100
            CROSS JOIN num AS n1000
            CROSS JOIN num AS n10000
        ) AS numlist;
  ```
  
  ```
    	<select id="selectDayCount" parameterType="String"
    		resultType="com.abc.app.model.Day_count">
    		select day_by_day ,a.count from calendar
    		left JOIN (select
    		date_format(time,'%Y-%m-%d') as
    		day_time, count(1) as count
    		from
    		login_info
    		<![CDATA[
    		where time >= date(now()) - interval 7 day group by
    		day(time)) as a
    		on calendar.day_by_day = a.day_time
    		where day_by_day >=
    		CURDATE() - INTERVAL 6 DAY
    		and day_by_day <=CURDATE()
    		]]>
    		ORDER BY
    		day_by_day asc ;
    	</select>
  ```
    
 ### 查找今天的信息个数
 
 ```
   <select id="selectTodaySMSCount" parameterType="String"
    		resultType="int">
    		select COUNT(1)as count FROM web_sms where
    		phone=#{phone} and
    		to_days(time) = to_days(now());
   </select>
 ```