---
title: "js的ajax方式"
date: 2019-05-08T14:01:43+08:00
draft: false
author: "caicai"
tags: ["html", "web", 'ajax']
categories: ["web"]
---

JS通过ajax调用接口实现数据传送，第一种方式为设置button的点击`onclick`方法，第二种通过绑定`bind`点击事件获得访问

用`onclick`方式：
```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="content-type" content="text/html; charset=UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- src值为文件位置路径 -->
    <script type="text/javascript" charset="UTF-8" src="javascript/jquery-1.12.1.js"></script>
    <title>测试案例</title>
    <!-- 语法：jQuery.getJSON(url,data,success(data,status,xhr)) -->
    <script type="text/javascript" charset="UTF-8">
        function getToken(){
            $.getJSON("http://localhost/kdapi/api/access_token", {"id":111,"secret":2352532}, function(result){
                alert(result.access_token);
            });
        }
    </script>
</head>
<body>
    <button onclick="getToken()" style="width: 120px; height: 60px;">获取Token</button>
</body>
</html>
```

