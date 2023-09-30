### F12

1. 打开网站

2. 关闭浏览器js
3. 打开审查元素
4. 刷新

### Hurry

访问带参数`?web=`

### Cat

* 尝试1: 题目里说全是01的地方,我就用01组合爆破了很久.

后来试了试`0cat1`,发现关键词其实是cat

猜想后端会按照返回的内容运行cat指令,于是就试了试

```bash
cat /etc/profile
```

这条是有返回结果的

* 尝试2: 于是我尝试了重定向`>`,这时题目返回了什么`give someome a girl friend`,我就

```bash
cat girlfriend > someome
```

但是没有效果

* 尝试3: 使用管道符发现可以运行其他指令,比如ls,find之类的,就动用find寻找flag

  ```bas
  cat a |find / -name *flag*
  ```

  发现根目录下有个`/flag`,然后就尬住了,等到hint出来才知道需要字符集转换

  iconv转换一下,发现是假的,真的flag在`/bin/catcatcat`(/flag里给出了指示)

### 真假flag

直接扫码

### 八十八万行

输出只有`Wrong Answer`没有其他的,猜想是没有出现在if语句中的数字才是正确的,所以写个程序筛选一下就行(最后记得转换成字符串)

```java
public class Main2 {
    public static void main(String[] args) throws IOException {
        FileReader fs = new FileReader("88w.py");
        BufferedReader bf = new BufferedReader(fs);
        int[][] arr = new int[44][2048];
        for(int i = 0;i < 880000;i++){
            String s = bf.readLine();
            if(s != null && s.contains("flag[")){
                s = s.split("flag\\[")[1];
                String[] a = s.split("\\]\\) == ");
                Integer a1 = Integer.parseInt(a[0]);
                Integer a2 = Integer.parseInt(a[1].substring(0,a[1].length() - 1));
                arr[a1][a2] = 1;
            }
        }
        for (int i = 0; i < arr.length; i++) {
            for (int j = 1; j < arr[i].length; j++) {
                if(arr[i][j] == 0){
                    System.out.print((char)(j));
                }
            }
        }
    }
}
```

### 一只小小鸟

拿到文件,一张图一个游戏一个data.win,猜想题解和data.win有关

然而,我没这个实力,就开始摆了,开局乱点,发现最上面的部分是空的,可以直接过去.

过完这关我手都麻了,不要问我为什么不开连点,我不会.

### 500w

起手一个包告诉我答对666个就能赢,在控制台输入答案然后按下回车就能回答

分析: 是TCP,发过来的报文中含有题目,我只需要返回含答案的报文就行

只不过需要注意最后那个回车,不加上后台就不会返回报文

程序:

```python
from socket import *
import re
import time
# 创建socket
tcp_client_socket = socket(AF_INET, SOCK_STREAM)

server_ip="202.198.27.90"
server_port= 20270
tcp_client_socket.connect((server_ip, server_port))
s=1
count = 0
while count < 669:

	recvData = tcp_client_socket.recv(1024)
	res = str(recvData);
	print(res);
	a = re.findall('\. (.+?) (.+?) (.+?) =',res);
	s=0;
	if(len(a) >= 1):
		count = count + 1;
		b = int(a[0][0])
		c = int(a[0][2])
		if a[0][1] == '+':
			s = b + c
		if a[0][1] == '-':
			s = b - c	
		if a[0][1] == '*':
			s = b * c	
		print(s)
		tcp_client_socket.send(bytes(str(s) + '\n', encoding = "utf8"))

# 关闭套接字
tcp_client_socket.close()
```

