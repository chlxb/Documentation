# CocodPods 创建 SVN 私有库 
## 索引
* [背景](#title1)
* [准备](#title2)
* [开始](#title3)
* [后续](#title4)
* [常见问题](#title5)


## <a name=title1>一、背景</a>

随着业务需求的不断发展与变化，单工程项目的开发暴露了代码庞大、高耦合、以及内部功能复杂、运行缓慢、复用度低等缺点。面对以上情况，业界主流的重构方向是对项目进行组件化拆分，将单独功能、底层模块等以独立组件的形式拆分，再通过堆积木的方式组合起来，快速生产具备一组特定功能的应用。组件间各自独立、在最大解耦的同时，又可以相互依赖，形成一个整体。一种可行的方案就是通过 CocoaPods 来解决项目之间的依赖关系。

## <a name=title2>二、准备</a>

CocoaPods 是 iOS 开发过程中常用的解决快速集成和依赖第三方库的方式之一，CocoaPods 工作原理如图所示.

![Image text](/img/4D522EDA-4A08-40CA-BD5B-BB8C8E95A7FF.jpeg)

用户在终端执行 pod install 命令的时候，cocoapods 会根据本地的 Spec 仓库索引到对应的库的 podspec 文件，根据文件中的设置来配置依赖。在安装 CocoaPods 的时候，会自动在`~/.cocoapods`下创建默认的官方 master repo。按照它的工作原理，如果我们可以创建私有 Spec Repo，并且可以使 pod install 的时候根据我们私有 Repo 中的Spec 进行对依赖的安装就可以了。实际上，CocoaPods 文档上指出，我们可以根据自己的需求添加自己的私有库，但是文档上支持的是基于 git 的私有库，文档也指出是支持 SVN 的。那么接下来，来看一看正确的创建姿势吧，主要流程如下：

> 1. 安装 CocoaPods
> 2. [安装 CocoaPods 插件 cocoapods-repo-svn](#install-cocoapods-repo-svn)
> 3. [创建私有`Spec`文件 SVN 仓库](#create-spec-svn)
> 4. [创建私有库项目的 SVN 仓库](#create-framework-svn)
> 5. [本地添加 CocoaPods 私有 Repo](#create-private-repo)
> 6. [创建项目的 podsepc文件（推送到私有 Repo）](#create-podspec-file)
> 7. [编辑集成项目的 podfile 文件，pod install](#create-podfile)


[cocoapods-repo-svn-github]: https://github.com/dustywusty/cocoapods-repo-svn

***我们假设你已经安装了 CocoaPods，因此我们直接介绍从第二步之后的内容。在此，我们先从第五步开始***

## <a name="title3">三、开始</a>

### <a name="install-cocoapods-repo-svn">3.1 安装 CocoaPods 插件</a>
cocoapods-repo-svn 是 CocoaPods 的一个插件，在创建 SVN 的私有库时，此插件可以辅助项目安装过程中更加方便。具体的安装使用可以参考 [github for repo-svn][link-cocoapods-repo-svn-github]

**安装：**

```
gem install cocoapods-repo-svn 
```
安装过程 ruby的文件夹可能没有写入权限，可以尝试使用 `sudo`权限执行安装

**repo-svn使用：**

repo-svn 是pod repo 的命令增强，具体使用可以参看 pod repo 命令

```
pod repo-svn add `Repo` URL [BRANCH]	// 将远程仓库添加为本地索引 Repo。该操作会将远程的 spec 文件仓库克隆到 ~/.cocoapods/repos目录
pod repo-svn update `Repo` // 更新指定 repo
pod repo-svn push `Repo` [NAME.podspec] // 将指定 podsepc 文件推送到指定 repo
```

***repo-svn 不是 svn私有库的必须项，但是有了插件可以使功能更加便捷，具体可以查看文章后面的内容***

### <a name="create-spec-svn">3.2 创建`Spec`SVN 仓库</a>

**在 SVN 上创建 podspec 文件的仓库，假设命名为`SpecRepo`（仓库下不需要 trunk、branck、tags 等目录）**

文章最开始就已经简单介绍了 Pod 的工作原理，所以只要 pod 可以正确获取到对应库的 podspec 文件，就能够根据文件内容去配置库的依赖。如果公司是以 git 作为版本控制，那么按照官网的文档，创建私有的 spec git 仓库即可满足需求。那么当版本控制使用的是 svn 的时候，也可以是同样的思路，可以创建 spec 的 git 仓库，私有库项目放在 svn 上。spec 仓库可以自己搭建，也可以使用公共的诸如 coding、码云、github 等网站创建私有仓库。但这种同时使用 svn 跟 git 的方案看起来有点奇葩，也有点不够统一。实际上，我们完全可以使用 svn 的仓库来存放 podspec 文件。

### <a name="create-framework-svn">3.3 创建私有库的 SVN 仓库</a>

创建准备作为私有库的项目，此处我们以 `LXBLog` 项目为例，该项目功能是根据预设级别打印日志，假设我们要将该功能做成一个 framework，供别的项目使用。一般情况下，我们需要对工程做一些设置，比如添加header文件、资源文件、编译文件、引用的其他库，甚至还有比如 `-ObjC`, 静态库标识等。

![Image text](/img/095DE7F7-4B50-4D8D-843B-8C2F5F7C85D6.png)

![Image text](/img/69CD720A-BA16-40FB-9A4F-0757FFDBF6B3.png)

在项目目录下创建 `Classes` 目录用来存储 framework 需要编译的所有文件，创建 LXBLog.bundle 用来存放所有的资源文件。编写好代码之后，往常情况是我们编译生成对应的 framework 。现在我们用 Pod 来管理依赖，所以我们需要创建 podspec文件来告诉 Pod，我们的库的运行环境、版本、依赖、资源文件、编译文件等。由于是用作项目开发，我们可以添加 tags、trunk、branch 等目录。

***到目前为止，我们已经有两个 SVN 仓库了，一个用来存放私有 podspec 文件的 `SpecRepo`，一个用来存放我们私有库的代码 `LXBLog`***

### <a name="create-private-repo">3.4 添加 CocoaPods 私有 Repo</a>

Pod 是从本地的索引库先去找到需要的库，然后再去执行下载库与依赖安装的。因此我们要将第二步创建的 podspec 文件的 svn 仓库添加为 Pod 本地索引库，以便在执行 pod install 或者 update 的时候能找到对应的库。
首先在终端执行`pod repo`命令，默认情况下你都会一个 `master`仓库的输出，

![Image text](/img/A022DE09-701D-43DC-86A9-217247D30B80.png)

这个就是 CocoaPods 的默认索引仓库。如果需要将一个 git仓库设为索引库，只需要执行:

```
pod repo add NAME URL [BRANCH]
```

命令即可，其中 NAME 是本地的仓库名称，url 是 git 地址。**但是**，如果是一个 svn 仓库地址，如`svn://192.168.0.88/ios/SpecRepo`的地址，你会发现执行时会报错，此时就需要用我们的插件了。假设我们将本地仓库命名为 `TJSpecRepo `,那么命令应该这么写：

```
pod repo-svn add TJSpecRepo svn://192.168.0.88/ios/SpecRepo
```
再执行 `pod repo`，输出如下，除了 master 多了一个 TJSpecRepo。

![Image text](/img/F59FC0E4-E083-4B5B-BF30-BB1FC5125630.png)

### <a name="create-podspec-file">3.5 编写podsepc文件</a>

以上工作完成之后，要编写 podspec 文件，好让 Pod 知道我们的库在被人使用时，需要遵守的规则、编译的代码文件、资源文件、引入的系统库、依赖的第三方等等。我们把 [3.3](#create-framework-svn)创建的项目克隆到本地，一般情况，我们会把每个项目的 podspec 文件放在项目中，有修改时再推送到私有索引库中，我们在 LXBLog项目下，执行命令：

```
pod spec create LXBLog
```

之后，在项目目录下可以看到多出了一个 `LXBLog.podspec` 的文件。

![Image text](/img/1BA7F257-CD25-449C-811E-77E0BC2E2A93.png)

打开该文件，里面的内容就是 podspec 文件的默认内容，注释部分对每一项也有详细的解释，另外我们还可以参看官网的 [Podspec Syntax Reference][link-Podspec-Syntax-Reference]，官方文档对没所有的设置项都做了介绍。

```
---
# 名字，用来使用或者搜索时的名字
s.name         = "LXBLog" 	
# 版本号
s.version      = "0.0.1"	
# 简介
s.summary      = "A short description of LXBLog."  
# 详情描述，用来详细描述库的用途
s.description  = <<-DESC
                 DESC
# 主页，可以是官网，github 地址等可用地址，否则会报错
s.homepage     = "http://EXAMPLE/LXBLog" 
# 授权 license，私有库往往是在局域网使用，只要存在 license 即可，如果是要开源的库，那么要注意你自己选择的授权。
s.license      = "MIT (example)"	
# 邮箱
s.author       = { "author name" => "author_email@163.com" }
# source 对应的私有库地址，及用来检索库的地址，官网上对此项有比较多的例子来介绍
s.source       = { :git => "http://EXAMPLE/LXBLog.git", :tag => "#{s.version}" }
# 项目所包含的代码源文件，可以是 swift、h、m、mm、c、cpp 等格式的文件
s.source_files  = "Classes", "Classes/**/*.{h,m}"
s.dependency "JSONKit", "~> 1.4"	# 项目的其他 CocoaPods 库的依赖
---
```

![Image text](/img/CA13962C-2AAE-4BF8-8642-D7EDBC077E74.png)

![Image text](/img/6B7E6203-7F70-47C8-B789-583F163A6C75.png)

podspec 文件编辑好之后，可以通过终端命令`pod lib lint`来验证语法以及格式是否正确，关于该命令的使用可以参看 [Command-line-Reference][link-Command-line-Reference]。终端会给出错误的原因以及错误所在，按照提示进行修改直到可以通过验证。license文件要存放在指定的路径下，例如上图中 `s.license = { :type => "MIT", :file => "LICENSE" }` 那么在项目目录下，需要存在对应的名为 `LICENSE` 的文件。由于我的例子使用 swift 编写，swift_version 在低版本的 Pod 上面是不支持的，因此某些错误情况可能需要先对 CocoaPods 进行升级，编辑该文档时，我的 CocoaPods 版本是 `1.5.3`

![Image text](/img/3F71EF6B-BCBD-45A4-9683-23F58A98634D.png)

之后我们把便捷好的 podspec 文件推送到我们本地的私有 SpecRepo 中，**执行命令:**

```
pod repo-svn push TJSpecRepo LXBLog.podspec
```
我们再看远程的 SpecRepo，此时也出现了提交记录，打开之后发现，LXBLog.podspec 也被推送到的远程仓库，可见在执行上面命令的时候，同时也会对我们的修改提交到远端的 svn 仓库，git 也是如此。以上工作完成之后，也可以像搜索别的第三方库一样使用 pod search 命令来搜索我们自己的库。
**执行命令:**
```
pod search LXBLog
```
***注意：如果以上搜索没有结果，需要将`~/Library/Caches/CocoaPods/`路径下的`search_index.json`文件删除，然后执行命令即可***

### <a name="create-podfile">3.6 Podfile</a>

万事俱备！现在可以使用我们的私有库了，创建 `PodTest` 项目,在项目目录下**执行命令：**
`pod init`会在 PodTest 中生成了默认的 Podfile 文件。假设我们的项目还要只用 Alamofire 网络库，因此将内容按照下图修改，然后**执行命令**`pod install`，看看发生了什么。

![Image text](/img/EC8B9623-96B9-4FC4-B1C2-B16843899F6D.png)

![Image text](/img/9D58F6C4-C66B-4E65-BAEB-9D523AD1CDD1.png)

什么？pod search 都能找到我们的私有库，为什么 install 的时候 Unable to find ？这是因为默认情况下 Pod 都是从 CocoaPods 的官方仓库去查找索引的，我们的私有库是放在我们自己的 svn 索引仓库里，此时当然无法在官方仓库找到。继续对 Podfile 做如下修改，添加红色标记的内容，然后再执行 `pod install`命令：

![Image text](/img/D84624BB-999A-403C-813D-41CF700B30D9.png)

不出意外，会得到大概类似如下图中的输出，打开项目，就可以正常引入我们的 `LXBLog` 以及 `Alamofire` 库，并正常使用。

![Image text](/img/56D73014-CEA4-450C-8CB1-31B8F7D6720E.png)


## <a name=title4>四、后续</a>

## <a name=title5>五、常见问题</a>


[link-cocoapods-repo-svn-github]: https://github.com/dustywusty/cocoapods-repo-svn
[link-Podspec-Syntax-Reference]: https://guides.cocoapods.org/syntax/podspec.html
[link-Command-line-Reference]: https://guides.cocoapods.org/terminal/commands.html

***参考文档***

* [1] <https://guides.cocoapods.org/syntax/podspec.html> Podspec Syntax Reference
* [2] <https://guides.cocoapods.org/syntax/podfile.html> Podfile Syntax Reference
* [3] <https://guides.cocoapods.org/terminal/commands.html> Command-line Reference
* [4] <https://guides.cocoapods.org/making/private-cocoapods.html> Private Pods
* [5] <https://github.com/CocoaPods/CocoaPods/> CocoaPods’s GitHub
* [6] <https://github.com/dustywusty/cocoapods-repo-svn> cocoapods-repo-svn's GitHub



### THE END