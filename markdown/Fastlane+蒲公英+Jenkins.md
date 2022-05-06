# 前言
> 在项目测试阶段，频繁的打包发布会耗费很多的时间。学习使用了Fastlane实现了自动化打包发布到蒲公英&fir，以下内容是实现的过程。

### Fastlane安装

> Fastlane是一套使用Ruby写的自动化工具集，用于iOS和Android的自动化打包、发布等工作，可以节省大量的时间。

##### 安装过程如下：

---

1. 检查Ruby版本，需要2.0及以上版本。在终端输入以下命令确认：

```
ruby -v
```
![图1-1](https://shivinky.github.io/images/Snip20220420_6.png)

- 需要注意的是需要将gem的source改为https://gems.ruby-china.org/。有可能造成 bundle update 卡主问题
如何检查?在终端输入以下命令:
```
gem sources
```
结果应为：

![图1-2](https://shivinky.github.io/images/Snip20220420_3.png)

2. 检查Xcode命令行工具是否安装。在终端输入以下命令：

```
xcode-select --install
```
> 如果没有安装会进行安装。如果已经安装了则会提示(否则会弹出框让你去安装)：

![图1-3](https://shivinky.github.io/images/Snip20220420_16.png)

3. 安装Fastlane

```
通过 gem 安装
sudo gem install fastlane --verbose

也可以通过 brew 安装
brew install fastlane
```

4. 检查Fastlane是否正确安装。输入以下命令：

```
fastlane --version
```
![图1-4](https://shivinky.github.io/images/Snip20220420_7.png)


5. 设置 Fastlane 的环境变量
> 我这里采用的是 .zprofile
> /.profile, ~/.zshrc ，~/.bashrc

```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8

source ~/.zprofile
```

  
### Fastlane配置

##### 配置如下:
---

1. 打开终端，进入你的项目含有xxx.xcodeproj工程的根目录，输入以下命令：


```
fastlane init
```

> 新版本安装的时候会出现下面的分支，按要求选择就行
（1）自动截屏。这个功能能帮我们自动截取APP中的截图，并添加手机边框（如果需要的话）
（2）自动发布 beta 版本用于 TestFlight
（3）自动发布到 AppStore
（4）手动设置
 
![图1-5](https://shivinky.github.io/images/Snip20220420_9.png)

> 如果卡在上面这个图上：去替换 fastlane init 工程里面产生的 Gemfile 文件的源地址，记住是 刚刚 fastlane init 产生的 Gemfile 文件，

```
https://gems.ruby-china.com

或

source 'https://gems.ruby-china.com'

gem "fastlane"

plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
eval_gemfile(plugins_path) if File.exist?(plugins_path)


```

![图1-6](https://shivinky.github.io/images/Snip20220420_12.png)

> 然后 删掉 fastlane 文件夹 重新 fastlane init 成功界面如下:

![图1-7](https://shivinky.github.io/images/Snip20220420_13.png)

> 目前我选择的是4，按照提示， 多个Enter 后有可能会输入开发者账号和密码。安装成功之后，会在工程目录生成一个 fastlane 文件夹和 Appfile 和 Fastfile 两个文件。Appfile保存苹果开发者的相关信息、项目的相关信息等。Fastfile是运行脚本。 

![图1-8](https://shivinky.github.io/images/Snip20220420_14.png)

2. 蒲公英的Fastlane插件安装
> 执行命令安装
```
fastlane add_plugin pgyer
```
- 可能碰到一下错误 (删掉全部，重新弄一遍，我这样操作过了)
![图1-9](https://shivinky.github.io/images/Snip20220420_15.png)
![图1-10](https://shivinky.github.io/images/Snip20220420_20.png)

> 成功如下

![图1-11](https://shivinky.github.io/images/Snip20220420_10.png)

3. 编辑Fastfile文件 
> app-store,   AppStore正式生产环境包
ad-hoc,  生产测试包
enterprise,  企业包 
development  开发测试包

```
default_platform(:ios)

platform :ios do
  desc "Description of what the lane does"
  lane :custom_lane do #打包方法调用
    # add actions here: https://docs.fastlane.tools/actions

    gym(

        scheme: "", #项目名称 
        export_method:"development",#打包输出种类 
        output_directory: "", # 打包后的 ipa 文件存放的目录 
        output_name: ""  # ipa文件名 
    )

  end
end
```
![图1-12](https://shivinky.github.io/images/Snip20220420_11.png)

> 命令行打包（成功如下）

```
fastlane custom_lane
```
![图1-13](https://shivinky.github.io/images/Snip20220420_26.png)



### 上传到蒲公英 [Fastlane](https://www.pgyer.com/doc/view/fastlane) 

---
1. Fastlane配置 

```
default_platform(:ios)

#定义变量
scheme="Luxury" #项目名称 
api_key="c0b14970d735c076424f47bad8e985a2" #蒲公英 api_key
user_key="c471de66f0454ac885a43dc9db3f0c93" #蒲公英  

platform :ios do
  desc "Description of what the lane does"
  lane :custom_lane do |options|
    # add actions here: https://docs.fastlane.tools/actions

    gym(

        output_name:"#{scheme}_#{get_build_number()}", # ipa文件名 
        clean:true,
        configuration:"Release", 
        export_method:"ad-hoc",#打包输出种类 
        output_directory: "./fastlane/build", # 打包后的 ipa 文件存放的目录  
    )
  puts "开始上传蒲公英"
  pgyer(api_key: "#{api_key}", user_key: "#{user_key}")
  end
end
```
2. 执行打包上传蒲公英 （注：custom_lane 自定义调用标签）
```
fastlane custom_lane
```
> 如果出现一下错误，可能 [api_key 或 user_key](https://note.youdao.com/) 有误

![图1-14](https://shivinky.github.io/images/Snip20220420_22.png)
 
>  更换后成功如下: (去查看蒲公英就有最新版本了)

![图1-15](https://shivinky.github.io/images/Snip20220420_24.png)

### Jenkins 

---
#### 1、安装
> brew 安装方式终端执行

```
brew install jenkins
```
> 如果安装过程中出现以下错误

![图1-15](https://shivinky.github.io/images/Snip20220420_25.jpg)

> 解决方案:

① 更新Homebrew

```
	#更新HomeBrew
	cd "$(brew --repo)" 
	git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
	
	#更新HomeBrew-Core
	cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core" 
	git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
	
	#更新Homebrew-cask (最后一步，很多更新完国内源依然卡，就是没更新这个)
	cd "$(brew --repo)"/Library/Taps/homebrew/homebrew-cask 
	git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
	
	配置环境变量: (zprofile 或是 bash)
	
	# HomeBrew 镜像
	export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/

```

②. 升级Brew

```
brew update -v
或 
brew update-reset
```
> 再次执行brew install jenkins 就ok了 ( 它端起啤酒庆祝了 )

![图1-16](https://shivinky.github.io/images/Snip20220415_233.png)

#### 2、配置
> ①启动之后在浏览器中输入http://localhost:8080 就会进入Jenkins初始页面
按照提示填写管理密码点击继续 

![图1-16](https://shivinky.github.io/images/Snip20220506_8.png)

> ② 安装推荐

![图1-17](https://shivinky.github.io/images/Snip20220506_9.png)

> ③ 注册一个管理账号

![图1-18](https://shivinky.github.io/images/Snip20220506_10.png)

> ④ 配置URL (默认就可以)

![图1-19](https://shivinky.github.io/images/Snip20220506_11.png)


> ⑤ 新建任务

![图1-20](https://shivinky.github.io/images/Snip20220506_12.png)

![图1-21](https://shivinky.github.io/images/Snip20220506_13.png)

> ⑥ 配置任务
- 1、基本配置

![图1-22](https://shivinky.github.io/images/Snip20220506_14.png)

- 2、配置代码仓库，供jenkins拉取代码

![图1-23](https://shivinky.github.io/images/Snip20220506_15.png)


- 3、构建
> 因为我用的是 Fastlane 脚本，所以选择shell

![图1-23](https://shivinky.github.io/images/Snip20220506_16.png)

![图1-24](https://shivinky.github.io/images/Snip20220506_17.png)

- 4、执行构建

![图1-25](https://shivinky.github.io/images/Snip20220506_18.png)

> 如果报错 UTF-8 问题，则需添加如下 shell
  
```
export LANG=en_US.UTF-8

cd SDYunPro
fastlane archivie_lane
```

![图1-25](https://shivinky.github.io/images/Snip20220506_19.png)

