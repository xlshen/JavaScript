### Text类型
特征：  
1. `nodeType`的值为3  
2. `nodeName`的值为`“#text”`  
3. `nodeValue`值为所包含的文本  
可以通过`nodeValue`或者`data`属性访问`text`文本，这两个属性值相同，对`nodeValue`修改也会体现在`data`上
##### 文本节点操作方法：
> 1. `appendData(text)`:将`text`添加到节点尾部
2. `deleteData(offset, count)`:从`offset`指定位置删除`count`个字符
3. `insertData(offset, text)`:从`offset`指定位置插入`text`
4. `replaceData(offset, count, text)`:用`text`替换从`offset`开始到`offset+count`为止的文本
5. `splitText(offset)`:从`offset`指定的位置将文本分为两个文本节点
6. `substringData(offset, count)`:提取从`offset`指定位置到`offset+count`位置的字符串

##### 创建文本节点
> `document.createTextNode(text)`创建文本节点，`text`为要创建的文本
