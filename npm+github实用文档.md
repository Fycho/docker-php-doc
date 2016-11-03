#一、通过npm将github上的包安装到项目
1.  进入项目文件夹： （这里以宿主机为例，虚拟机也差不多 ）  
	a)   运行cmd，进入相应盘，输入cd workspace/www/helloworld/hwtest  
2.  假如我要安装这个包：[https://github.com/expressjs/express](https://github.com/expressjs/express)  
	c)  获得这个包的地址：[https://github.com/expressjs/express.git](https://github.com/expressjs/express.git)  
	d)  命令行输入：npm install [https://github.com/expressjs/express.git](https://github.com/expressjs/express.git) 安装成功，效果如图：1-1　　
![图１－１](https://ooo.0o0.ooo/2016/10/31/581714afb8596.png)
3.  观察此刻的项目目录，会发现多了一些东西，如图1-2  
![图1-2](https://ooo.0o0.ooo/2016/10/31/581714afc2dbd.png)   
4.  使用时用CommonJS或者AMD/CMD等JS模块化标准引入就好，如：var express = require('express') 
5.  别忘了打包编译相应文件，以让浏览器能识别
	
#二、将github上的包发布到私有npm上  
1.  npm set registry http://192.168.100.230:4873    // 设置镜像，这里指向具体npm私库地址
2.  npm config list     //  显示配置
3.  npm login
4.  进入到有package.json 的文件夹下
5.  npm publish，效果如图1-3

#三、发布前配置Package.json

##准备工作
- Node.js环境
- 已安装npm包管理器的操作系

##快速配置
1. 从github把类库clone到本地或直接在本地创建的类库
- 在类库文件夹内执行命令npm init
- 根据提示输入一些属性后检查生成的package.json预览
- 其他的属性比如依赖包，则需要手动修改package.json
- $ npm publish

##常用属性说明
- name: 必填，不要在name中包含js, node字样，不能以点号或下划线开头；这个名字可能在require()方法中被调用，尽可能短
- verison: 必填，例如1.0.0
- entry point: 入口文件，可选字段。这个字段的值是你程序主入口模块的ID。如果其他用户需要你的包，当用户调用require()方法时，返回的就是这个模块的导出（exports）
- files: 可选，项目包含的一组文件。如果是文件夹，文件夹下的文件也会被包含。如果需要把某些文件不包含在项目中，添加一个”.npmignore”文件。这个文件和”gitignore”类似
- repository: 可选，用于指示代码位置。注意这仅是一个说明文件，不会和git产生实质的关联，对项目文件也没有影响
- scripts: 可选，配置一些用到的脚本
- dependencies: 可选，指示当前包所依赖的其他包
- devDependencies： 可选，如果只需要下载使用某些模块，而不下载这些模块的测试和文档框架，那么用它
- private: 可选字段，布尔值。如果private为true，npm会拒绝发布。用于防止私有repositories不小心被发布
- Author, contributors: 可选字段。author是一个人，contributors是一组人
 
****
更多属性：[npm package.json属性详解](http://www.cnblogs.com/tzyy/p/5193811.html)

#四、在项目中引用npm包
1. 配置实际项目自身的package.json，把需要的包列入dependencies属性
2. 执行命令npm install安装依赖包
3. 配置模块化加载工具如webpack
4. 相应文件里使用var test = require('testname'); 获得相应模块的导出。testname替换为对应的npm包名
5. 使用webpack打包、编译相应文件，使得浏览器能最终识别requrie函数

****
扩展阅读：[前端模块化实践----使用webpack打包js代码](http://www.cnblogs.com/ibufu/p/5078684.html)
