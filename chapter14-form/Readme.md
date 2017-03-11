### 表单脚本

#### 表单的基础知识
表单对应的是HTMLFormElement类型。  
HTMLFormElement的属性： 
 
- acceptCharset
- action 
- elements
- enctype 编码类型
- length 
- method
- name
- reset()
- submit()
- target
document.forms可以取得页面中所有的表单。
##### 提交表单
提交按钮

```
<!--通用提交按钮-->
<input type="submit" value="submit from">

<!--自定义提交按钮-->
<button type="submit">submit form</button>

<!--图像按钮-->
<input type="image" src="gra.gif">

```
上述按钮获得焦点后，按回车键就可以提交表单。浏览器会在请求发送之前触发submit事件，我们可以在submit事件中对表单数据进行校验。  
JS用`submit()`进行提交，不会触发submit事件，要记得在调用此方法之前验证数据。  

##### 重置表单  
重置按钮

```
<!--通用重置按钮-->
<input type="reset" value="reset form">

<!--自定义重置按钮-->
<button type="reset">reset form</button>
```

JS用`form.reset()`重置表单

#### 表单字段
访问表单字段  
form.elements[0] 或 form.elements[name]  

表单字段属性  

- disabled 是否被禁用
- form 指向当前表单
- name 字段名称
- readOnly
- tabIndex 当前字段的切换序号
- type 'checkbox','radio'等
- value 值

表单字段方法  

- focus()
- blur()

表单字段事件 
 
- blur
- change
- focus

#### 文本框脚本 
input 或 textarea
##### 选择文本
select()
select事件  

取得选择的文本 
 
- textbox.selectionStart 和 textbox.selectionEnd
- document.selection

```
function getSelectedText(textbox) {
    if (typeof textbox.selectionStart == "number") {
        return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd);
    } else if (document.selection) {
        return document.selection.createRange().text;    
    }
}
```

选择部分文本  

- setSelectionRange()
- createTextRange() moveStart() moveEnd()

##### 过滤输入
屏蔽字符  
在keypress事件中去检测文本

##### 操作剪贴板
- beforecopy
- copy
- beforecut
- cut 
- beforepaste
- paste

##### HTML5约束验证API
- required 必填字段
- 新增type， email和url
- 对数值类型，新增的属性 min, max, step 
- 输入模式 pattern属性
- 检测有效性 checkValidity() validity属性
- 禁用验证 novalidate

#### 选择框脚本
选择框由`<select>`和`<option>`组成。HTMLSelectElement提供下列的属性和方法。

- add(newOption,relOption) 向控件中插入新的<option>元素 
- multiple 是否允许多项选择
- options 控件中所有<option>元素的HTMLCollection
- remove(index) 移除给定位置的选项
- selectedIndex 选中项的索引
- size 选择框中可见的行数

HTMLOptionElement 

- index
- label 
- selected
- text
- value 

##### 选择选项
- selelctedIndex
- selected

```
var selectedOption = selectbox.options[selectbox.selectedIndex];
```
##### 添加选项
```
var newOption = new Option("option text", "option value");
selectbox.add(newOption, undefined);
```

##### 移除选项
- remove
- 将选项置为 null

##### 移动和重排选项
- insertBefore()

#### 表单序列化
```
function serialize(form) {
    var parts = [],
    field = null,
    i,
    len,
    j,
    optLen,
    option,
    optVale;
    for (i = 0, len = form.elements.length; i < len; i++) {
        field = form.elements[i];

        switch(field.type) {
            case "select-one":
            case "select-multiple": 
                if (field.name.length) {
                    for (j = 0, optLen = field.options.length; j < optLen; j++) {
                        option = field.options[j];
                        if (options.selected) {
                            optValue = "";
                            if (option.hasAttribute) {
                                optValue = (option.hasAttribute('value')) ? option.value : option.text;  
                            } else {
                                optValue = option.attributes["value"].specified ? option.value: option.text;
                            }
                            parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(optValue));
                        }
                    }
                }
                break;
            case undefined:
            case "file":
            case "reset":
            case "button":
                break;
            case "radio":
            case "checkbox": 
                if (!field.checked) {
                    break;
                }
            default:
                if (field.name.length) {
                    parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(field.value));
                }
        }
    }
    return parts.join("&");
}
```
#### 富文本编辑
 1.在页面中添加一个iframe, 设置它的designMode为“on”。

```
frames['richedit'].document.designMode = "on";
```

 2.给想要编辑的元素添加 contenteditable属性 

```
<div class="editable" id="richedit" contenteditable></div>
```

##### 操作富文本
- document.execCommand('bold', false, null)
可以设置文档的背景颜色，粗体，复制，剪切，插入元素，对齐等
- queryCommandEnabled("bold") 检测是否可以执行某个命令
- queryCommandState("bold") 确定是否已将指定命令用到了选择的文本
- queryCommandValue() 取得执行命令时传入的值

##### 富文本选区
iframe的getSelection() 可以确定实际选择的文本。  
Selection的属性

- anchorNode 选区起点所在的节点
- anchorOffset 到达选区起点位置之前跳过的anchorNode中字符数量
- focusNode 终点所在的节点
- focusOffset focusNode 中包含在选区之内的字符数量
- isColllapsed  起点和终点是否重合
- rangeCount 选区中包含的DOM范围的数量
Selection的方法
- addRange(range) 将指定的DOM范围添加到选区中 
- collapse(node,offset) 将选区折叠到指定节点中的相应的文本偏移位置
- collapseToEnd()  将选区折叠到终点位置
- collapseToStart()  将选区折叠到起点位置
- containsNode(node) 确定指定的节点是否包含在选区中
- deleteFromDocument() 从文档中删除选区中的文本
- extend(node, offset) 通过将focusNode 和 focusOffset 移动到指定的值来扩展选区
- getRangeAt(index)  范围索引对应的选区中的DOM范围
- removeAllRanges() 从选区中移除所有的DOM范围
- removeRange(range) 从选区中移除指定的DOM范围
- toString()  返回选区所包含的文本内容

```
var selection = frames['richedit'].getSelection();

// 取得选则的文本
var selectedText = selection.toString();

// 取得范围
var range = selection.getRangeAt(0);

// 突出显示选择的文本
var span = frames["richedit"].document.createElement("span");
span.style.backgroundColor = "yellow";
range.surroundContents(span);
```

富文本编辑器不是表单字段，因此将其内容提交给服务器之前，必须将iframe或contenteditable元素的HTML复制到一个表单字段中。 



