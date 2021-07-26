# css组件示例

## 一. 下拉菜单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <style>
        .box1{
            display: inline-block;
            width: 100px;
            height: 40px;
            background-color: #aabbaa;
            text-align: center;
            line-height: 40px;
            border-radius: 6px;
            cursor: pointer;
        }
        .box2{
            height: 50px;
            box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
            margin-top: 20px;
            width: 150px;
            /*border: 1px black solid;*/
            display: none;
        }
        .box1:hover .box2{
            display: block;
        }
    </style>
</head>
<body>
    <div class="box1">
        点我下拉
        <div class="box2">做的下拉菜单</div>
    </div>
</body>
</html>
```

## 二. 工具提示

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1{
            position: relative;
            display: inline-block;
        }
        .hov{
            text-align: center;
            background-color: black;
            color: white;
            /*width: 90px;*/
            display: inline-block;
            border-radius: 5px;
            padding: 5px 5px;
            font-size: 14px;
            visibility: hidden;
        }
        .hov{
            position: absolute;
            top: -5px;
            left: 106%;
        }
        .hov::after{
            content: " ";
            position: absolute;
            top: 50%; /* At the bottom of the tooltip */
            right: 100%;
            margin-top: -5px;
            border-width: 5px;
            border-style: solid;
            border-color: transparent black transparent transparent;
        }
        .box1:hover .hov{
            visibility: visible;
        }
    </style>
</head>
<body>
    <div class="box1">
        吃饭
        <span class="hov">喝水</span>
    </div>
</body>
</html>
```

