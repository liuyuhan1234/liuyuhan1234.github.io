---
layout: post
title:  "IDEA插件Lombok"
date:   2020-03-31 08:20:37 +0530
categories: IDEA 插件

---

[https://www.jianshu.com/p/68babcf5a4ad](https://www.jianshu.com/p/68babcf5a4ad)

## 简介 & 安装

开发项目过程中，会有很多的 pojo. pojo 又叫做 javabean,bean,entity 等等，都是他。
pojo会有很多的 setter 和 getter , toString, hashcode, equals 等等
每个 pojo 都要写，增加了属性要写，减少了属性要写，还是。。。很麻烦的。


为了偷懒，我们就可以用 lombok. 用了之后就会如下代码所示，加上注解就行了
```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Data
public class Hero {
	private int id;
	private String name;
}
```

为了证明没有写 setter ,getter 也能访问，做了个截图，可以看到 IDE 会自动弹出已经有的方法，诺， setter, getter, toString 什么的，全部都有了
![9bbb05ac59749184bd1362a1f9af9512.png](en-resource://database/28992:1)@w=500



为了使用，需要安装插件
如图所示，依次点击： Plugins -> Browse repositories -> Lombok -> Install
![33d33b7bb8e8530dd67d56bc23d843ec.png](en-resource://database/28994:1)@w=500


## 用法
### jar

为了使用 lombok 的功能，需要用到 jar, 在右上角下载。 如果是maven 就是下面这段。
```		
<!-- lombok -->
<dependency>
		  <groupId>org.projectlombok</groupId>
		  <artifactId>lombok</artifactId>
		  <scope>compile</scope>
</dependency>
```

### @Data 注解

@Data 注解会为类的所有属性自动生成setter/getter、equals、canEqual、hashCode、toString方法，如为final属性，则不会为该属性生成setter方法。
```java
import lombok.Data;

@Data

public class Hero {
	private int id;
	private String name;
	
	public static void main(String[] args) {
		
		System.out.println(new Hero().getName());
		System.out.println(new Hero().toString());
	}
}
```

## 构造方法

@AllArgsConstructor
@NoArgsConstructor
分别提供全参构造方法和无参构造方法
```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor 

public class Hero {
	private int id;
	private String name;
	
	public static void main(String[] args) {
		
		System.out.println(new Hero(1,"gareen"));
		System.out.println(new Hero());
	}
}
```

## @Builder
实例化和设置属性值的风格变了

```java
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor 

@Builder
public class Hero {
	private int id;
	private String name;
	
	public static void main(String[] args) {
		//传统方式
		Hero h1 = new Hero();
		h1.setId(1);
		h1.setName("garren");
		System.out.println(h1);
		
		//builder 方式
		Hero h2 =Hero.builder().id(1).name("gareen").build();
		System.out.println(h2);
	}
}
```









