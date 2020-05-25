## 安装nodejs

在官网下载合适版本的nodejs，只直接下一步安装即可。

[nodejs官网](http://nodejs.cn/)



安装完成后在命令行输入下面的命令检查安装是否成功：

```shell
$ node --version
v10.16.3

$ npm --version
6.9.0
```





## 更换npm源

默认npm源国内访问很慢，可以更换为taobao的npm源：

```shell
$ npm config set registry https://registry.npm.taobao.org
$ npm config get registry

https://registry.npm.taobao.org/
```





## 安装vue-cli

vue-cli是vue的脚手架工具，可以用来初始化vue项目：

```shell
npm install -g vue-cli
```



> 使用 -g 参数来进行全局安装



输入下面的命令检查vue的版本：

```shell
$ vue -V
2.9.6
```



## 创建vue脚手架

输入下面的命令，创建一个vue的脚手架项目：

```shell
# 这里创建一个名为vueproject的项目
vue init webpack vueproject
```



> 注意项目名称不要有大写字母



在创建后会让输入一些参数，按需求输入即可。其中vue-router一般是要安装的，eslint是检查语法格式的，可以不用安装。

