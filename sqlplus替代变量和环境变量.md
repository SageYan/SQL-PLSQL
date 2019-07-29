# sqlplus替代变量和环境变量

## sqlplus的替代变量"&"

```sql
SQL> select ename,&a from emp;
Enter value for a: sal
old   1: select ename,&a from emp
new   1: select ename,sal from emp

ENAME             SAL
---------- ----------
SMITH             800
ALLEN            1600
WARD             1250
JONES            2975
MARTIN           1250
BLAKE            2850


SCOTT>select * from emp where ename=upper('&NN');
Enter value for nn: scott
old   1: select * from emp where ename=upper('&NN')
new   1: select * from emp where ename=upper('scott')


分页查询，每页5行：
select * from 
(select rownum rn,a.* from (select * from emp order by sal desc) a)
where rn between &p*5-4 and &p*5;


替代变量的原理：
定义
define c=sal
undefine c
查看
define
```



## sqlplus环境变量的设置

```sql
查看保存最后执行的sql：

SQL> list
  1  select * from
  2  (select rownum rn,a.* from (select * from emp order by sal desc) a)
  3* where rn between &p*5-4 and &p*5
SQL> 1  
  1* select * from
SQL> sav 1.sql
Created file 1.sql
SQL> get 1.sql
  1  select * from
  2  (select rownum rn,a.* from (select * from emp order by sal desc) a)
  3* where rn between &p*5-4 and &p*5
SQL> @1.sql
Enter value for p: 1
Enter value for p: 1
old   3: where rn between &p*5-4 and &p*5
new   3: where rn between 1*5-4 and 1*5

    
对最后执行的SQL的操作：
SQL> select  * from scott.emp;

EMPNO ENAME  JOB              MGR HIREDATE    SAL       COMM     DEPTNO
----- ------ --------- ---------- --------- ----- ---------- ----------
 7369 SMITH  CLERK           7902 17-DEC-80   800                    20
 7499 ALLEN  SALESMAN        7698 20-FEB-81  1600        300         30
 7521 WARD   SALESMAN        7698 22-FEB-81  1250        500         30
 7566 JONES  MANAGER         7839 02-APR-81  2975                    20
 7654 MARTIN SALESMAN        7698 28-SEP-81  1250       1400         30
 7698 BLAKE  MANAGER         7839 01-MAY-81  2850                    30
 7782 CLARK  MANAGER         7839 09-JUN-81  2450                    10
 7788 SCOTT  ANALYST         7566 19-APR-87  3000                    20
 7839 KING   PRESIDENT            17-NOV-81  5000                    10
 7844 TURNER SALESMAN        7698 08-SEP-81  1500          0         30
 7876 ADAMS  CLERK           7788 23-MAY-87  1100                    20

EMPNO ENAME  JOB              MGR HIREDATE    SAL       COMM     DEPTNO
----- ------ --------- ---------- --------- ----- ---------- ----------
 7900 JAMES  CLERK           7698 03-DEC-81   950                    30
 7902 FORD   ANALYST         7566 03-DEC-81  3000                    20
 7934 MILLER CLERK           7782 23-JAN-82  1300                    10

14 rows selected.

SQL> a  order by deptno;  --添加order by
  1* select  * from scott.emp order by deptno
SQL> /

EMPNO ENAME  JOB              MGR HIREDATE    SAL       COMM     DEPTNO
----- ------ --------- ---------- --------- ----- ---------- ----------
 7782 CLARK  MANAGER         7839 09-JUN-81  2450                    10
 7839 KING   PRESIDENT            17-NOV-81  5000                    10
 7934 MILLER CLERK           7782 23-JAN-82  1300                    10
 7566 JONES  MANAGER         7839 02-APR-81  2975                    20
 7902 FORD   ANALYST         7566 03-DEC-81  3000                    20
 7876 ADAMS  CLERK           7788 23-MAY-87  1100                    20
 7369 SMITH  CLERK           7902 17-DEC-80   800                    20
 7788 SCOTT  ANALYST         7566 19-APR-87  3000                    20
 7521 WARD   SALESMAN        7698 22-FEB-81  1250        500         30
 7844 TURNER SALESMAN        7698 08-SEP-81  1500          0         30
 7499 ALLEN  SALESMAN        7698 20-FEB-81  1600        300         30

EMPNO ENAME  JOB              MGR HIREDATE    SAL       COMM     DEPTNO
----- ------ --------- ---------- --------- ----- ---------- ----------
 7900 JAMES  CLERK           7698 03-DEC-81   950                    30
 7698 BLAKE  MANAGER         7839 01-MAY-81  2850                    30
 7654 MARTIN SALESMAN        7698 28-SEP-81  1250       1400         30

14 rows selected.

SQL> break on deptno  --去除order by的重复值
SQL> /

EMPNO ENAME  JOB              MGR HIREDATE    SAL       COMM     DEPTNO
----- ------ --------- ---------- --------- ----- ---------- ----------
 7782 CLARK  MANAGER         7839 09-JUN-81  2450                    10
 7839 KING   PRESIDENT            17-NOV-81  5000
 7934 MILLER CLERK           7782 23-JAN-82  1300
 7566 JONES  MANAGER         7839 02-APR-81  2975                    20
 7902 FORD   ANALYST         7566 03-DEC-81  3000
 7876 ADAMS  CLERK           7788 23-MAY-87  1100
 7369 SMITH  CLERK           7902 17-DEC-80   800
 7788 SCOTT  ANALYST         7566 19-APR-87  3000
 7521 WARD   SALESMAN        7698 22-FEB-81  1250        500         30
 7844 TURNER SALESMAN        7698 08-SEP-81  1500          0
 7499 ALLEN  SALESMAN        7698 20-FEB-81  1600        300

EMPNO ENAME  JOB              MGR HIREDATE    SAL       COMM     DEPTNO
----- ------ --------- ---------- --------- ----- ---------- ----------
 7900 JAMES  CLERK           7698 03-DEC-81   950                    30
 7698 BLAKE  MANAGER         7839 01-MAY-81  2850
 7654 MARTIN SALESMAN        7698 28-SEP-81  1250       1400



环境变量的设置：
1. echo
是否显示脚本的内容：
SQL> show echo
echo OFF
SQL> set echo on
SQL> @1.sql
SQL> select * from
  2  (select rownum rn,a.* from (select * from emp order by sal desc) a)
  3  where rn between &p*5-4 and &p*5
  4  /
Enter value for p: 1
Enter value for p: 1
old   3: where rn between &p*5-4 and &p*5
new   3: where rn between 1*5-4 and 1*5


SQL> set echo off
SQL> @1.sql
Enter value for p: 1
Enter value for p: 1
old   3: where rn between &p*5-4 and &p*5
new   3: where rn between 1*5-4 and 1*5


2.
SQL> show arraysize  --屏幕一次打印的行数
arraysize 15
SQL> show pagesize
pagesize 14
SQL> show feedback  --返回结果集几行
FEEDBACK ON for 6 or more rows

SQL> show heading  --是否打印页头
heading ON
SQL> show long     --打印长文本的长度
long 80
SQL> col  EMPNO for 9999  --数字类型打印长度
SQL> col ename for a6    --字符类型打印长度

3. sqlplus使用主机命令
SQL> !ls
1.sql  416a.sql  database  dba_snapshot_database_10g_ARIS_20181219.html  expdp  p13390677_112040_Linux-x86-64_1of7.zip  p13390677_112040_Linux-x86-64_2of7.zip


4. 导出屏显内容
spool 0728am.txt append
spool off
```

