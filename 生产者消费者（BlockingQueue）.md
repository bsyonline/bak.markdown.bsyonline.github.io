生产者消费者（BlockingQueue）
=

---
title: 单机搭建elk+logback
toc: true
date: 2016-07-16 15:53:17
tags:
categories:
---

	public class ProducerAndConsumerInBlockingQueue {

		public static void main(String[] args) {
			BlockingQueue<Integer> queue = new ArrayBlockingQueue<Integer>(10);

		 	new Thread(new ProducerAndConsumerInBlockingQueue().new Producer(queue)).start();
		 	new Thread(new ProducerAndConsumerInBlockingQueue().new Consumer(queue)).start();

		}

		class Producer implements Runnable {

			BlockingQueue<Integer> queue;

		 	public Producer(BlockingQueue<Integer> queue) {
				this.queue = queue;
		 	}

			public void run() {
				while (true) {
					int i = new Random().nextInt();
				 	try {
						queue.put(i);
				 		System.out.println("加入元素：" + i + "queue size=" + queue.size());
				 		Thread.sleep(1000);
				 	} catch (InterruptedException e) {
						e.printStackTrace();
				 	}
				}
			}
		}

		class Consumer implements Runnable {

			BlockingQueue<Integer> queue;

		 	public Consumer(BlockingQueue<Integer> queue) {
				this.queue = queue;
		 	}

			public void run() {
				while (true) {
					try {
						Integer i = queue.take();
						System.out.println("取出元素：" + i + "queue size=" + queue.size());
						Thread.sleep(1000);
		 			} catch (InterruptedException e) {
						e.printStackTrace();
		 			}
				}
			}
		}
	}
