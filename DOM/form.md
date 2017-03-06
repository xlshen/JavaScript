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
      if(typeof textbox.selectStart === "number"){
        return textbox.value.substring(textbox.selectionStart, textbox.selectedEnd);
      }else if(document.selection){
        return document.selection.createRange().text; // 兼容IE8及之前版本
      }
    }
  </script>
```
##### 选择部分文本  
HTML5也为选择文本框中的部分文本提供了解决方案，除了select()方法外，所有文本框都有一个setSelectionRange()方法。这个方法接收两个参数：要选择的第一个字符的索引和要选择的最后一个字符的索引。类似substring()方法
```html
  <script>
    textbox.value = "Hello World!";
    textbox.setSelectionRange(0, 3);
    textbox.select();
  </script>
```
[注：要看到选择的文本，调用setSelectionRange之后或者之前立即将焦点设置到文本框]
##### 操作剪切板
HTML5中6个剪切板事件:  
1. beforecopy: 在发生复制操作前触发    
2. copy: 在发生复制操作时触发    
3. beforecut: 在发生剪切操作前触发    
4. cut: 在发生剪切操作时触发    
5. beforepaste: 在发生粘贴操作前触发    
6. paste: 在发生粘贴操作时触发   
要访问剪切板数据，使用clipboardData对象：IE中该对象是window对象属性；在其他浏览器，该对象是对应event对象的属性。但是只有在处理剪切操作期间有效。在IE中可以随时访问clipboardData对象；  
该对象有三个方法：getData()、setData()和clearData()。
```html
  <script>
    var Event = {
      getClipboardData: function(event){
        var clipboardData = (event.clipboardData || window.clipboardData);
        return clipboardData.getData("text");
      },
      setClipboardText: function(event, value){
        if(event.clipboardData){
          return event.clipboardData.setData("text/plain", value);
        }else if(window.clipboradData){
          return window.clipboardData.setData("text", value);
        }
      }
    };
  </script>
```
##### 自动切换焦点
```html
  <script>
    function tabForward(event){
      var target = event.target;
      if(target.value.length === target.maxLength){
        var form = target.form;
        for(var i = 0, len = form.elements.length; i < len; i++){
          if(form.elements[i] === target){
            if(form.elements[i + 1]){
              form.elements[i + 1].focus();
            }
            return;
          }
        }
      }
    }
  </script>
```
##### HTML5约束验证API
1. 必填字段 required，适用于input、textarea、select
```html
  <script>
    var isUsernameRequired = document.forms[0].elements["username"].required;
    // 验证浏览器是否支持required属性
    var isRequiredSupported = "required" in document.createElement("input");
  </script>
```
2. input增加了type属性值。其中email和url支持最多
```html
  <script>
    <input type="email" name="name" />
    <input type="url" name="home" />
    // 判断浏览器是否支持type属性
    var input = document.createElement("input");
    input.type = "email";
    var isEmailSupported = (input.type === "email"); // 不支持的自动将未知值设置为text,支持的会返回正确的值
  </script>
```
3. 输入模式
HTML5为文本字段新增了pattern属性。这个属性是一个正则表达式，用于匹配文本框中的的值。
```html
  <script>
    <input type="text" pattern="\d+" name="count" title="只能为数字"/>
  </script>
```
4. 检查有效性
使用checkValidity()方法可以检测表单中的某个字段是否有效。所有表单字段都有方法，如果有效，该方法返回true,否则false。
```html
  <script>
    if(document.forms[0].elements[0].checkValidity()){
      // 字段有效
    }else{
      // 字段无效
    }
    // 检测整个表单是否有效
    if(document.forms[0].checkValidity()){
      // 表单有效
    }
  </script>
```
