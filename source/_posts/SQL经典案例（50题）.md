---
title: SQL经典案例（50题）
toc: true
date: 2020-04-18 20:19:38
tags:
categories:
- 算法
---

表结构
<!--more-->
```
Student(Sid,Sname,Sage,Ssex)学生表
sid：学号
sname：学生姓名
sbirth：学生年龄
ssex：学生性别

Course(Cid,Cname,T#)课程表
cid：课程编号
cname：课程名称
tid：教师编号

Score(Sid,Cid,score)成绩表
sid：学号
cid：课程编号
score：成绩

Teacher(Tid,Tname)教师表
tid：教师编号：
tname：教师名字
```

数据
```sql
CREATE TABLE `Student`(
`sid` VARCHAR(20),
`sname` VARCHAR(20) NOT NULL DEFAULT '',
`sbirth` VARCHAR(20) NOT NULL DEFAULT '',
`ssex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);

CREATE TABLE `Course`(
`cid` VARCHAR(20),
`cname` VARCHAR(20) NOT NULL DEFAULT '',
`tid` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);

CREATE TABLE `Teacher`(
`tid` VARCHAR(20),
`tname` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);

CREATE TABLE `Score`(
`sid` VARCHAR(20),
`cid` VARCHAR(20),
`score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);

insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');

insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');

insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');

insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```

# 1、查询“001”课程比“002”课程成绩高的所有学生的学号
```sql
-- 查询“001”课程比“002”课程成绩高的所有学生的学号
SELECT a.sid,a.score,b.score FROM 
(SELECT sid,score FROM Score WHERE cid='01') as a INNER JOIN (SELECT sid,score FROM Score WHERE cid='02') as b on a.sid=b.sid
 WHERE a.score>b.score;
```

# 2、查询平均成绩大于60分的同学的学号和平均成绩
```sql
SELECT sid,avg(score) as avgs
FROM Score
GROUP BY sid
HAVING avgs>60
```

# 3、查询所有同学的学号、姓名、选课数、总成绩
```sql
SELECT s.sid,s.sname,sc.count_class,sc.sum_score
FROM Student as s
LEFT JOIN 
(SELECT sid,count(cid) as count_class,sum(score) as sum_score FROM Score GROUP BY sid) as sc
on s.sid=sc.sid
```

# 4、查询姓‘李’的老师的个数：
```sql
SELECT count(*)
FROM Teacher
WHERE tname LIKE "李%"
```

# 5、查询没有学过“叶平”老师可的同学的学号、姓名：

```sql
SELECT s.sid,s.sname
FROM Student as s
WHERE sid NOT IN
(SELECT DISTINCT sid
FROM Score as sc
WHERE cid IN
		(SELECT cid FROM Course as c
		LEFT JOIN Teacher as t
		ON t.tid=c.tid
		WHERE t.tname='叶平'))
```

# 6、查询学过“叶平”老师所教的所有课的同学的学号、姓名：
```sql
SELECT s.sid,s.sname
FROM Student as s
WHERE s.sid IN
(SELECT sid
FROM Score as sc
INNER JOIN Course as c on sc.cid=c.cid
INNER JOIN Teacher as t on c.tid=t.tid
WHERE t.tname='叶平'
GROUP BY sc.sid
HAVING count(sc.cid)=(SELECT count(cc.cid) FROM Teacher as tt,Course as cc WHERE tt.tid=cc.cid and tt.tname='叶平')
)
```

# 7、查询学过“011”并且也学过编号“002”课程的同学的学号、姓名：
```sql
SELECT sid,sname
FROM Student
WHERE sid IN (SELECT sid FROM Score WHERE cid='01')
AND sid IN (SELECT sid FROM Score WHERE cid='01')
```

# 8、查询课程编号“002”的成绩比课程编号“001”课程低的所有同学的学号、姓名：
```sql
SELECT s.sid,s.sname
FROM Student as s
WHERE s.sid IN
(SELECT t1.sid FROM
(SELECT *
FROM Score
WHERE cid='02') as t2,
(SELECT *
FROM Score
WHERE cid='01') as t1
WHERE t1.sid=t2.sid AND t1.score>t2.score)
```

# 9、查询所有课程成绩小于60的同学的学号、姓名：
```sql
SELECT *
FROM Student
WHERE sid IN
(SELECT sid FROM Score as sc
WHERE 60> ALL(SELECT score FROM Score as sc2 WHERE sc.sid=sc2.sid))
```

# 10、查询没有学全所有课的学生的学号、姓名
```sql
SELECT *
FROM Student
WHERE sid IN
(SELECT sid
FROM Score as sc
GROUP BY sid
HAVING count(cid)=(SELECT count(*) from Course))
```

# 11、查询至少有一门课与学号为“1001”同学所学相同的同学的学号和姓名：
```sql
SELECT *
FROM Student
WHERE sid IN
(SELECT DISTINCT sc.sid
FROM Score as sc
WHERE sc.cid IN
(SELECT cid
FROM Score
WHERE sid='01')
)
```

# 12、查询和“01”号同学所学课程完全相同的其他同学的学号
```sql
SELECT sid
FROM Score
WHERE cid in (SELECT sc.cid FROM Score as sc WHERE sc.sid='01')
GROUP BY sid
HAVING count(cid)=(SELECT count(sc2.cid) FROM Score as sc2 WHERE sc2.sid='01' GROUP BY sc2.sid)
```

# 13、把“SCORE”表中“张三”老师教的课的成绩都更改为此课程的平均成绩


```sql
-- 错误，更新出现在子查询中
START TRANSACTION;
UPDATE Score as sc
SET score=(SELECT AVG(b.score) FROM Score as b WHERE sc.cid=b.cid GROUP BY b.cid)
WHERE sc.cid in (
SELECT cid
FROM Teacher as t,Course as c
WHERE t.tname='张三' and t.tid=c.tid);
ROLLBACK; 
```

# 14、查询和“1002”号的同学学习的课程完全相同的其他同学学号和姓名：
```sql
SELECT *
FROM Student
WHERE sid IN
(
SELECT sid
FROM Score as sc
WHERE sc.cid in (SELECT cid
		FROM Score AS sc
		WHERE sc.sid='02')
GROUP BY sc.sid
HAVING count(sc.cid)=(SELECT count(cid)
FROM Score AS sc
WHERE sc.sid='02')
)
```

# 15、删除学习“张三”老师课的SC表记录
```sql
START TRANSACTION;
DELETE FROM Score
WHERE cid IN
(SELECT cid
FROM Course as c
INNER JOIN Teacher as t on c.tid=t.tid
WHERE t.tname='张三');
SELECT * FROM Score;
ROLLBACK;
```

# 16、向SC表中插入一些记录，这些记录要求符合以下条件：没有上过编号“003”课程的同学学号、002号课的平均成绩：
```sql
START TRANSACTION;
INSERT Score
SELECT sid,'02',(SELECT AVG(sc2.score) FROM Score as sc2 WHERE sc2.cid='02')
FROM Student
WHERE sid NOT IN (SELECT sid FROM Score WHERE cid='03');
SELECT * FROM score;
ROLLBACK;
```

# 17、按平均成绩从高到低显示所有学生的“语文”（c_id='01'）、“数学”（c_id='02'）、“英语”（c_id='03'）三门课程成绩,按如下形式显示：学生ID，语文，数学，英语，有效课程数，有效平均分
```sql
SELECT sc2.sid,
(SELECT score FROM Score as sc WHERE sc.cid='02' and sc.sid=sc2.sid) as '课程',
count(*),
AVG(sc2.score)
FROM Score as sc2
GROUP BY sid
ORDER BY AVG(sc2.score)
```

# 18、查询各科成绩最高和最低的分：以如下的形式显示：课程ID，最高分，最低分
```sql
SELECT cid,
(SELECT MAX(score) FROM Score as sc WHERE sc.cid=c.cid) as 最高分,
(SELECT MIN(score) FROM Score as sc WHERE sc.cid=c.cid) as 最低分
FROM Course as c
```

# 19、按各科平均成绩从低到高和及格率的百分数从高到低顺序：
```sql
SELECT cid,
MAX(score) as 最高分,
MIN(score) as 最低分
FROM score as sc
GROUP BY cid
```

# 20、查询如下课程平均成绩和及格率的百分数(用”1行”显示): 企业管理（001），马克思（002），OO&UML （003），数据库（004）：
```sql
SELECT sc.cid as 课程号,c.cname as 课程名,AVG(sc.score) as 平均成绩, 
(SELECT count(sid) FROM Score as sc2 WHERE sc.cid=sc2.cid and sc2.score>60)/count(sid) as 及格率
FROM Score as sc INNER JOIN Course as c ON sc.cid=c.cid
WHERE sc.cid in ('01','02','03')
GROUP BY sc.cid
ORDER BY 平均成绩,及格率 DESC;
```


# 21、查询不同老师所教不同课程平均分从高到低显示：
```sql
SELECT sc.cid,t.tname,c.cname,avg(sc.score)
FROM Score as sc
INNER JOIN Course as c on sc.cid=c.cid
INNER JOIN Teacher as t on c.tid=t.tid
GROUP BY cid
ORDER BY avg(sc.score) DESC
```

# 22、查询如下课程成绩第3名到第6名的学生成绩单：企业管理(001)，马克思(002)，UML(003)，数据库(004)：

# 23、统计下列各科成绩，各分数段人数：课程ID，课程名称，[100-85],[85-70],[70-60],[ 小于60] ：
```sql
SELECT sc.cid,c.cname,
sum(CASE WHEN sc.score>85 THEN 1 ELSE 0 END) as '100-85',
sum(CASE WHEN sc.score>70 and sc.score<=85 THEN 1 ELSE 0 END) as '85-70',
sum(CASE WHEN sc.score>60 and sc.score<=70 THEN 1 ELSE 0 END) as '70-60',
sum(CASE WHEN sc.score<60 THEN 1 ELSE 0 END) as '<60'
FROM Score as sc
LEFT JOIN Course as c
ON sc.cid=c.cid
GROUP BY sc.cid
```

# 24、查询学生平均成绩及其名次：
```sql
SELECT (SELECT 1+count(DISTINCT avgs)
FROM
(SELECT sc.sid,AVG(sc.score) as avgs
FROM Score as sc
GROUP BY sid) as T1
WHERE T1.avgs>T2.avgs) as 名次
,T2.sid,T2.avgs
FROM 
(SELECT sid,avg(score) as avgs
FROM Score
GROUP BY sid) as T2
ORDER BY avgs desc;
```

# 25、查询各科成绩前三名的记录（不考虑成绩并列情况）：
```sql
SELECT *
FROM Score as a
WHERE (
SELECT count(*)
FROM Score as b
WHERE a.cid=b.cid and b.score>a.score)<3
ORDER BY cid,score DESC
```


# 26、查询每门课程被选修的学生数：
```sql
SELECT cid,count(sid)
FROM Score
GROUP BY cid
```

# 27、查询出只选修一门课程的全部学生的学号和姓名：
```sql
SELECT DISTINCT s.sid,s.sname,count(sc.cid)
FROM Student as s
LEFT JOIN Score as sc ON s.sid=sc.sid
GROUP BY s.sid
HAVING count(sc.cid)=1
```

# 28、查询男生、女生人数：
```sql
SELECT sum(case WHEN s.ssex='男' THEN 1 ELSE 0 END) as 男,
sum(case WHEN s.ssex='女' THEN 1 ELSE 0 END) as 女
FROM Student as s
```

# 29、查询姓“张”的学生名单：
```sql
SELECT *
FROM Student
WHERE sname like '张%'
```

# 30、查询同名同姓的学生名单，并统计同名人数：
```sql
SELECT DISTINCT a.sname,count(*)
FROM Student as a,Student as b
WHERE a.sid<>b.sid and a.sname=b.sname
GROUP BY a.sname

SELECT sname,count(*)
FROM Student
GROUP BY sname
HAVING count(*)>1
```

# 31、1981年出生的学生名单（注：student表中sage列的类型是datetime）:
```sql
SELECT *
FROM Student as s
WHERE YEAR(s.sbirth)='1990'
```

# 32、查询平均成绩大于85的所有学生的学号、姓名和平均成绩：

```sql
SELECT sc.sid,s.sname,AVG(sc.score)
FROM Score as sc
INNER JOIN Student as s on s.sid=sc.sid
GROUP BY sid
HAVING AVG(sc.score)>85
```

# 33、查询每门课程的平均成绩，结果按平均成绩升序排序，平均成绩相同时，按课程号降序排列：
```sql
SELECT cid,AVG(score)
FROM Score
GROUP BY cid
ORDER BY AVG(score),cid DESC;
```

# 34、查询课程名称为“数据库”，且分数低于60的学生名字和分数：
```sql
SELECT s.sname,sc.score
FROM Score as sc
INNER JOIN Course as c on sc.cid=c.cid
INNER JOIN Student as s on s.sid=sc.sid
WHERE c.cname='数据库' AND sc.score<60
```

# 35、查询所有学生的选课情况：
```sql
SELECT s.sname,sc.sid,c.cid
FROM Score as sc
LEFT JOIN Course as c ON sc.cid=c.cid
LEFT JOIN Student as s ON sc.sid=s.sid
```

# 36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数：
```sql
SELECT DISTINCT s.sname,sc.score,c.cname
FROM Student as s
LEFT JOIN Score as sc ON s.sid=sc.sid
LEFT JOIN Course as c ON sc.cid=c.cid
WHERE sc.score>70
```

# 37、查询不及格的课程，并按课程号从大到小的排列：
```sql
SELECT *
FROM Score
WHERE score<60
ORDER BY cid
```


# 38、查询课程编号为“003”且课程成绩在80分以上的学生的学号和姓名：
```sql
SELECT s.sid,s.sname
FROM Student as s
WHERE s.sid IN
(SELECT DISTINCT sid
FROM Score
WHERE score>80 and cid='03'
)
```

# 39、求选了课程的学生人数：
```
SELECT count(DISTINCT sid) 
FROM Score
```

# 41、查询各个课程及相应的选修人数：
```sql
SELECT cid,count(*)
FROM Score
GROUP BY cid;
```

# 42、查询不同课程成绩相同的学生和学号、课程号、学生成绩：
```sql
select DISTINCT a.sid,a.cid,a.score
from Score as a ,Score as b 
where a.score = b.score
and a.cid <> b.cid;
```

# 43、查询每门课程成绩最好的前两名：
```sql
SELECT *
FROM Score as a
WHERE (
SELECT count(*) FROM Score as b WHERE a.cid=b.cid and b.score>a.score
)<3
ORDER BY cid,score desc;
```

# 44、统计每门课程的学生选修人数(超过10人的课程才统计)。要求输出课程号和选修人数，查询结果按人数降序排序，若人数相同，按课程号升序排序：
```sql
SELECT cid,count(score) as cs
FROM Score
GROUP BY cid
HAVING cs > 10
ORDER BY cs DESC,cid;
```

# 45、检索至少选修两门课程的学生学号：
```sql
SELECT sid
FROM Score
GROUP BY sid
HAVING count(cid)>=2
```


# 46、查询全部学生选修的课程和课程号和课程名：
```sql
SELECT cid,cname
FROM Course
WHERE cid IN(SELECT cid FROM Score)
```

# 47、查询没学过”叶平”老师讲授的任一门课程的学生姓名
```sql
SELECT s.sname
FROM Student as s
WHERE s.sid NOT IN
(SELECT sid
FROM Score as sc
WHERE sc.cid IN(

SELECT cid
FROM Course as c
INNER JOIN Teacher as t
WHERE c.tid=t.tid and t.tname='叶平')
)
```

# 48、查询两门以上不及格课程的同学的学号以及其平均成绩：
```sql
SELECT sid,avg(score)
FROM Score
WHERE sid IN 
(SELECT sid
FROM Score
WHERE score<60
GROUP BY sid
HAVING count(sid)>2)
GROUP BY sid
```

# 49、检索“004”课程分数小于60，按分数降序排列的同学学号：
```sql
SELECT sid
FROM Score
WHERE cid='004' and score<60
ORDER BY score DESC;
```

# 50、删除“002”同学的“001”课程的成绩：
```sql
DELETE FROM Score
WHERE sid='002' and cid='001'
```