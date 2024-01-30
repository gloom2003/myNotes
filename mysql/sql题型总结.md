# sql题型总结

### (1) 求排名问题（第一名、倒数第三名...）:

#### 1 求总分最高的学生信息的及总分

视频讲解：https://www.bilibili.com/video/BV1UH4y1m7uP/?spm_id_from=333.788.recommend_more_video.12&vd_source=d6367c1fc21883823f1fb738f86ef26e

~~~sql
-- 求总分最高的学生信息的及总分(经典)

-- 思路分析: 应该当作总分最高的学生不止一人，先求出总分最高的分数，再查询出分数等于最高总分的学生信息

select st.s_id,st.s_name,st.s_sex,st.s_birth,a.sum_score
from student st right join (
     -- 子2:查询 总分最高的学生id 与 分数
	select sc.s_id,sum(sc.s_score) sum_score
	from score sc
	group by sc.s_id having sum(sc.s_score) = (
                                                     -- 子1:查询 总分最高的分数
                                                    select sum(sc.s_score) sum_score
                                                    from score sc
                                                    group by sc.s_id
                                                    order by sum_score desc
                                                    limit 0,1
											)
						  ) a on a.s_id = st.s_id

~~~

#### 2 求语文单科成绩最高的学生的信息与分数

同理，也是一样的做法（不要认为分数最高永远只有一人即可）:

~~~sql
-- 求语文单科成绩最高的学生的信息与分数

-- 思路：首先求出语文单科的最高分数，然后查询语文分数等于最高分数的学生信息

-- 结果:
select a.s_id '学号',st.s_name '姓名',st.s_sex '性别',st.s_birth '出生日期',a.s_score '分数'
from (
	select sc.s_id,sc.s_score
	from score sc
	where sc.s_score = 
    (
        -- 查询语文单科的最高分数
        select max(sc.s_score) max_score
        from score sc
        where sc.c_id = 
        (
            select c.c_id
            from course c
            where  c.c_name = '语文'
        )
	) 
    and sc.c_id = (
                    select c.c_id
                    from course c
                    where  c.c_name = '语文'
				 ) -- 子3与学生表进行连接
	) a left join student st on a.s_id = st.s_id


~~~

#### 3 求每门课程单科成绩最高的学生的信息与分数

解法1：常规解法

~~~sql
-- 求每门课程单科成绩最高的学生的信息与分数

-- 思路：与求单科类似：首先求出语文单科的最高分数，然后查询语文分数等于最高分数的学生信息，只是数量变多了。
-- 求出每门课程的最高分数，然后查询课程分数等于最高分数的学生信息

-- 结果：查询每门课程的最高分数以及学生的信息
select c.c_name '课程名称',st.s_name '学生姓名',a.max_score '最高分'
from (
         -- 子2: 查询每门课程的最高分数以及学生的id
        select sc.s_id,a.c_id,a.max_score
        from score sc right join (
                                         -- 子1：查询每门课程的最高分数
                                        select sc.c_id,max(sc.s_score) max_score
                                        from score sc
                                        group by sc.c_id 
                                  ) a on sc.c_id = a.c_id and sc.s_score = a.max_score
	) a 
left join student st on a.s_id = st.s_id 
left join course c on a.c_id = c.c_id
~~~

解法2：使用窗口函数

~~~mysql
-- 求每门课程单科成绩最高的学生的信息与分数

-- 写法1：
with temp as
(
    -- 子1：给score表按照c_id进行排名
    select s_id,c_id,s_score,rank() over(PARTITION by c_id order by s_score desc) num
    from score
) 
select c.c_name '课程',st.s_name '姓名',t.s_score '最高分'
from temp t left join student st on t.s_id = st.s_id
left join course c on t.c_id = c.c_id
where t.num = 1

-- 写法1相当于：
select c.c_name '课程',st.s_name '姓名',t.s_score '最高分'
from (
    	-- 子1：给score表按照c_id进行排名
        select s_id,c_id,s_score,rank() over(PARTITION by c_id order by s_score desc) num
        from score
	 ) t 
left join student st on t.s_id = st.s_id
left join course c on t.c_id = c.c_id
where t.num = 1
~~~



#### 4 求总分前3的学生分数与学生姓名(大厂)

写法1：先查询出使用学生的信息，再过滤不需要的信息，一个子查询+join 

~~~mysql
-- 思路：老样子，应该当作总分前3不止3人！查询总分第3的分数，使用 having sum(score) >= 总分第3的分数; 即可过滤出总分前3的学生,最后再连表求其他信息即可。

-- 结果1：
select sc.s_id '学号',st.s_name '姓名',sum(s_score) '总分'
from score sc 
left join student st on sc.s_id = st.s_id
group by sc.s_id,st.s_name having sum(s_score) >= 
(
    -- 子1：查询总分第3的分数
    select distinct sum(s_score) sum_score -- distinct sum(s_score)表示从总分的结果中去重,
    from score 
    group by s_id
    order by sum_score desc
    limit 2,1
)
order by 总分 desc
~~~

写法2：先查出需要的学生id再查询其他信息 ,两个子查询 + join (连接时总分表这边的行数少了很多)

~~~mysql
-- 结果2：
select st.s_name '姓名',a.sum_score '总分数'
from 
(	
    -- 子2：总分前3的学生id与分数
	select sc.s_id,sum(s_score) sum_score
	from score sc
	group by sc.s_id having sum(s_score) >= 
	(
        -- 子1：查询总分第3名的分数
		select sum(s_score) sum_score
		from score
		group by s_id
		order by sum_score desc
		limit 2,1
	)
	order by sum_score desc
) a 
left join student st on a.s_id = st.s_id


~~~



解法2：使用窗口函数

~~~mysql
-- 求总分前3的学生分数与学生姓名

-- 结果：
select st.s_name '姓名',b.sum_score '分数'
from 
(
    -- 子2：根据总分添加排名字段，使用DENSE_RANK，有总分重复时的排名：如： 1 1 1 2 2 3 3 4 4
	select a.s_id,a.sum_score,DENSE_RANK() over(order by a.sum_score desc) '排名'
	from 
	(	
         -- 子1：查询每个学生的总分
		select s_id,sum(s_score) sum_score
		from score
		group by s_id
	) a
) b left join student st on b.s_id = st.s_id
where b.排名 <= 3
~~~

#### 5 查询选修“张三”老师所授课程的学生中成绩最高的学生姓名及其成绩

成绩最高的学生可能不止一个

解法1：join写法 可读性好，写起来方便简洁，效率不如子查询

~~~mysql
-- 查询选修“张三”老师所授课程的学生中成绩最高的学生姓名及其成绩

-- 思路：应当成绩最高的学生不止一个来进行处理，查询出最高分，再根据最高分 与 “张三”老师所授课程 两个条件进行查询

-- join写法 可读性好，写起来方便简洁，效率不如子查询
select st.s_name,c.c_name,sc.s_score
from teacher t 
inner join course c on t.t_id = c.t_id
inner join score sc on sc.c_id = c.c_id
inner join student st on sc.s_id = st.s_id
where t.t_name = '张三' and sc.s_score = 
(
    -- 选修“张三”老师所授课程的学生中成绩最高的分数
	select max(sc.s_score) max_score
	from teacher t 
    inner join course c on t.t_id = c.t_id
	inner join score sc on sc.c_id = c.c_id
	where t.t_name = '张三'
)
~~~

解法2：子查询 + with ... as 写法

~~~mysql
-- temp表存储着 选修“张三”老师所授课程的学生成绩信息
with temp as
(
    select *
    from score sc
    where sc.c_id in
    (
            select c.c_id
            from course c 
            where c.t_id = 
            (
                select t.t_id
                from teacher t
                where t.t_name = '张三'
            ) 
    )
)

select st.s_name '学生姓名',a.s_score '成绩'
from student st 
right join 
(
    -- 查询选修“张三”老师所授课程的学生中成绩最高的学生id及其分数
	select t.s_id,t.s_score
	from temp t
	where t.s_score =
    (
         -- 查询选修“张三”老师所授课程的学生中成绩最高的分数
		select max(s_score) max_score
		from temp t
	)
) a on st.s_id = a.s_id

~~~



#### 6 查找员工入职时间排名倒数第三的员工

数据库SQL实战 No.2

链接：https://www.nowcoder.com/questionTerminal/ec1ca44c62c14ceb990c3c40def1ec6c

关键：distinct + limit 直接锁定了排名倒数第三的员工

注意：入职第一的员工的入职时间是最小(早)的，而其他的排名问题是：第一的员工分数是最高的。

所以**查找员工入职时间排名倒数第三的员工，其实是查找入职时间倒数第三大的员工**。

~~~mysql
-- 查找入职员工时间排名倒数第三的员工所有信息

SELECT * 
FROM employees 
WHERE hire_date = (
                    SELECT DISTINCT hire_date -- 加了distinct去重，会按入职日期进行分组，多个相同入职日期会分为一组
    			   FROM employees 
                    ORDER BY hire_date DESC 
    			   limit 2,1
				 );
-- 或者直接直接分组
SELECT * 
FROM employees 
WHERE hire_date = (
                    SELECT hire_date 
    			   FROM employees 
                    GROUP BY hire_date 
                    ORDER BY hire_date DESC 
                    limit 2,1
   				 )
~~~

####  7 查找薪水排名第二多的员工信息，不能使用order by

https://www.nowcoder.com/questionTerminal/c1472daba75d4635b7f8540b837cc719

no.18:请你查找薪水排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，**不能使用order by完成**

解法1：my max() + 子查询

~~~mysql
-- 结果
select a.emp_no,a.salary,e.last_name,e.first_name
from 
(
    -- 查询薪水第二多的员工id与薪水的值
    select emp_no,salary
    from salaries
    where salary = 
    (
        -- 查询薪水第二多的值
        select max(salary)
        from salaries
        where salary !=
            (
                select max(distinct salary)
                from salaries
            )
    )
) a left join employees e on a.emp_no = e.emp_no

~~~

解法2：

~~~mysql
-- 第二种 通用型:可以求任意第几高的工资
select e.emp_no,s.salary,e.last_name,e.first_name
from
employees e
join 
salaries s on e.emp_no=s.emp_no 
and  s.to_date='9999-01-01'
and s.salary = 
(
     select s1.salary
     from 
     salaries s1
     join -- 一个表进行自连接
     salaries s2 on s1.salary <= s2.salary 
     and s1.to_date='9999-01-01' and s2.to_date='9999-01-01'
     group by s1.salary
     having count(distinct s2.salary) = 2 -- 相当于遍历每一个薪资，查找 >= 当前薪资的薪资数量等于2的那一个,就是第二高的工资
 )

~~~



### 总结

1. 总分前3的学生、最高分的学生 应该当作不止只有3个、1个来处理,都是先查出分数，再根据分数查询人数。
2. **distinct 去重（或者group by去重） + order by 排序 + limit 截取** ,可以解决100%的问题
3. **max(),min()**可以解决部分问题
4. 注意入职时间这种特殊的排名机制。
5. 拿一个表进行**自连接**也是一种思路,也可以解决100%的问题





~~~mysql
~~~

