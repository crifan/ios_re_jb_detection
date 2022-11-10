# iOS逆向开发：越狱检测和反越狱检测

* 最新版本：`v1.0`
* 更新时间：`20221110`

## 简介

介绍iOS逆向期间涉及到的iOS的越狱检测和反越狱检测方面的内容。包括常用的越狱检测手段，比如URL Scheme、文件方面的：文件属性、文件打开和文件写入；其中文件打开又包括C函数和iOS函数，其中C函数又包括syscall的C函数和svc 0x80内联汇编；以及文件写入包括C函数和iOS函数；以及其他检测手段：环境变量、是否可调试、system、沙箱完整性校验、越狱相关进程、dyld动态库，包括dylib的dladdr、dlopen、dlsym和_dyld开头的系列函数、ObjC运行时、app本身、已安装app、SSH相关、getsectiondata等；以及上述各种方法的反越狱检测的具体实现，以及其他一些额外的反越狱检测手段，比如内核级反越狱；接着整理越狱检测和反越狱检测的通用的内容，比如app的启动过程、越狱路径相关，尤其是越狱路径列表。以及其他一些心得。且均已贴出相关的越狱检测和反越狱检测的代码段和独立的代码仓库。

## 源码+浏览+下载

本书的各种源码、在线浏览地址、多种格式文件下载如下：

### HonKit源码

* [crifan/ios_re_jb_detection: iOS逆向开发：越狱检测和反越狱检测](https://github.com/crifan/ios_re_jb_detection)

#### 如何使用此HonKit源码去生成发布为电子书

详见：[crifan/honkit_template: demo how to use crifan honkit template and demo](https://github.com/crifan/honkit_template)

### 在线浏览

* [iOS逆向开发：越狱检测和反越狱检测 book.crifan.org](https://book.crifan.org/books/ios_re_jb_detection/website/)
* [iOS逆向开发：越狱检测和反越狱检测 crifan.github.io](https://crifan.github.io/ios_re_jb_detection/website/)

### 离线下载阅读

* [iOS逆向开发：越狱检测和反越狱检测 PDF](https://book.crifan.org/books/ios_re_jb_detection/pdf/ios_re_jb_detection.pdf)
* [iOS逆向开发：越狱检测和反越狱检测 ePub](https://book.crifan.org/books/ios_re_jb_detection/epub/ios_re_jb_detection.epub)
* [iOS逆向开发：越狱检测和反越狱检测 Mobi](https://book.crifan.org/books/ios_re_jb_detection/mobi/ios_re_jb_detection.mobi)

## 版权和用途说明

此电子书教程的全部内容，如无特别说明，均为本人原创。其中部分内容参考自网络，均已备注了出处。如发现有侵权，请通过邮箱联系我 `admin 艾特 crifan.com`，我会尽快删除。谢谢合作。

各种技术类教程，仅作为学习和研究使用。请勿用于任何非法用途。如有非法用途，均与本人无关。

## 鸣谢

感谢我的老婆**陈雪**的包容理解和悉心照料，才使得我`crifan`有更多精力去专注技术专研和整理归纳出这些电子书和技术教程，特此鸣谢。

## 其他

### 作者的其他电子书

本人`crifan`还写了其他`150+`本电子书教程，感兴趣可移步至：

[crifan/crifan_ebook_readme: Crifan的电子书的使用说明](https://github.com/crifan/crifan_ebook_readme)

### 关于作者

关于作者更多介绍，详见：

[关于CrifanLi李茂 – 在路上](https://www.crifan.org/about/)
