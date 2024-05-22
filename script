# schools.csv数据格式类似于下面的表格
# school_name,boys_quota,girls_quota,total_quota
# xxx,,,6
# yyy,,,1

# student.csv数据格式类似于下面的表格
# name,gender,choice1,choice2,choice3
# 张三,M,xxx,yyy,zzz
# 李四,F,aaa,bbb,ccc

import pandas as pd
import random

# 读取数据 
students = pd.read_csv('./students.csv')
schools = pd.read_csv('./schools.csv')

# 初始化学校剩余名额
schools['boys_remaining'] = schools['boys_quota'].fillna(0)
schools['girls_remaining'] = schools['girls_quota'].fillna(0)
schools['total_remaining'] = schools['total_quota'].fillna(0)

# 创建一个字典用于存储学校信息
school_dict = {row['school_name']: row for index, row in schools.iterrows()}

# 初始化分配结果
allocation = []

# 学生名单字典，用于快速查找每个志愿的学生
choice_dict = {school: [] for school in schools['school_name']}
for index, student in students.iterrows():
    if pd.notna(student['choice1']):
        choice_dict[student['choice1']].append(student)
    if pd.notna(student['choice2']):
        choice_dict[student['choice2']].append(student)
    if pd.notna(student['choice3']):
        choice_dict[student['choice3']].append(student)

# 分配函数
def allocate_school(school_name):
    school = school_dict[school_name]
    candidates = choice_dict[school_name]
    random.shuffle(candidates)  # 随机打乱学生顺序

    allocated_students = []
    for student in candidates:
        if student['gender'] == 'M' and (school['boys_remaining'] > 0 or school['total_remaining'] > 0):
            school['boys_remaining'] -= 1
            school['total_remaining'] -= 1
            allocated_students.append(student['name'])
        elif student['gender'] == 'F' and (school['girls_remaining'] > 0 or school['total_remaining'] > 0):
            school['girls_remaining'] -= 1
            school['total_remaining'] -= 1
            allocated_students.append(student['name'])

    return allocated_students

# 进行分配
for school_name in schools['school_name']:
    allocated_students = allocate_school(school_name)
    for student_name in allocated_students:
        allocation.append((student_name, school_name))
        # 从choice_dict中移除已分配的学生
        for choice in choice_dict.values():
            choice[:] = [student for student in choice if student['name'] != student_name]

# 将未分配的学生标记为None
all_student_names = set(students['name'])
allocated_student_names = set(student_name for student_name, _ in allocation)
unallocated_student_names = all_student_names - allocated_student_names
for student_name in unallocated_student_names:
    allocation.append((student_name, None))

# 将分配结果保存到DataFrame中
allocation_df = pd.DataFrame(allocation, columns=['student_name', 'allocated_school'])

# 保存分配结果到csv文件
output_path = './allocation_result.csv'
allocation_df.to_csv(output_path, index=False)
