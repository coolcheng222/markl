# Opencv简易api介绍

## 一. 

1. 导入和读取

```python
! $ pip install opencv-python
import cv2
cv2.imread('cat.jpg',[para])
# para : 1-rgb,0-grey scale,-1-unchanged
```

2. colorspace的转换

```python
image2 = cv2.cvtColor(image,flag)
# cv2.COLOR_BGR2GRAY
# cv2.COLOR_BGR2RGB
# cv2.COLOR_BGR2HSV

```

