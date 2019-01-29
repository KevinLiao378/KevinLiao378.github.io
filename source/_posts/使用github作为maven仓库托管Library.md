---
title: 使用github作为maven仓库托管Library
date: 2019-01-28 11:39:31
tags:
 - maven
 - gradle
categories: 小抄
---

## 配置打包脚本

### 配置脚本

在Library Module下`build.gradle`最后添加如下脚本：

```groovy
apply plugin: 'maven'
ext {
    // Library本地maven仓库地址
    GITHUB_REPO_PATH = "D:\\develop\\AndroidProjects\\demoLibrary"
    PUBLISH_GROUP_ID = 'com.example'
    PUBLISH_ARTIFACT_ID = 'demoLibrary'
    PUBLISH_VERSION = '1.0.0'
}

uploadArchives {
    repositories.mavenDeployer {
        def deployPath = file(project.GITHUB_REPO_PATH)
        repository(url: "file://${deployPath.absolutePath}")
        pom.project {
            groupId project.PUBLISH_GROUP_ID
            artifactId project.PUBLISH_ARTIFACT_ID
            version project.PUBLISH_VERSION
        }
    }
}
```
<!-- more -->

其中`GITHUB_REPO_PATH`为本地maven仓库地址，`PUBLISH_GROUP_ID`、`PUBLISH_ARTIFACT_ID`和`PUBLISH_VERSION`分别对应了maven仓库pom文件的`groupId`、`artifactId`和`version`，其最终效果为：

```groovy
implementation 'com.example:demoLibrary:1.0.0'
```

### 执行脚本

在Android Studio右侧`gradle`找到`:soundlinksSDK > Tasks > upload > uploadArchives`，双击执行后，将会在此前设置的`GITHUB_REPO_PATH`目录生成`aar`及maven配置文件。

## 上传本地maven仓库到github

### 创建github仓库，得到仓库地址

```html
https://github.com/KevinLiao378/ParamitaApp.git
```

### 上传本地maven仓库到github

在`GITHUB_REPO_PATH`目录执行：

```shell
// 初始化git
git init
// 添加文件
git add .
git commit -m 'initial commit'
// 添加github关联
git remote add origin https://github.com/KevinLiao378/demoLibrary.git
// 提交
git push origin master
```

使用github的raw服务作为下载服务，仓库对应的地址为：

```html
https://raw.githubusercontent.com/KevinLiao378/demoLibrary/master
```

## 使用

在项目下的`builde.gradle`添加上一步得到的仓库地址：

```groovy
repositories {
    google()
    jcenter()
    maven {
        url 'https://raw.githubusercontent.com/KevinLiao378/demoLibrary/master'
    }
}
```

在app下的`build.gradle`添加依赖：

```groovy
implementation 'com.example:demoLibrary:1.0.0'
```

至此，同步后即可在代码中使用了。