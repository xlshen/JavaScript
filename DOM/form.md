#### 表单脚本

##### 表单字段
每个表单都有elements属性，该属性是表单中所有元素的集合。该集合是一个有序列表，其中包含着表单所有字段。可以通过位置和name特性访问
```html
  <script>
    var form = document.getElementById("form1");
    // 表单第一个元素
    var field1 = form.elements[0];
    // 名称为textbox字段
    var textbox = form.elements["textbox"];
    // 表单所有字段数量
    var fieldCount = form.elements.length;
  </script>
```
如果有多个表单控件都使用同一个name，那么返回一个NodeList。但通过form.elements["color"]中第一个元素相同。
##### 选择文本
select()方法用于选择文本框中的所有文本。调用时都会把焦点设置到文本框中。  
select事件在选择文本时触发  
取得选择的文本，HTML5扩展了方案解决，添加了两个属性：selectionStart和selectionEnd。都是基于0的数值，表示所选择文本的范围。
```html
  <script>
    function getSelectedText(textbox){
      textbox.value.substring(textbox.selectionStart, textbox.selectedEnd);
    }
  </script>
```
