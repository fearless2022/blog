## 标签含义

### ` ＜dependencies＞`

* 即使在子项目中不写该依赖，子项目任然会从父项目中继承该依赖项（全部继承）

### `＜dependencyManagement＞`

* dependencyManagement里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom;另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本

### `<type>pom</type>`

* 把spring-boot-dependencies引入为pom 文件

### `<scope>import</scope>`

* 解决单继承问题，类似parent标签，把spring-boot-dependencies引入到dependencyManagement

### `<packaging>pom</packaging>`

* 使用maven分模块管理，都会有一个父级项目，pom文件一个重要的属性就是packaging（打包类型），一般来说所有的父级项目的packaging都为pom，packaging默认类型jar类型，如果不做配置，maven会将该项目打成jar包