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
        stuType = row[3]
        schName = row[4]
        schType = row[5]
        city = row[6]
        district = row[7]

        if district!="":
            school = {
                "name": schName,
                "type": schType,
                "city": city,
                "district": district
            }
        else:
            school = {
                "name": schName,
                "type": schType,
                "city": city
            }            

        data[stuNo] = {
            "name": stuName,
            "gender": gender,
            "type": stuType,
            "school": school
        }

#------------------------------
# 寫出資料
#------------------------------
with open('students.json', 'w', encoding='utf8') as outfile:
    json.dump(data, outfile, ensure_ascii=False)
```
