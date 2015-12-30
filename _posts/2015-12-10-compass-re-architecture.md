---
layout: post
title: Compass Re-architecture
---

Early this year at [Compass](https://www.compass.co) we decided to significantly redesign our core product with a focus on making it self-service. This juncture was the perfect opportunity for us to also evaluate the technical stack we were leveraging. This evaluation lead to some specific changes as outlined below.

|                             | New Stack   | Old Stack  |
| --------------------------- | :----------- | :---------- |
| Database                    | [PostgreSQL](http://www.postgresql.org/)  | [MongoDB](https://www.mongodb.org/)    |
| Inter-Process Communication | [RabbitMQ](https://www.rabbitmq.com/)    | [RabbitMQ](https://www.rabbitmq.com/)   |
| Web Backend                 | [Sinatra](http://www.sinatrarb.com/)     | [Rails](http://rubyonrails.org/)      |
| Web Frontend                | [AngularJS](https://angularjs.org/)   | [AngularJS](https://angularjs.org/)  |


It is now 6 months from our re-architecture and I wanted to discuss the outcomes from our move.

## MongoDB to PostgreSQL
The problem we were finding with Mongo was that our data model was becoming a bit of a mess. As a reporting and benchmarking tool it is fundamental that [Compass](https://www.compass.co) maintains a well structured database. One of the fundamental features of MongoDB (schemaless storage) was proving to be a significant pain point.

Having our storage engine define our schema has certainly been a positive decision. We often run algorithms over very flat data structures - as a result we found a relational data model actually mapped effectively to our problem space. Although there are certainly domains where persisting documents can increase productivity, this simply wasn't the case for us.

[Compass](https://www.compass.co) is made up of both Software Engineers and Data Scientists. Much of a Data Scientist’s job is often spent cleaning and analyzing data. Moving to PostgreSQL has made it significantly easier for our data science team to explore critical data. The improved consistency coupled with their comfortability with SQL has lead to more efficient turn around in problem understanding and feature specification.

As PostgreSQL comes equipped with some NoSQL features our move really has felt like the best of both worlds. With PostgreSQL you can get document database-like capabilities using the JSON data type, and key/value storage with the HSTORE module. As a result we have been able to make use of these features where appropriate.

## Rails to Sinatra
The biggest problem with our existing Rails application was that it didn't have a clear separation of concerns. At times the web layer was implemented as simple restful endpoints, at other times it was rendering complex views. With our move to Sinatra we wanted to address this inconsistency and have a clear separation between the client (Angular SPA) and our web API layer. Furthermore in building a single page application a lot of what Rails would provide is overkill.

Since moving to Sinatra we have found it excellent for building out a web API layer. Its flexibility gave us the control over what libraries we leverage in building out our application we wanted. Furthermore its lightweight nature and simple method for specifying routes has given the team a restful focus, encouraging the separation between web and client we were striving for.

One interesting positive side effect that we didn't anticipate, is that leveraging a micro framework has helped our junior developers better understand web semantics. In having less ‘configuration magic’, ramp up time for our junior developers may be greater, however it has forced them to gain a deeper understanding of how web applications are built.

## Conclusion
Six months on from our architectural changes and the team remains busy building out new features for [Compass](https://www.compass.co). Software is forever changing and I am sure our stack will have significant evolutions in the years to come. But as it stands the team remains happy with the architectural decisions made and is excited about evolving the platform. Moving to Sinatra gave us the clear separation between web and client we were looking for, and PostgreSQL the tighter structure and subsequent boost in productivity we needed.
