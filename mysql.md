# MYSQL

### **count(case when)**

```mysql
count(case when a then 1 else 0 end)
```

 无法统计出正确答案 需要用

```mysql
count(case when a then 1 else null end)
```



### **寻找中位数ABC公司中位数**

```mysql
select *,
row_numer over(partition by company order by salary) as rnk,
count(salary) over(partition by company) as rnk

where rnk in (cnt/2,cnt/2+1,(cnt+1)/2)
```

cnt/2,cnt/2+1对应偶数情况

(cnt+1)/2对应奇数情况



**注意从from 和left join** 

**不然会出现查询结果为null**



### **中位数正反序问题**

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



###  **IFNULL函数**

```mysql
ifnull(expr1,expr2)
```

如果expr1不是NULL，IFNULL()返回expr1，否则它返回expr2。IFNULL()返回一个数字或字符串值
**注意:空值不判断**



mysql不支持full join 可使用UNION ALL 将 LEFT JOIN 和 RIGHT JOIN 组合起来



### **自连接去重及删除一半重复的记录**

 [612. 平面上的最近距离](https://leetcode.cn/problems/shortest-distance-in-a-plane/)

```mysql
select *,round(sqrt(pow(a.x-b.x,2)+pow(a.y-b.y,2)),2) as shortest

from Point2D a

join Point2D b 

where a.x < b.x or(a.x = b.x and a.y<b.y) 
```



### rank,row_number,dense_rank

1、rank  遇重复值排序并列，然后跳跃到当前排序记录次数开始（递增或递减）排序

2、row_number 遇重复值排序不并列，连续不间断（递增或递减）排序

3、dense_rank 遇重复值排序并列，然后继续不间断（递增或递减）排序

![image-20220713141735797](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220713141735797.png)



### 求连续区间

[1225. 报告系统状态的连续日期](https://leetcode.cn/problems/report-contiguous-dates/)

按照rnk_diff进行分组

```mysql
select 

  period_state,

  min(date) as start_date,

  max(date) as end_date

from

(select

  period_state,

  date,

  row_number() over(order by date) - 

  row_number() over(partition by period_state order by date) as rnk_diff

from cte

where date between '2019-01-01' and '2019-12-31') 

group by period_state, rnk_diff # 不同period_state时rnk_diff一样

order by 2
```

