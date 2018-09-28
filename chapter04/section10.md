# ORM作业参考答案

1. 查询平均成绩大于60分的同学的id和平均成绩
```python
    rows = Student.objects.annotate(avg=Avg("score__number")).filter(avg__gte=60).values("id","avg")
    for row in rows:
        print(row)
```
2. 查询所有同学的id、姓名、选课的数、总成绩
```python
    rows = Student.objects.annotate(course_nums=Count("score__course"),total_score=Sum("score__number")).values("id","name","course_nums","total_score")
    for row in rows:
        print(row)
```
3. 查询姓“李”的老师的个数
```python
    teacher_nums = Teacher.objects.filter(name__startswith="李").count()
    print(teacher_nums)
```
4. 查询没学过“黄老师”课的同学的id、姓名
```python
    rows = Student.objects.exclude(score__course__teacher__name="黄老师").values('id','name')
    for row in rows:
        print(row)
```
5. 查询学过课程id为1和2的所有同学的id、姓名
```python
    rows = Student.objects.filter(score__course__in=[1,2]).distinct().values('id','name')
    for row in rows:
        print(row)
```
6. 查询学过“黄老师”所教的所有课的同学的学号、姓名
```python
    rows = Student.objects.annotate(nums=Count("score__course",filter=Q(score__course__teacher__name='黄老师'))).filter(nums=Course.objects.filter(teacher__name='黄老师').count()).values('id','name')
    for row in rows:
    print(row)
```