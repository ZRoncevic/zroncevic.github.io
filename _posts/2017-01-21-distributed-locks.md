---
layout: post
title: Distributed locks
categories: [java, programming]
tags: [java, apache-ignite, hazelcast, zookeeper]
---

Recently at my work I came across the need for distributed locks, so I did some investigation and a small prototype to check how it can be done. Here you can my findings so perhaps I will save somebody some time if they are ever in the same situation as me. 

__What are distributed locks?__ - they provide software applications which are distributed across a cluster on multiple machines with a means to synchronize their accesses to shared resources. More on the subject can be found on [Wikipedia.](https://en.wikipedia.org/wiki/Distributed_lock_manager)

If you are looking for a good blog post about the subject I recommend [Martin Kleppmann's.](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html) In it you can find everything you need about the way it's done. So in this blog post I will focus on the framework part of how it's done.

You have a few a few frameworks you can choose from: 

*  [Apache Curator](http://curator.apache.org/) 
*  [Apache Helix](http://helix.apache.org/) 
*  [Apache Ignite](https://ignite.apache.org/) 
*  [Hazelcast](https://hazelcast.com/) 

They are ordered alphabetically, so you don't think I favor Curator. So here are my two cents about each of them.

### Apache Curator

Curator is based on Apache ZooKeeper and on their web page they have described it better then I can explain - Guava is to Java what Curator is to ZooKeeper. 

Having that in mind, you will need to have ZooKeeper running to have locks. If you don't already run ZooKeeper I wouldn't suggest choosing this as your first pick. But if you already use ZooKeeper chances are you already know about Curator.

### Apache Helix
Helix also uses ZooKeeper for distributed locks. Their tutorial about it can be found [here.](http://helix.apache.org/0.6.2-incubating-docs/recipes/lock_manager.html) There you can find a code example on Git you can run (which I did). 

Here is the output of the example:

{% highlight bash %} 
./lock-manager-demo
STARTING localhost_12000
STARTING localhost_12002
STARTING localhost_12001
STARTED localhost_12000
STARTED localhost_12002
STARTED localhost_12001
localhost_12001 acquired lock:lock-group_3
localhost_12000 acquired lock:lock-group_8
localhost_12001 acquired lock:lock-group_2
localhost_12001 acquired lock:lock-group_4
localhost_12002 acquired lock:lock-group_1
localhost_12002 acquired lock:lock-group_10
localhost_12000 acquired lock:lock-group_7
localhost_12001 acquired lock:lock-group_5
localhost_12002 acquired lock:lock-group_11
localhost_12000 acquired lock:lock-group_6
localhost_12002 acquired lock:lock-group_0
localhost_12000 acquired lock:lock-group_9
lockName    acquired By
======================================
lock-group_0    localhost_12002
lock-group_1    localhost_12002
lock-group_10    localhost_12002
lock-group_11    localhost_12002
lock-group_2    localhost_12001
lock-group_3    localhost_12001
lock-group_4    localhost_12001
lock-group_5    localhost_12001
lock-group_6    localhost_12000
lock-group_7    localhost_12000
lock-group_8    localhost_12000
lock-group_9    localhost_12000
Stopping localhost_12000
localhost_12000 Interrupted
localhost_12001 acquired lock:lock-group_9
localhost_12001 acquired lock:lock-group_8
localhost_12002 acquired lock:lock-group_6
localhost_12002 acquired lock:lock-group_7
lockName    acquired By
======================================
lock-group_0    localhost_12002
lock-group_1    localhost_12002
lock-group_10    localhost_12002
lock-group_11    localhost_12002
lock-group_2    localhost_12001
lock-group_3    localhost_12001
lock-group_4    localhost_12001
lock-group_5    localhost_12001
lock-group_6    localhost_12002
lock-group_7    localhost_12002
lock-group_8    localhost_12001
lock-group_9    localhost_12001
{% endhighlight %}

One great thing that Helix offers is redistribution. In this example 3 instances are started and 12 locks are distributed among them. Once they are up and running the first instance is shut down which causes Helix to do the redistribution of the locks acquired by the interrupted instance among the live instances. 

Also you can connect to your already running instance of ZooKeeper if you have it running, or you can spin off one from code - like it was done in the example provided on git.

### Apache Ignite

Like they say on their web site - Apache Ignite In-Memory Data Fabric is a high-performance, integrated and distributed in-memory platform for computing and transacting on large-scale data sets in real-time, orders of magnitude faster than possible with traditional disk-based or flash technologies. 

So distributed locks is only a fraction of what it can do. They have some documentation about it [here.](http://apacheignite.gridgain.org/docs/distributed-locks)

Code sample is straightforward
{% highlight java %}
IgniteCache<String, Integer> cache = ignite.cache("myCache");

// Create a lock for the given key.
Lock lock = cache.lock("keyLock");
try {
  	// Aquire the lock.
    lock.lock();
  
    cache.put("Hello", 11);
    cache.put("World", 22);
}
finally {
  	// Release the lock.
    lock.unlock();
}
{% endhighlight %}

One thing to keep in mind is that in Ignite, locks are supported only for TRANSACTIONAL atomicity mode, which can be configured via atomicityMode property of CacheConfiguration. So if you don't set atomicity mode to transactional you won't even be able to run your code. Once it comes to the lock part it will throw a exception and in the logs it will log just that - to change it to transactional. 


### Hazelcast

Next in line is Hazelast and their tutorial which can be found [here.](http://docs.hazelcast.org/docs/3.5/manual/html/lock.html)

Also pretty easy to use:

{% highlight java %}
import com.hazelcast.core.Hazelcast;
import java.util.concurrent.locks.Lock;

HazelcastInstance hazelcastInstance = Hazelcast.newHazelcastInstance();
Lock lock = hazelcastInstance.getLock( "myLock" );
lock.lock();
try {
  // do something here
} finally {
  lock.unlock();
}
{% endhighlight %}

As with Ignite Hazelcast also provides you with a Java concurrency lock. Also as Ignite it provides much more then distributed locks and describing functionality of Hazelcast or Ignite can be a separate blog post, so I won't go into it here.

### Summary

On to the summary. If you want a solution based on ZooKeeper I would suggest Apache Helix. But if you don't want to add ZooKeeper into your architecture I would recommend Ignite or Hazelcast. Which one depends on you. If you only plan to use to for distributed locks you can't go wrong with each of them, but I if you need distributed locking perhaps you will need some other features in the future. So check what each offers and what best suits your current and perhaps your future needs. 