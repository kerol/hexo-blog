---
title: gradle构建Spring项目
date: 2016-12-12 00:18:17
tags: gradle
---

### About gradle
A powerful building tool, replace maven, ant, ivy...
PS: 开始玩的时候还是从命令行开始，熟悉后可以使用idea开发，对于IDE不是特别感冒~
[Official site](https://gradle.org)
### build.gradle
```
apply plugin: 'java'

task helloWorld << {
    println 'hello, world'
}

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
```
<!-- more -->

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
