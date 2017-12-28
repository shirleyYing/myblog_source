title: 记录--一个简单的数据封装
date: 2016-06-02 12:25:26
tags: [record ]
categories: js
---
# 记录--一个简单的数据封装
<!-- more -->
### 数据库返回的数据格式：
```
data=[{
    knowledge_point: "网页设计与制作",
    type_name: "单选",
    count: 70
},
{
    knowledge_point: "网页设计与制作",
    type_name: "多选",
    count: 30
}]

```
### 需要的数据格式
```
data= {
    knowledge_point: "网页设计与制作",
    sum: 100,
    types: [{
        type_name: "单选",
        count: 70
    }, {
        type_name: "多选",
        count: 30
    }]
}, 
```
### 主要思路    
把相同knowledge_point 的type_name和count合并到数组中，最初的想法是一个for取得knowledge_point，第二个for遍历按照knowledge_point将type_name,count放到对应数组中，第三个for，将第二次for生成的数组塞到对应knowledge_point的对象中。

多次循环效率太低，想用一个for搞定，难点就是第一次得到一个新的knowledge_point时，可以封装，但是第二次遇到时，在循环中不能取得第一次遇到的对象在数组中的位置。想到了对象属性可以直接调用，定义一个`foramatData={}`将knowledge_point作为key
```
foramatData:{
    "网页设计与制作":{
        knowledge_point:"网页设计与制作", 
        types: [{
            type_name: "单选",
            count: 70
        }]    
    }
}
```
在data循环中再次遇到“网页设计与制作”时，调用`foramatData["网页设计与制作"].types.push()`,就可将新对象存到对应数组中.
然后再查询一次得到sum，放到`formatData`对应对象中去。
代码如下：

>formatData:格式化后的数据
>questionTypes:存放题目类型列表
>types:合并后的数组题目类型数组
>item:types中的一个对象

```javascript
questionBankList.then(function(data) {

        var questionTypes = [];
        var formatDatas = {};
        for (var i = 0; i < data.length; i++) {
            var types = [];
            var item={};
            item.type_name = data[i].question_type;
            item.count = data[i].count;

            var knowledge_point = data[i].knowledge_point;

            if (questionTypes.indexOf(knowledge_point) < 0) {
                questionTypes.push(knowledge_point);
                formatDatas[knowledge_point] = {
                    knowledge_point: knowledge_point,
                    types:types
                };
            }
            formatDatas[knowledge_point].types.push(item);
        }
        var oneQuestionBankSum = studentPaper.getOneQuestionBankSum();   
        oneQuestionBankSum.then(function(sum) {
                for (var i = 0; i < sum.length; i++) {
                    formatDatas[sum[i].knowledge_point].sum= sum[i].sum;
                }  
                res.json(formatDatas);
        });
    });
```