### JSON
JSON是一种数据格式，不是一种编程语言。
#### 语法
- 简单值 // 5 "hello" true null 
- 对象 // {"name": "nicholas"} 属性加""
- 数组 // [25, "hi", true] 没有分号和变量

#### 解析与序列化
可以把JSON数据结构解析为游泳的JavaScript对象。  
JSON对象有两个方法：
- stringify()  把JavaScript对象序列化为JSON字符串
- parse()  把JSON字符串解析为原生JavaScript值

```
var book = {
    title: 'professional JavaScript',
    author: ["Nicholas"],
    edition: 3
};

var jsonText = JSON.stringify(book);

var bookCopy = JSON.parse(jsonText);
```

##### 序列化选项
stringify还可以接受两个参数，一个是过滤器，一个是换行缩进

```
var jsonText = JSON.stringify(book, ["title", "edition"]);


var jsonText = JSON.stringify(book, function(key, value) {   // 替换（过滤）函数replacer 
    switch(key) {
        case "authors": 
            return value.join(",");
        case "year": 
            return 5000;
        case "edition": 
            return undefined;   // 删除该属性
        default:
            return value;
        
    }
})

var jsonText = JSON.stringify(book, null, 4);
var jsonText = JSON.stringify(book, null, " - -");
```

toJSON() 方法，可以给对象定义toJSON()方法，返回其自身的JSON数据格式，stringfy()会调用此方法

##### 解析选项
```
var bookCopy = JSON.parse(jsonText, function(key, value) { // 还原函数 reviver
    if (key == "releaseDate") {
        return new Date(value);
    } else {
        return value;
    }
})
```

