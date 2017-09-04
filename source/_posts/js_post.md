---
title: iframe+表单跨域提交POST请求
date: 2017-09-04 09:30:43
tags: 
   - JavaScript
   - 跨域
categories: Front-End
---

 ## 虚拟表单的形式提交post请求
 
 ```javascript
function post(URL, PARAMS) {
  var temp = document.createElement("form");
  temp.action = URL;
  temp.method = "post";
  temp.style.display = "none";
  for (var x in PARAMS) {
    var opt = document.createElement("textarea");
    opt.name = x;
    opt.value = PARAMS[x];
    // alert(opt.name)
    temp.appendChild(opt);
  }
  document.body.appendChild(temp);
  temp.submit();
  return temp;
}
```

- 调用方法

```javascript
post('pages/statisticsJsp/excel.action', {html :"fafd",cm1:'llld',cm2:'haha'});
```

 
