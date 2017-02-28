#### 事件委托
```html
  <script>
    var list = document.getElementById("list");
    Event.addHandle(list, "click", function(event){
      event = Event.getEvent(event);
      var target = Event.getTarget(event);
      switch(target.id){
        case "doSomething":
          document.title = "I changed the doc title.";
          break;
        case "goSomething":
          location.href = "XXX";
          break;
      }
    });
  </script>
```
