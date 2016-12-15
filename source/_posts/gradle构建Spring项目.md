---
title: gradle构建Spring项目
date: 2016-12-12 00:18:17
tags: gradle
---

### About gradle
A powerful building tool, replace maven, ant, ivy...
PS: 开始玩的时候还是从命令行开始，熟悉后可以使用idea开发，对于IDE不是特别感冒~
[Official site](https://gradle.org)
[UserGuide](https://docs.gradle.org/current/userguide/userguide.html)
[Download](https://gradle.org/gradle-download/)
### Install gradle and config
```
brew install gradle
```
然后就可以使用gradle了，为了方便管理同一版本，建议使用gradle wrapper,
不会依赖系统的gradle版本，
主要文件：
gradlew: 执行脚本./gradlew
gradlew.bat: gradlew调用
gradle/wrapper/gradle-wrapper.jar
gradle/wrapper/gradle-wrapper.properties
build.gradle: 设置项目的插件、依赖、gradle wrapper版本等
gradle.properties: 一些依赖的版本号，类似python的requirements.txt
主要命令：
```
touch ~/.gradle/gradle.properties && echo "org.gradle.daemon=true" >> ~/.gradle/gradle.properties
./gradlew wrapper // 更改build.gradle里面gradle版本后可以升级gradle
./gradlew build // build
```
<!-- more -->
### build.gradle
```
apply plugin: 'java'

repositories {
    mavenCentral()
    // and optionally...
    maven { url "http://repo.spring.io/release" }
    // maven { url "http://repo.spring.io/milestone" }
    // maven { url "http://repo.spring.io/snapshot" }
}

dependencies {
    // spring-context depends on spring-core, spring-beans...
    compile("org.springframework:spring-context:4.3.4.RELEASE")
    testCompile("org.springframework:spring-test:4.3.4.RELEASE")
}
task wrapper(type: Wrapper) {
    gradleVersion = '3.2.1'
}
```

### gradle build
```
folder tree:
⇒  tree -L 5 src
src
└── main
    └── java
        └── me
            └── kerol
                ├── Hello.java
                └── World.java
⇒  gradle build
⇒  tree -L 6 build
build
├── classes
│   └── main
│       └── me
│           └── kerol
│               ├── Hello.class
│               └── World.class
├── dependency-cache
├── libs
    └── demo.jar
⇒  java -cp build/classes/main/ me.kerol.Hello
Hello World!
```
