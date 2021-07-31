
# python 示例

```python
import requests
import json

# 输入输出
# name=input()
# print("hello", name)

# 第三方包
r = requests.get('https://www.baidu.com/')
print(r.status_code, r.headers)

# list
nums = [1, 2, 3, 4]
print(nums)
for num in nums:
    if num > 2:
        print(num)

# tuple
tupleNums = (1, 2, 3, 4)
print(tupleNums)
for num in tupleNums:
    print(num)

for i in range(0, 5):
    print(i)

# dict
d = {"top": 10, "jim": 20}
for k, v in d.items():
    print(k, v)
for k in d:
    print(k, d[k])

# 函数
def power(x, n=2):
    s = 1
    while n>0:
        n=n-1
        s=s*x
    return s


print(power(3))
print(pow(3, 5))

# class
class Student(object):
    def __init__(self, name, score):
        self.__name = name  # __下划线实现访问控制
        self.__score = score

    def SetName(self, name):
        self.__name = name

    def GetName(self):
        return self.__name

    def GetScore(self):
        return self.__score


def ToDict(st):
    return {
        "name": st.GetName(),
        "score": st.GetScore()
    }


def StudentDemo():
    s = Student("tom", 99)
    print(s.GetName())
    s.SetName('jim')
    print(s.GetName())
    print(json.dumps(s, default=lambda obj: obj.__dict__))
    print(json.dumps(s, default=ToDict))  # json

StudentDemo()

# 读文件
filePath = "./test.txt"
try:
    with open(filePath, 'r') as f:
        while True:
            line = f.readline()
            if line:
                print(line, end='')
            else:
                break
        print("read file over")
except Exception as e:
    print(e)
finally:
    pass


# 写文件
try:
    # f = open(filePath, 'w')
    with open(filePath, 'a') as f:
        for i in range(3):
            f.write("new line\n")
except Exception as e:
    print(e)
finally:
    pass


# 正则表达式
import re

# 字符串匹配，类似 grep 功能
# src = input("please enter src string:")
src = "abcdef,abcde"
# result = re.match('abc*', src) # 匹配
result = re.search('abc*', src) # 存在
if result:
    print("match")
else:
    print("not match")

pattern = re.compile(r'abc[a-z]*')
# result = re.findall(r'abc[a-z]*', src)
result = pattern.findall(src)
print(result)

# 切分字符串，类似 awk 功能
result = re.split('[a-zA-Z]+', '0a3B9')
print(result)
if len(result)>=3:
    print(result[1], result[2])

```

