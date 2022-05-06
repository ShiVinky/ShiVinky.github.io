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
![snip20220420_4.png](https://note.youdao.com/yws/res/4843/WEBRESOURCE35516f8bad36372ee11d35f19335d8d4)

- 需要注意的是需要将gem的source改为https://gems.ruby-china.org/。有可能造成 bundle update 卡主问题
如何检查?在终端输入以下命令:
```
gem sources
```
结果应为：
![snip20220420_3.png](https://note.youdao.com/yws/res/4839/WEBRESOURCE7e6a4a5d8a5a3cefc3f4f7ba72d266c0)


2. 检查Xcode命令行工具是否安装。在终端输入以下命令：

```
xcode-select --install
```
> 如果没有安装会进行安装。如果已经安装了则会提示(否则会弹出框让你去安装)：

![snip20220420_6.png](https://note.youdao.com/yws/res/4856/WEBRESOURCE89a830676d683c8684d2d9b6f03b14f3)

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
![snip20220420_7.png](https://note.youdao.com/yws/res/4876/WEBRESOURCE66574eb61465693dce94c3306bbf47ab)


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
 
![snip20220420_9.png](https://note.youdao.com/yws/res/4895/WEBRESOURCE3f6f6a9e3b5fb1bb819632c92b10ff90)

> 如果卡在上面这个图上：去替换 fastlane init 工程里面产生的 Gemfile 文件的源地址，记住是 刚刚 fastlane init 产生的 Gemfile 文件，

```
https://gems.ruby-china.com

或

source 'https://gems.ruby-china.com'

gem "fastlane"

plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
eval_gemfile(plugins_path) if File.exist?(plugins_path)


```

![snip20220420_12.png](https://note.youdao.com/yws/res/4909/WEBRESOURCEec875dddc86d7c5266f65b34c6f4aca1)

> 然后 删掉 fastlane 文件夹 重新 fastlane init 成功界面如下:

![snip20220420_13.png](https://note.youdao.com/yws/res/4916/WEBRESOURCE8c01e30ac60a77cca4206e2b49143d14)

> 目前我选择的是4，按照提示， 多个Enter 后有可能会输入开发者账号和密码。安装成功之后，会在工程目录生成一个 fastlane 文件夹和 Appfile 和 Fastfile 两个文件。Appfile保存苹果开发者的相关信息、项目的相关信息等。Fastfile是运行脚本。 

![snip20220420_14.png](https://note.youdao.com/yws/res/4920/WEBRESOURCEc506f403e312da844bc91d82ebb41a41)

2. 蒲公英的Fastlane插件安装
> 执行命令安装
```
fastlane add_plugin pgyer
```
- 可能碰到一下错误 (删掉全部，重新弄一遍，我这样操作过了)
![snip20220420_15.png](https://note.youdao.com/yws/res/4936/WEBRESOURCEe4d0f801db6cdd534c1a4a74f553eab4)
![snip20220420_16.png](https://note.youdao.com/yws/res/4940/WEBRESOURCEe9c6de542542d829a028a5d3b86f4bff)

> 成功如下

![snip20220420_10.png](https://note.youdao.com/yws/res/4947/WEBRESOURCEa6e1ef321de9abc981f7ac164f03dd67)

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
![snip20220420_11.png](https://note.youdao.com/yws/res/4957/WEBRESOURCE0eb11a2b79c7e8f78cf52c12de41c55c)

> 命令行打包（成功如下）

```
fastlane custom_lane
```
![snip20220420_12.png](https://note.youdao.com/yws/res/4973/WEBRESOURCE6351995860f3abd5861cc125dcf68568)



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

![snip20220420_13.png](https://note.youdao.com/yws/res/5003/WEBRESOURCEbe51f9599ef16e411a25da3d4d8dfd9f)
 
>  更换后成功如下: (去查看蒲公英就有最新版本了)

![snip20220420_14.png](https://note.youdao.com/yws/res/5012/WEBRESOURCE0a3fc98c1a9055e236f9ae9c69c74f8c)

### Jenkins 

---
#### 1、安装
> brew 安装方式终端执行

```
brew install jenkins
```
> 如果安装过程中出现以下错误

![1891649921336_.jpg](https://note.youdao.com/yws/res/5059/WEBRESOURCEd0b7e5fa3a788884cc30e1995c52a5b0)

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

![snip20220415_2333333.png](https://note.youdao.com/yws/res/5091/WEBRESOURCE159e6dae2c36118586bd1ac934bd2e61)

#### 2、配置
> ①启动之后在浏览器中输入http://localhost:8080 就会进入Jenkins初始页面
按照提示填写管理密码点击继续 

![snip20220415_344444.png](https://note.youdao.com/yws/res/5099/WEBRESOURCE63a0e5aa65f5d2f8be56ecee356097c0)

> ② 安装推荐

![snip20220415_4666.png](https://note.youdao.com/yws/res/5105/WEBRESOURCEa98b85bc17cd9c6e52e89dfc63e97acf)

> ③ 注册一个管理账号

![snip20220415_59999.png](https://note.youdao.com/yws/res/5110/WEBRESOURCE4e8bc445499da55ddecca69c0d6974e0)

> ④ 配置URL (默认就可以)

![snip20220415_3333333333333333.png](https://note.youdao.com/yws/res/5115/WEBRESOURCE1b3222623a2c24f3825ff38b3e93882a)


> ⑤ 新建任务

![snip20220423_3.png](https://note.youdao.com/yws/res/5120/WEBRESOURCE514240bd71e42989e05e04a5f4bf8eab)

![snip20220423_6.png](https://note.youdao.com/yws/res/5123/WEBRESOURCE3a5f40fd7baa537e27aa824a20bf1d5e)

> ⑥ 配置任务
- 1、基本配置

![snip20220423_7.png](https://note.youdao.com/yws/res/5130/WEBRESOURCEa049df6918e5e4ab244d644e479d755b)

- 2、配置代码仓库，供jenkins拉取代码

![snip20220423_9.png](https://note.youdao.com/yws/res/5136/WEBRESOURCE412f946d1e9ad7aeacd803a6faaa4fd3)


- 3、构建
> 因为我用的是 Fastlane 脚本，所以选择shell

![snip20220423_10.png](https://note.youdao.com/yws/res/5143/WEBRESOURCEff21e2db3ca0e230807118f83ff1e448)

![snip20220423_11.png](https://note.youdao.com/yws/res/5145/WEBRESOURCE7d56e152f6e4b98cecc2c604d8e6eb37)

- 4、执行构建
```

```

