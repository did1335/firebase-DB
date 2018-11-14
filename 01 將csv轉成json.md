```python
#------------------------------
# 匯入必要模組
#------------------------------
import csv
import json

#------------------------------
# 存CSV資料的dict
#------------------------------
data = {}

#------------------------------
# 逐行讀入CSV
#------------------------------
with open('students.csv', 'r', encoding='utf-8') as file:
    rows = csv.reader(file, delimiter=',', quotechar='"')
    for row in rows:
        stuNo = row[0]
        stuName = row[1]
        gender = row[2]

        data[stuNo] = {
            "stuName": stuName,
            "gender": gender
        }

#------------------------------
# 寫出資料
#------------------------------
with open('students.json', 'w', encoding='utf8') as outfile:
    json.dump(data, outfile, ensure_ascii=False)
```
