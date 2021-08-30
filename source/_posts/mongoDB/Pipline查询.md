---
title: MongoDB--Pipline查询
date: 2021-08-28 22:29:31
tags: 
 - MongoDB
 - Query
 - Pipline
categories:
 - MongoDB
---

# Pipline(管道) 查询

MongoDB通过Pipline(管道)来支持复杂的查询（分组，聚合函数，联表。。。）

Pipline定义了一系列的Stage来处理document，每一个Stage的输入是一个或多个Stage，输出也是一个或多个document，document就像管道里的水一样，从一个Stage流入另一个Stage。

## 语法

```JavaScript
db.collection.aggregate( [ { <stage> }, ... ] )
```

## Stage

### $addFields

添加新的字段到文档中，在MongoDB4.2后更名为$set,并保留$addFields

### 用法

#### 1.添加新字段或覆盖已存在的字段，支持聚合函数

`INPUT`:
```json
{
  _id: 1,
  student: "Maya",
  homework: [ 10, 5, 10 ],
  quiz: [ 10, 8 ],
  extraCredit: 0
}
{
  _id: 2,
  student: "Ryan",
  homework: [ 5, 6, 5 ],
  quiz: [ 8, 8 ],
  extraCredit: 8
}
```

`Stage`:
```JavaScript
db.scores.aggregate( [
   {
     $addFields: {
       totalHomework: { $sum: "$homework" } ,
       totalQuiz: { $sum: "$quiz" }
     }
   },
   {
     $addFields: { totalScore:
       { $add: [ "$totalHomework", "$totalQuiz", "$extraCredit" ] } }
   }
] )
```

`OUTPUT`:
```json
{
  "_id" : 1,
  "student" : "Maya",
  "homework" : [ 10, 5, 10 ],
  "quiz" : [ 10, 8 ],
  "extraCredit" : 0,
  "totalHomework" : 25,
  "totalQuiz" : 18,
  "totalScore" : 43
}
{
  "_id" : 2,
  "student" : "Ryan",
  "homework" : [ 5, 6, 5 ],
  "quiz" : [ 8, 8 ],
  "extraCredit" : 8,
  "totalHomework" : 16,
  "totalQuiz" : 16,
  "totalScore" : 40
}
```
#### 2.支持向嵌入文档添加新字段
`INPUT`:
```json
{ _id: 1, type: "car", specs: { doors: 4, wheels: 4 } }
{ _id: 2, type: "motorcycle", specs: { doors: 0, wheels: 2 } }
{ _id: 3, type: "jet ski" }
```

`Stage`:
```json
db.vehicles.aggregate( [
        {
           $addFields: {
              "specs.fuel_type": "unleaded"
           }
        }
   ] )
```

`OUTPUT`:
```json
{ _id: 1, type: "car",
   specs: { doors: 4, wheels: 4, fuel_type: "unleaded" } }
{ _id: 2, type: "motorcycle",
   specs: { doors: 0, wheels: 2, fuel_type: "unleaded" } }
{ _id: 3, type: "jet ski",
   specs: { fuel_type: "unleaded" } }
```
#### 2.支持向数组字段添加新元素
`INPUT`:
```json
{ _id: 1, student: "Maya", homework: [ 10, 5, 10 ], quiz: [ 10, 8 ], extraCredit: 0 }
{ _id: 2, student: "Ryan", homework: [ 5, 6, 5 ], quiz: [ 8, 8 ], extraCredit: 8 }
```

`Stage`:
```json
db.scores.aggregate([
   { $match: { _id: 1 } },
   { $addFields: { homework: { $concatArrays: [ "$homework", [ 7 ] ] } } }
])
```

`OUTPUT`:
```json
{ "_id" : 1, "student" : "Maya", "homework" : [ 10, 5, 10, 7 ], "quiz" : [ 10, 8 ], "extraCredit" : 0 }

```