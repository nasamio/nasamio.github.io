#### CMP是什么

cmp 是区别于kmp的一种叫法，分别是：

- kmp：kotlin mutilplatform 使用kt编写跨端应用的 主要是解决android/ios的跨端需求，使用相同的逻辑和不同平台的ui
- cmp：compose mutilplatform 也是使用kt编写跨端应用 可以在一个工程下实现所有平台的代码编写，并且逻辑和ui都可以复用

#### 官网

[官网地址](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-multiplatform-create-first-app.html#create-a-project-using-the-wizard)

点击可以打开上面的官网地址，查看不同的官网信息。

#### 生成wizard包

[生成地址](https://kmp.jetbrains.com/?_gl=1*daj565*_gcl_au*OTg5MDYwMTYyLjE3MjQ5MTAwMTA.*_ga*NjQ3MDIwNDcyLjE3MjQ5MTAwMDg.*_ga_9J976DJZ68*MTcyNDkyMDQ1My4zLjEuMTcyNDkyMDQ2NS40OC4wLjA.)

点击上面的地址，勾选你需要的平台即可。ps：ios受制于苹果的限制，只能使用mac电脑进行开发，这里我就不勾选ios了。

<img src="e8b3a854d0f807336c99e064c04f0a14.png" alt="截图" style="zoom:50%;" />

接着，将下载的zip文件解压到合适的位置，用idea打开即可。

目录结构：

![截图](4b5af220f97c872dc9b89f160f66f27f.png)

- composeApp：主要是ui代码及原生实现（不需要改）
- server：服务端代码，使用ktor实现
- shared：主要是功能性代码

#### 如何启动

下载好的就是一个模板，不需要任何代码就可以启动。默认是使用gradle作为构建工具的，点击gradle打开构建工具：

<img src="20511859d84cabe7d2df3e6f24d17538.png" alt="截图" style="zoom:50%;" />

##### 跑安卓：

点击如图的installDebug即可：

<img src="963dbba0de50c16d98602fb472d3fc95.png" alt="截图" style="zoom:50%;" />

这时候你会发现，确实安装了安装包，但是需要手动点击才能运行，这也太蠢了。

我们添加一个task来自动安装：

<img src="5b295135b3e3b50e246d663f8d926858.png" alt="截图" style="zoom:50%;" />

在composeApp模块下的build.gradle.kts的android闭包中加入如下代码：

```
// 增加一个安装安卓应用并且运行的task
    val installDebugAndRun = tasks.register("installDebugAndRun") {
        dependsOn("installDebug")
        group = "install"
        description = "Install debug apk and run"
        doLast {
            val adb = android.sdkDirectory.resolve("platform-tools").resolve("adb")
            val packageName = android.defaultConfig.applicationId
            val process = ProcessBuilder(adb.toString(), "shell", "am", "start", "-n", "$packageName/.MainActivity")
                .redirectOutput(ProcessBuilder.Redirect.INHERIT)
                .redirectError(ProcessBuilder.Redirect.INHERIT)
            process.start()

        }
    }
```

之后点击右上角的gradle中的同步按钮：

<img src="51ec92d53c67c4559b37911e62721144.png" alt="截图" style="zoom:50%;" />

等待同步完成后，你的install中就多了一个task：

<img src="61b20db9900c431a21e2a8cd303304d0.png" alt="截图" style="zoom:50%;" />

点击即可运行你的安卓应用啦。

你也可以像我一样，给每个任务取个名字：

<img src="07572af94d9a53d24ba6d2b549852a62.png" alt="截图" style="zoom:50%;" />

<img src="3edcff24861461cd96d38b3f8397fb99.png" alt="截图" style="zoom:50%;" />

<img src="2c8f11096064c9d99b73c06932a1cb8c.png" alt="截图" style="zoom:50%;" />

在上面输入你想要的名字，点击apply就行了。后面别的模块也是一样的，就不再赘述了。

补一个安卓运行的图，我这里是一个横向的安卓平板真机：

<img src="9219d00da97e1bfc086adaf5094fead7.png" alt="截图" style="zoom:20%;" />

<img src="f9e34b22815b4891a9a21fb7ce2328a8.png" alt="截图" style="zoom:20%;" />

点击会显示一个图，再次点击会回到初始状态；这里下面其实还有个文字，我这里由于屏幕比例原因没显示出来。

##### 桌面端运行

<img src="3f24860b58ef8ab03f53aa70a22ecfe2.png" alt="截图" style="zoom:50%;" />

<img src="322fbc0d61097f3f04528b2ab8b826db.png" alt="截图" style="zoom:50%;" />

<img src="8d7e5b658d56e3444e68c37a704c2145.png" alt="截图" style="zoom:50%;" />

<img src="93b947f040a83d887b0fd5e2c802611f.png" alt="截图" style="zoom:50%;" />

##### web网页运行

![截图](7cfb56acc2321de2657eda8bd8743c11.png)<img src="bba384725db4e55f7f9717aa2df13a02.png" alt="截图" style="zoom:20%;" />

<img src="241550ef00c382513568fab57fa3a2dd.png" alt="截图" style="zoom:20%;" />

##### 服务器运行

<img src="17b7d5be4a7ee4980c51322f70f8ad0b.png" alt="截图" style="zoom:50%;" />

运行后控制台会有以下内容，说明你运行成功了：![截图](56ec900e4a1395e42019d57b26fd2ed6.png)

访问该地址，浏览器会显示get的相应信息，例如我应该在浏览器访问：

```
http://127.0.0.1:9001
```

ps：我这里是改了端口号，我发现默认用的8080和web的端口号重复了；修改位置在shared/src/commonMain/kotlin/com/mio/Constants中的SERVER_PORT