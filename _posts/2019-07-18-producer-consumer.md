---
layout: post
title: "生产者消费者模式"
date: "2019-07-18 22:23"
author: "Danfei"
---
2019-07-18 22:23

生产者消费者模式

{% highlight ruby %}

public class ProducerConsumer{
	public static void main(String[] args){
		final BlockingQueue<Character> bq;
		bq = new ArrayBlockingQueue<Character>(26);
		final ExecutorService executor = Executors.newFixedThreadPool(2);
		Runnable producer = () -> {
			for(char ch = 'A'; ch <= 'Z'; ch++){
				try{
					bq.put(ch);
					System.out.printf("%c producted by " + "producer.%n", ch);
				}catch(InterruptedException e){
					
				}
			}
		};
		executor.execute(producer);
		Runnable consumer = () -> {
			char ch = '\0';
			do{
				try{
					ch = bq.take();
					System.out.printf("%c consumed by" + "consumer.%n", ch);
				}catch(InterruptedException ie){
				
				}
			}
			while(ch != 'Z');
			executor.shutdownNow();
		};
		executor.execute(consumer);
	} 
}

{% endhighlight %}
