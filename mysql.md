MYSQL

```mysql
count(case when a then 1 else 0 end)
```

 无法统计出正确答案 需要用

```mysql
count(case when a then 1 else null end)
```



寻找中位数ABC公司中位数

```mysql
select *,
row_numer over(partition by company order by salary) as rnk,
count(salary) over(partition by company) as rnk

where rnk in (cnt/2,cnt/2+1,(cnt+1)/2)
```

cnt/2,cnt/2+1对应偶数情况

(cnt+1)/2对应奇数情况



注意从from 和left join 

不然会出现查询结果为null



中位数正反序问题

rnk1为正序，rnk2为倒序 需要排序都 >= total/2

```mysql
select round(avg(num),1) as median

from

  (select 

  num,

  sum(frequency) over(order by num) as rnk1,

  sum(frequency) over(order by num desc) as rnk2,

  sum(frequency) over() as total

  from Numbers

) as t

where rnk1 >= total/2 and rnk2 >= total/2
```

