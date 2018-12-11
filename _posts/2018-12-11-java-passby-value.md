---
layout: post
title: "Java pass by reference or by value?"
date: "2018-12-11 22:07"
author: "Danfei"
---
2018-12-11 22:07

Java pass by value! check below example

{% highlight ruby %}
public class PassTest {

	public static void main(String args[]){
		TestStudent t = new TestStudent();
		t.name = "cc";
		t.age = 10;
		System.out.println(t.name + ", " + t.age);
		changeStudent(t);
		System.out.println(t.name + ", " + t.age);
		
		TestStudent t1 = new TestStudent();
		t1.name = "ac";
		t1.age = 1;
		TestStudent t2 = new TestStudent();
		t2.name = "ab";
		t2.age = 2;
		System.out.println("t1: " + t1.name + ", " + t1.age);
		System.out.println("t2: " + t2.name + ", " + t2.age);
		change(t1, t2);
		System.out.println("t1: " + t1.name + ", " + t1.age);
		System.out.println("t2: " + t2.name + ", " + t2.age);
	}
	
	public static void change(TestStudent t1, TestStudent t2){
		TestStudent temp = t1;
		t1 = t2;
		t2 = temp;
	}
	
	public static void changeStudent(TestStudent t){
		
		TestStudent t2 = new TestStudent();
		t2.name = "dd";
		t2.age = 12;
		System.out.println(t2.name + ", " + t2.age);
		t = t2;
	}
}

#=> prints 'Hello world' to console.
{% endhighlight %}


java 中的值传递还是引用传递的问题，首先java中是值传递，对调用的方法里改了引用是指影响该方法的，对原方法没有影响，传递的是copy的副本，浅copy，copy出来的对象引用的内容仍旧和原对像一样，像这里的name和age，在方法里改了也影响原对象。