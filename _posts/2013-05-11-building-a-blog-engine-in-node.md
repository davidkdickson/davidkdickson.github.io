---
layout: post
title: Building a blog engine in Node
---

I recently got the urge to mess around with some new technologies. To give me some direction, I decided to build my new blog with a new stack. The technologies I explored were:

* [Node](http://nodejs.org/)
* [Redis](http://redis.io/)
* [Heroku](https://www.heroku.com/)

## Node

The Node ecosystem is very active, this made it easy to find tutorials and examples to get me started. The package manager npm made it simple to pull in libraries to accelerate my development. To build my blog I chose [Express](http://expressjs.com/) as my web application framework. For editing I used Sublime Text, and debugging [node-inspector.](https://github.com/dannycoates/node-inspector)

Whilst programming in Node, there were times when I certainly missed the security I feel with a static language. However, one thing that's for sure is that working in Node is doing wonders for my JavaScript skills.

## Redis

I really enjoyed working with Redis. Redis is an in-memory key-value store, however it is probably better described as a data structure server. What I most enjoyed about working with Redis is how it changed the way I thought about modelling my application’s data.

Rather than thinking of data in tables and relations, or objects and graphs, Redis forces you to think in hashes, lists and sets. I found this focus on basic data structures at the persistence level appealing as it influenced me to build a flat and simple model. One other appealing thing about Redis is it is quick. Below are the results of the redis-benchmark utility run on my macbook pro:

{% highlight Bash %}
====== PING_INLINE ======
  10000 requests completed in 0.12 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
100.00% <= 0 milliseconds
85470.09 requests per second
 
====== PING_BULK ======
  10000 requests completed in 0.14 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
99.99% <= 1 milliseconds
100.00% <= 1 milliseconds
74074.07 requests per second
 
====== SET ======
  10000 requests completed in 0.16 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
100.00% <= 0 milliseconds
64516.13 requests per second
 
====== GET ======
  10000 requests completed in 0.11 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
100.00% <= 0 milliseconds
88495.58 requests per second
 
====== INCR ======
  10000 requests completed in 0.16 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
99.51% <= 1 milliseconds
99.87% <= 2 milliseconds
99.93% <= 3 milliseconds
99.96% <= 4 milliseconds
100.00% <= 4 milliseconds
64102.56 requests per second
 
====== LPUSH ======
  10000 requests completed in 0.15 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
99.38% <= 1 milliseconds
99.84% <= 2 milliseconds
100.00% <= 2 milliseconds
66666.66 requests per second
 
====== LPOP ======
  10000 requests completed in 0.13 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
99.88% <= 1 milliseconds
99.96% <= 2 milliseconds
100.00% <= 2 milliseconds
75757.58 requests per second
 
====== SADD ======
  10000 requests completed in 0.15 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
100.00% <= 0 milliseconds
68027.21 requests per second
 
====== SPOP ======
  10000 requests completed in 0.14 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
99.51% <= 1 milliseconds
100.00% <= 1 milliseconds
68965.52 requests per second
 
====== LPUSH (needed to benchmark LRANGE) ======
  10000 requests completed in 0.14 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
99.77% <= 1 milliseconds
99.86% <= 2 milliseconds
99.91% <= 3 milliseconds
99.97% <= 4 milliseconds
100.00% <= 4 milliseconds
71428.57 requests per second
 
====== LRANGE_100 (first 100 elements) ======
  10000 requests completed in 0.47 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
37.77% <= 1 milliseconds
99.66% <= 2 milliseconds
99.97% <= 3 milliseconds
100.00% <= 3 milliseconds
21276.60 requests per second
 
====== LRANGE_300 (first 300 elements) ======
  10000 requests completed in 1.11 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
0.10% <= 1 milliseconds
10.91% <= 2 milliseconds
70.31% <= 3 milliseconds
92.80% <= 4 milliseconds
99.53% <= 5 milliseconds
99.80% <= 6 milliseconds
99.91% <= 7 milliseconds
100.00% <= 7 milliseconds
8976.66 requests per second
 
====== LRANGE_500 (first 450 elements) ======
  10000 requests completed in 1.49 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
0.05% <= 1 milliseconds
0.96% <= 2 milliseconds
23.64% <= 3 milliseconds
66.67% <= 4 milliseconds
91.83% <= 5 milliseconds
97.22% <= 6 milliseconds
99.28% <= 7 milliseconds
100.00% <= 7 milliseconds
6706.91 requests per second
 
====== LRANGE_600 (first 600 elements) ======
  10000 requests completed in 1.93 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
0.02% <= 1 milliseconds
0.04% <= 2 milliseconds
2.60% <= 3 milliseconds
22.09% <= 4 milliseconds
63.34% <= 5 milliseconds
88.36% <= 6 milliseconds
96.23% <= 7 milliseconds
99.21% <= 8 milliseconds
99.64% <= 9 milliseconds
99.83% <= 10 milliseconds
99.90% <= 11 milliseconds
99.98% <= 12 milliseconds
100.00% <= 12 milliseconds
5175.98 requests per second
 
====== MSET (10 keys) ======
  10000 requests completed in 0.22 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
 
53.98% <= 1 milliseconds
99.95% <= 2 milliseconds
99.97% <= 3 milliseconds
100.00% <= 4 milliseconds
46082.95 requests per second
{% endhighlight %}

## Heroku

Having deployed to both [Appharbor](https://appharbor.com/), and [Azure](http://www.windowsazure.com/) I was excited about seeing how Heroku did its thing. Heroku was a pleasure to work with. It really is as simple as creating an account then git push. Heroku automatically detected that I was deploying a node application and my site was up. Pulling in Redis as an add on was equally effortless. Another thing that I really enjoyed about working on Heroku is its focus on interacting with the platform via the command line.