---
title: 'Spring in Action Ch2: 装配bean'
date: 2016-07-31 23:02:49
tags: spring-ia
---
在Spring中，对象无需自己查找或创建与其关联的其他对象。IOC容器负责把需要相互协作的对象引用赋予各个对象。创建应用对象之间协作关系的行为通常称为装配，这也是依赖注入的本质。
### 2.1 Spring配置的可选方案
Spring容器负责应用程序中的bean并通过DI来协调这些对象之间的关系。Spring提供了三种主要的装配机制：
- 在XML中进行显式配置；
- 在Java中进行显式配置；
- 隐式的bean发现和自动装配；  

尽可能的使用自动配置的机制，显示配置越少越好。推荐使用类型安全并且比XML更强大的JavaConfig，只有想用便利的XML命名空间，并且JavaConfig没有同样的实现时，才应该使用XML。
### 2.2 自动化装配bean
Spring从两个角度来实现自动化装配：
- 组件扫描：Spring会自动发现上下文中创建的bean；
- 自动装配：自动满足bean之间的依赖关系；
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Compoment;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.CompomentScan;
```
<!-- more -->
#### 2.2.1 创建可被发现的bean
使用了@Compoment注解的类表明该类会作为组件类，并告知Spring要为该类创建bean。
```
package soundsystem;
public interface CompactDisc {
    void play();
}
@Compoment
public class SgtPeppers implements CompactDisc {
    void play() {
        // do something
    }
}
```
通过下面的配置启用组件扫描：
```
@Configuration
@CompomentScan
public class CDPlayerConfig {}
```
因为该类位于soundsystem包中，因此Spring将会扫描这个包以及这个包下的所有子包，查找带有@Component注解的类，并未这样的类自动创建一个bean。
#### 2.2.2 为组件扫描的bean命名
默认将类的第一个字母小写，可以自定义bean的名称：
```
@Component("beanName")
public class SgtPeppers implements CompactDisc {}
```
#### 2.2.3 设置组件扫描的基础包
我们没有给@ComponentScan设置任何属性，默认它会以配置类所在的包作为基础包来扫描组件。如果要扫描多个基础包，就要指明扫描哪些包。
使用basePackages:
```
@Configuration
@ComponentScan(basePackages="soundsystem")
public class CDPlayerConfig {}
// or
@Configuration
@ComponentScan(basePackages={"soundsystem", "video"})
public class CDPlayerConfig {}
```
上面的扫描的基础包是string类型的，如果代码重构或者包的名称改变就可能发生错误。
另外一种方法是将基础包指定为包中所包含的类或接口：
```
@Configuration
@ComponentScan(basePackageClasses={CDPlayer.class, DVDPlayer.class})
public class CDPlayerConfig {}
```
这些类所在的包会作为组件扫描的基础包。
#### 2.2.4 通过为bean添加注解实现自动装配
自动装配就是让Spring自动满足依赖的一种方法，会在Spring上下文中寻找匹配某个bean需求的其他bean。
使用Spring的@Autowired注解或者Java依赖注入规范的@Inject注解(暂时推荐使用前者)：
```
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc cd;
    // 构造器
    @Autowired
    public CDPlayer(CompactDisc cd) {
        this.cd = cd;
    }
    // setter方法或者其他需要bean的方法
    @Autowired
    public setCompactDisc(CompactDisc cd) {
        this.cd = cd;
    }
    public void play() {
        cd.play();
    }
}
```
如果没有匹配的bean，在应用上下文创建的时候回抛出一个异常；
如果有多个bean都能满足依赖关系的话，Spring也会抛出异常，我们会进一步讨论自动装配中的歧义性。
### 2.3 通过Java代码装配bean
很多情况下都可以通过组件扫描和自动装配实现Spring的自动化配置。如果你要讲第三方的组件装配到你的应用中，没法在它的类上添加@Component和@Autowired等注解，因此就不能用自动化装配的方案了。
使用显式装配的方案：Java或者XML。显式装配时JavaConfig是更好的方案，因为它更强大、类型安全并且对重构友好。
#### 2.3.1 创建配置类
关键在于为一个配置类添加@Configuration注解，之前通过组件扫描来创建bean，尽管可以同时使用组件扫描和显示配置，为了更加关注显示配置，去掉@ComponentScan的注解。
#### 2.3.2 声明简单的bean
在JavaConfig的配置类中编写一个方法，为该方法添加@Bean注解：
```
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class CDPlayerConfig {
    @Bean
    // bean的ID默认为方法名，可以指定bean的ID(name)
    // @Bean(name="compactDisc")
    public CompactDisc sgtPeppers() {
        return new SgtPeppers();
    }
    @Bean
    public CDPlayer cdPlayer() {
        return new CDPlayer(sgtPeppers());
    }
}
```
#### 2.3.3 借助JavaConfig实现注入
引用创建bean的方法(注：区别bean和class，bean直接返回一个实例，而class是普通类)：
```
@Bean
public CDPlayer cdPlayer() {
    return new CDPlayer(sgtPeppers());
}
```
看起来调用了sgtPeppers方法，实际上Spring拦截了所有对@Bean注解方法的调用，直接返回该方法所创建的bean，而不是每次对其实际的调用，得到的是相同的实例。
这种通过调用方法来引用bean的方式有点令人困惑，另外一种方式：
```
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    return new CDPlayer(compactDisc);
}
```
上面的方式引用其他的bean通常是最佳的选择，不会要求将CompactDisc类声明到同一个配置类中。不管CompactDisc是采用什么方式创建出来的，Spring都会将其传入配置方法中，并用来创建CDPlayer的bean。
这里使用了CDPlayer的构造器实现了DI功能，也可以通过setter方法注入CompactDisc：
```
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    CDPlayer cdPlayer = new CDPlayer();
    cdPlayer.setCompactDisc(compactDisc);
    return cdPlayer;
}
```
带有@Bean注解的方法可以采用任何必要的Java功能来产生bean实例。构造器和setter方法只是@Bean方法的两个简单样例。
(注：就是可以通过其他方法产生bean实例，只不过Java目前适合这两种方法)
### 2.4 通过XML装配bean
我们已经了解了如何让Spring自动发现和装配bean，还看到了如何进行手动干预，即通过JavaConfig显式地装配bean。由于历史原因，很多Spring配置基于XML。理解如何在Spring中使用XML配置，维护已有的XML的配置，在完成新的Spring工作时，尽量使用自动化配置和JavaConfig。
#### 2.4.1 创建XML规范
使用JavaConfig的时候，需要创建一个@Configuration注解的配置类，而在XML中要创建一个XML文件，并且要以<beans>为根。
```
<?xml version="1.0" encoding="UTF-8"?>   
<beans xmlns:xsi="http://www.springframework.org/schema/beans"  
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">   
    http://www.springframework.org/schema/context">   
    <!-- configuration deatils go here-->
</beans> 
```
#### 2.4.2 声明一个简单的`<bean>`
`<bean>`元素类似JavaConfig中的@Bean注解：
```
<bean class="soundsystem.SgtPeppers" />
```
命名bean：
```
<bean id="compactDisc" class="soundsystem.SgtPeppers" />
```
#### 2.4.3 借助构造器注入初始化bean
两种基本配置方案：
- `<constructor-arg>`元素
- 使用c-命名空间
构造器注入bean引用：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer">
  <constructor-arg ref="compactDisc" />
</bean>
```
Spring会创建一个CDPlayer实例，要将一个id为compactDisc的bean引用传递到CDPlayer的构造器中。
使用c-命名空间：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer"
  c:cd-ref="compactDisc" />
```
c-命名空间来声明构造器参数作为`<bean>`元素的一个属性。属性名以"c-"开头，接下来是要装配的构造器参数名"cd"(CompactDisc cd)，在此之后是"-ref"，这是一个命名的约定，它会告诉Spring正在装配的是一个bean的引用，而不是字面量"compactDisc"。
直接引用了构造器参数名有点怪异，也可以使用参数在整个参数列表中的位置信息：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer"
  c:_0-ref="compactDisc" />
```
字面量注入到构造器中：
考虑CompactDisc的新实现BlankDisc的构造器：
```
public BlankDisc(String title, String artist) {
    this.title = title;
    this.artist = artist;
}
```
构造器参数注入：
```
<!-- constructor-arg -->
<bean id="compactDisc" class="soundsystem.BlankDisc">
  <constructor-arg value="title name" />
  <constructor-arg value="artist name" />
</bean>
<!-- c-命名空间 -->
<bean id="compactDisc" class="soundsystem.BlankDisc"
  c:_0="title name"
  c:_1="artist name" />
```
装配集合：
构造器参数有List或Set类型，只能使用`<constructor-arg>`。
考虑构造器传入参数类型`(String, List<String>, List<BlankDisc>, Set<String>)`：
```
<bean id="compactDisc" class="soundsystem.BlankDisc">
  <constructor-arg value="title name" />
  <constructor-arg>
    <list>
      <value>first value</value>
      <value>second value</value>
      <value>third value</value>
    </list>
  </constructor-arg>
  <constructor-arg>
    <list>
      <ref> bean="first bean" />
      <ref> bean="second bean" />
    </list>
  </constructor-arg>
  <constructor-arg>
    <set>
      <value>first value</value>
      <value>second value</value>
    </set>
  </constructor-arg>
</bean>
```
### 2.4 设置属性
之前的类完全是通过构造器注入的，没有使用属性的setter方法。
```
package soundsystem.properties;
import org.springframework.beans.factory.annotation.Autowired;
import soundsystem.CompactDisc;
import soundsystem.MediaPlayer;
public class CDPlayer implements MediaPlayer {
  private CompactDisc compactDisc;
  @Autowired
  public void setCompactDisc(CompactDisc compactDisc) {
    this.compactDisc = compactDisc;
  }
  public void play() {
    compactDisc.play();
  }
}
```
CDPlayer没有任何构造器（除了隐含默认的），声明为bean，
注入CDPlayer的compactDisc属性：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer">
  <property name="compactDisc" ref="compactDisc" />
</bean>
```
本例中，通过ref引用了ID为compactDisc的bean，通过setter方法注入到compactDisc属性中。
类似的p-命名空间：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer">
  p:compactDisc-ref="compactDisc" />
```
将字面量注入到属性中
新的BlankDisc如下所示：
```
package soundsystem.properties;
import java.util.List;
import soundsystem.CompactDisc;
public class BlankDisc implements CompactDisc {
  private String title;
  private String artist;
  private List<String> tracks;
  public void setTitle(String title) {
    this.title = title;
  }
  public void setArtist(String artist) {
    this.artist = artist;
  }
  public void setTracks(List<String> tracks) {
    this.tracks = tracks;
  }
  public void play() {
    System.out.println("Playing " + title + " by " + artist);
    for (String track : tracks) {
      System.out.println("-Track: " + track);
    }
  }
}
```
使用property注入属性：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer">
  <property name="title" value="title name"/>
  <property name="artist" value="artist name"/>
  <property name="tracks">
    <list>
      <value>first str</value>
      <value>second str</value>
    </list>
  </property>
</bean>
```
使用p-命名空间注入属性：
```
<bean id="cdPlayer"
      class="soundsystem.CDPlayer"
      p:title="title name"
      p:artist="artist name">
  <property name="tracks">
    <list>
      <value>first str</value>
      <value>second str</value>
    </list>
  </property>
</bean>
```
### 2.5 导入和混合配置
需要启用组件扫描和自动装配。
自动装配时，Spring并不在意要装配的bean来自哪里。自动装配的时候会考虑到Spring容器中所有的bean。
#### 2.5.1 在JavaConfig中引用XML配置
```
package soundsystem;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.ImportResource;
@Configuration
@Import(CDPlayerConfig.class)
@ImportResource("classpath:cd-config.xml")
public class SoundSystemConfig {}
```
#### 2.5.2 在XML配置中引用JavaConfig
```
<!-- 导入其他XML文件 -->
<import resource="cdplayer-config.xml" />
<!-- 导入JavaConfig配置类 -->
<bean class="soundsystem.CDConfig" />
```
不管是JavaConfig还是使用XML进行装配，我们通常会创建一个根配置，这个配置会将两个或更多的JavaConfig类或XML配置文件组合起来，通常也会在根配置中启用组件扫描。
### 2.6 小结
Spring框架的核心是Spring容器。容器负责管理应用中组件的生命周期，它会创建这些组件并保证他们的依赖能够满足。
装配bean的三种主要方式：自动化配置、JavaConfig和XML显式配置，都描述了组件以及组件之间的关系。
