---
title: 使用hexo遇到过的问题
date: 2020-7-8
tags: [hexo,早期作品]

---

### 1. hexo g报的错

* 时区错误

```java
TypeError: Cannot read property 'utcOffset' of null
//解决方案: 把blog根目录_config.yml的timezone的值改成Asia/Shanghai  
```



