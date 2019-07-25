[TOC]



# 增强group by

## rollup

```sql
实验：
select deptno,job,sum(sal) from emp group by deptno,job order by 1,2;  普通group by
select deptno,job,sum(sal) from emp group by rollup(deptno,job) order by 1,2;
结果集等同于:
select deptno,job,sum(sal) from emp group by deptno,job 
union select deptno,null,sum(sal) from emp group by deptno 
union select null,null,sum(sal) from emp;

注释  ：rollup(a,b,c) --> 会产生n+1种聚集运算的结果
          相当于group by a
               group by a,b
               group by a,b,c
               total
```

## cube

```sql
cube(a,b,c) --> 2的n次方种聚集运算的结果
group by a
group by b
group by c
group by a,b
group by a,c
group by b,c
group by a,b,c
total

SQL> select deptno,job,sum(sal) from emp group by cube(deptno,job) order by 1,2;

    DEPTNO JOB         SUM(SAL)
---------- --------- ----------
        10 CLERK           1300
        10 MANAGER         2450
        10 PRESIDENT       5000
        10                 8750
        20 ANALYST         6000
        20 CLERK           1900
        20 MANAGER         2975
        20                10875
        30 CLERK            950
        30 MANAGER         2850
        30 SALESMAN        5600

    DEPTNO JOB         SUM(SAL)
---------- --------- ----------
        30                 9400
           ANALYST         6000
           CLERK           4150
           MANAGER         8275
           PRESIDENT       5000
           SALESMAN        5600
                          29025
```



## grouping  函数以及作用

```sql
select deptno,job,sum(sal),grouping(deptno),grouping(job)
from emp group by rollup(deptno,job);    
注释：参与聚合运算的列返回0，不参与返回1；
结果集：

    DEPTNO JOB         SUM(SAL) GROUPING(DEPTNO) GROUPING(JOB)
---------- --------- ---------- ---------------- -------------
        10 CLERK           1300                0             0
        10 MANAGER         2450                0             0
        10 PRESIDENT       5000                0             0
        10                 8750                0             1
        20 CLERK           1900                0             0
        20 ANALYST         6000                0             0
        20 MANAGER         2975                0             0
        20                10875                0             1
        30 CLERK            950                0             0
        30 MANAGER         2850                0             0
        30 SALESMAN        5600                0             0

    DEPTNO JOB         SUM(SAL) GROUPING(DEPTNO) GROUPING(JOB)
---------- --------- ---------- ---------------- -------------
        30                 9400                0             1
                          29025                1             1


作用：
col deptno for a15
select decode(GROUPING(DEPTNO)||GROUPING(JOB),'01','subtotal '||deptno,'11','total ',deptno) deptno,job,sum(sal) from emp group by rollup(deptno,job);

DEPTNO          JOB         SUM(SAL)
--------------- --------- ----------
10              CLERK           1300
10              MANAGER         2450
10              PRESIDENT       5000
subtotal 10                     8750
20              CLERK           1900
20              ANALYST         6000
20              MANAGER         2975
subtotal 20                    10875
30              CLERK            950
30              MANAGER         2850
30              SALESMAN        5600

DEPTNO          JOB         SUM(SAL)
--------------- --------- ----------
subtotal 30                     9400
Total                          29025

或者
SQL>  select 
(case  GROUPING(DEPTNO)||GROUPING(JOB) when '01' then 'subtotal '||deptno when '11' then 'Total' else to_char(deptno) end) deptno ,
job,sum(sal) from emp group by rollup(deptno,job);



select deptno,job,mgr,sum(sal)
from emp group by grouping sets ((deptno,job),(job,mgr));
注释： grouping by    相当于将（deptno，job）和（job.mgr)分别分组查询再求union
    DEPTNO JOB              MGR   SUM(SAL)
---------- --------- ---------- ----------
           CLERK           7902        800
           PRESIDENT                  5000
           CLERK           7698        950
           CLERK           7788       1100
           CLERK           7782       1300
           SALESMAN        7698       5600
           MANAGER         7839       8275
           ANALYST         7566       6000
        20 CLERK                      1900
        30 SALESMAN                   5600
        20 MANAGER                    2975

    DEPTNO JOB              MGR   SUM(SAL)
---------- --------- ---------- ----------
        30 CLERK                       950
        10 PRESIDENT                  5000
        30 MANAGER                    2850
        10 CLERK                      1300
        10 MANAGER                    2450
        20 ANALYST                    6000

```




   





