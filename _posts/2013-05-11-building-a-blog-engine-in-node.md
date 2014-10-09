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

<script src="https://gist.github.com/davidkdickson/5532206.js"></script>

## Heroku

Having deployed to both [Appharbor](https://appharbor.com/), and [Azure](http://www.windowsazure.com/) I was excited about seeing how Heroku did its thing. Heroku was a pleasure to work with. It really is as simple as creating an account then git push. Heroku automatically detected that I was deploying a node application and my site was up. Pulling in Redis as an add on was equally effortless. Another thing that I really enjoyed about working on Heroku is its focus on interacting with the platform via the command line.