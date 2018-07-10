# CocodPods 创建 SVN 私有库 
## 索引
* [背景](#title1)
* [准备](#title2)
* [开始](#title3)

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
cocoapods-repo-svn 是 CocoaPods 的一个插件，在创建 SVN 的私有库时，此插件可以辅助项目安装过程中更加方便。具体的安装使用可以参考[github for repo-svn][link-cocoapods-repo-svn-github]

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

在项目目录下创建 `Classes` 目录用来存储 framework 需要编译的所有文件，创建 LXBLog.bundle 用来存放所有的资源文件。编写好代码之后，往常情况是我们编译生成对应的 framework 。现在我们用 Pod 来管理依赖，所以我们需要创建 podspec文件来告诉 Pod，我们的库的运行环境、版本、依赖、资源文件、编译文件等。

***到目前为止，我们已经有两个 SVN 仓库了，一个用来存放私有 podspec 文件，一个用来存放我们私有库的代码***

### <a name="create-private-repo">3.4 添加 CocoaPods 私有 Repo</a>

Pod 是从本地的索引库先去找到需要的库，然后再去执行下载库与依赖安装的。因此我们要将第二步创建的 podspec 文件的 svn 仓库添加为 Pod 本地索引库，以便在执行 pod install 或者 update 的时候能找到对应的库。
首先在终端执行`pod repo`命令，默认情况下你都会一个 `master`仓库的输出，

![Image text](/img/A022DE09-701D-43DC-86A9-217247D30B80.png)

这个就是 CocoaPods 的默认索引仓库。如果需要将一个 git仓库设为索引库，只需要执行:

```
pod repo add NAME URL [BRANCH]
```

命令即可，其中 NAME 是本地的仓库名称，url 是 git 地址。


但是，如果是一个 svn 仓库地址，如`svn://192.168.0.88/ios/SpecRepo`的地址，你会发现执行时会报错，此时就需要用我们的插件了。假设我们将本地仓库命名为 `TJSpecRepo `,那么命令应该这么写：

```
pod repo-svn add TJSpecRepo svn://192.168.0.88/ios/SpecRepo
```
再执行 `pod repo`，输出如下，除了 master 多了一个 TJSpecRepo。

![Image text](/img/F59FC0E4-E083-4B5B-BF30-BB1FC5125630.png)

### <a name="create-podspec-file">3.5 编写podsepc文件</a>

### <a name="create-podfile">3.6 Podfile</a>


[link-cocoapods-repo-svn-github]: https://github.com/dustywusty/cocoapods-repo-svn

***参考文档***

* [1] <https://guides.cocoapods.org/syntax/podspec.html> Podspec Syntax Reference
* [2] <https://guides.cocoapods.org/syntax/podspec.html> Podspec Syntax Reference
* [3] <https://guides.cocoapods.org/terminal/commands.html> Command-line Reference
* [4] <https://guides.cocoapods.org/making/private-cocoapods.html> Private Pods
* [5] <https://github.com/CocoaPods/CocoaPods/> CocoaPods’s GitHub
* [6] <https://github.com/dustywusty/cocoapods-repo-svn> cocoapods-repo-svn's GitHub



### THE END