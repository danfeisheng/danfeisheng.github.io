---
layout: post
title: "Volatile"
date: "2019-07-14 09:31"
author: "Danfei"
---
2019-07-14 09:31

Useful links:

装饰器模式：https://www.jianshu.com/p/d80b6b4b76fc
	
{% highlight ruby %}
public class InputStreamTest {
    public static void main(String[] args) {
        DataInputStream dis = null;
        try {
            dis = new DataInputStream(
                    new BufferedInputStream(
                            new FileInputStream("23.txt")
                            )
                    );
            byte[] bytes = new byte[dis.available()];
            dis.read(bytes);
            String content = new String(bytes);
            System.out.println(content);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                dis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}


{% endhighlight %}

观察上面的代码，会发现最里层是一个FileInputStream对象，然后把它传递给一个BufferedInputStream对象，
经过BufferedInputStream对象处理后，再将处理后的对象传递给DataInputStream对象进行处理。这个过程，
其实就是装饰器的组装过程，FileInputStream对象相当于原始的被装饰的对象，而BufferedInputStream对象和DataInputStream对象则相当于装饰器。
