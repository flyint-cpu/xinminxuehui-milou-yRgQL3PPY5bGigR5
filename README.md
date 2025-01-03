[合集 \- DVWA(4\)](https://github.com)[1\.DVWA靶场搭建及错误解决教程2024\-12\-29](https://github.com/GuijiH6/p/18638537)[2\.DVWA靶场Brute Force (暴力破解) 漏洞low(低)，medium(中等)，high(高)，impossible(不可能的)所有级别通关教程及代码审计2024\-12\-30](https://github.com/GuijiH6/p/18640192)[3\.DVWA靶场Command Injection(命令注入) 漏洞low(低)，medium(中等)，high(高)所有级别通关教程及源码审计2024\-12\-31](https://github.com/GuijiH6/p/18643521)4\.DVWA靶场File Inclusion (文件包含) 漏洞所有级别通关教程及源码解析01\-01收起
# 文件包含


文件包含漏洞（File Inclusion Vulnerability）是一种常见的网络安全漏洞，主要出现在应用程序中不安全地处理文件路径时。攻击者可以利用此漏洞执行恶意文件，或者访问不该被访问的文件


## 1\. low


有3个页面


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229205952463.png)


随便点击一个，可以在url处发现传参点


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229210047668.png)


访问：[http://127\.0\.0\.1/DVWA/vulnerabilities/fi/?page\=file4\.php](https://github.com) ，可以看到下面的提示


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229210117652.png)


### 远程文件包含


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229210716697.png)


### 本地文件包含


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229210935502.png)


### 源码审计



```
php</span

// The page we wish to display
$file = $_GET[ 'page' ];

?>

```

可以看到没有做任何过滤


## 2\. medium


访问**file4\.php，**依然可以访问


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229211145433.png)


访问**?page\=../../phpinfo.php**发现没有回显，可以确定是被过滤了


尝试双写绕过，成功绕过


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229212019013.png)



```
..././ <=> ../

```

### 源码审计



```
</span php
// 显示的页面
$file = $_GET['page'];

// 输入验证
$file = str_replace(array("http://", "https://"), "", $file);
$file = str_replace(array("../", "..\\"), "", $file);
?>

```

将**http://; https://; ../ ; ..\\** 替换为了**' '** ,可以利用双写绕过过滤


## 3\. high


访问**1\.php**看看报错信息


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229212922864.png)


直接文件读取


![](https://track123.oss-cn-beijing.aliyuncs.com/20241229212758261.png)


### 源码审计



```
php</span
// 显示的页面
$file = $_GET['page'];

// 输入验证
if (!fnmatch("file*", $file) && $file != "include.php") {
    // 这不是我们想要的页面！
    echo "错误：文件未找到！";
    exit;
}  
?>

```

要求了参数必须以file开头，即**?page\=file..........** ，否则就会报错


## 4\. impossible


### 源码审计



```
php</span
  // 我们希望显示的页面
$file = $_GET['page'];

// 只允许 include.php 或 file{1..3}.php
if ($file != "include.php" && $file != "file1.php" && $file != "file2.php" && $file != "file3.php") {
    // 这不是我们想要的页面！
    echo "错误：文件未找到！";
    exit;
}
?>

```

只允许访问**include.php 或 file{1\..3}.php**页面，其他页面全部过滤


 \_\_EOF\_\_

       - **本文作者：** [track](https://github.com):[CMESPEED\-楚门加速器](https://cmnspeed.com)
 - **本文链接：** [https://github.com/GuijiH6/p/18645826](https://github.com)
 - **关于博主：** 评论和私信会在第一时间回复。或者[直接私信](https://github.com)我。
 - **版权声明：** 本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！
 - **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。
     
