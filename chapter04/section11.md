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
7. 查询所有课程成绩小于60分的同学的id和姓名
   ```python
    students = Student.objects.exclude(score__number__gt=60)
    for student in students:
        print(student)
   ```
8. 查询没有学全所有课的同学的id、姓名
   ```python
    students = Student.objects.annotate(num=Count(F("score__course"))).filter(num__lt=Course.objects.count()).values('id','name')
    for student in students:
    print(student)
   ```
9. 查询所有学生的姓名、平均分，并且按照平均分从高到低排序
   ```python
    students = Student.objects.annotate(avg=Avg("score__number")).order_by("-avg").values('name','avg')
    for student in students:
        print(student)
   ```
10. 查询各科成绩的最高和最低分，以如下形式显示：课程ID，课程名称，最高分，最低分
    ```python
    courses = Course.objects.annotate(min=Min("score__number"),max=Max("score__number")).values("id",'name','min','max')
    for course in courses:
        print(course)
    ```
11. 查询每门课程的平均成绩，按照平均成绩进行排序
    ```python
    courses = Course.objects.annotate(avg=Avg("score__number")).order_by('avg').values('id','name','avg')
    for course in courses:
        print(course)
    ```
12. 统计总共有多少女生，多少男生
    ```python
    rows = Student.objects.aggregate(male_num=Count("gender",filter=Q(gender=1)),female_num=Count("gender",filter=Q(gender=2)))
    print(rows)
    ```
13. 将“黄老师”的每一门课程都在原来的基础之上加5分
    ```python
    rows = Score.objects.filter(course__teacher__name='黄老师').update(number=F("number")+5)
    print(rows)
    ```
14. 查询两门以上不及格的同学的id、姓名、以及不及格课程数
    ```python
    students = Student.objects.annotate(bad_count=Count("score__number",filter=Q(score__number__lt=60))).filter(bad_count__gte=2).values('id','name','bad_count')
    for student in students:
    print(student)
    ```
15. 查询每门课的选课人数
    ```python
    courses = Course.objects.annotate(student_nums=Count("score__student")).values('id','name','student_nums')
    for course in courses:
        print(course)
    ```



