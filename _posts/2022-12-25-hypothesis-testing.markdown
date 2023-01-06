---
layout: post
title:  "Hypothesis Testing"
date:   2022-11-22 20:16:40 +0530
categories: jekyll update
---

## Reference Links
1. Youtube video link on [message queues][hussain-link-2]
2. Youtube video link on [pub-sub model][hussain-link-1]
3. Google Cloud Platform docs on [pub sub model][gcs-pub-sub]

## Our dear friend REST
> Communication is the key

Lets start with understanding how REST works. 

I am not gonna give you an abbrevation of REST cause honestly I dont care (know) but almost all of the applications we use today use REST. It is basically a request/response architecture. In this model we have a client and a server that share information with each other. Whenever a client makes a request to the server, it opens a TCP connection to the server and then you make whatever requests you want to the server. The server now processes these request while the client waits for the response. Now this architecture is fine if we have a straight up single service and when things are easy. 

Now imagine if we have multiple services in the backend and the client now makes a request to the server. The request first goes to the service 1, processes, goes to service 2, processes, service 3 ......... (*you know how this ends*). This is gonna be a UX problem where the client or in this case the end user will not be acknowledged about whats happening. We essentially not blocking the processes on the users device (client) because the assumption is that the we are making asynchronous calls but still the experience is not user-friendly. 

So how in the hell can we manage a multi-service architecture which is resource and time hungry?

## Enter Message Queues
> Wait for your turn mister!

As the name depicts, we are gonna have a queue and all the processes that needs to be executed will be stored in a centralized queue that are waiting to be executed by consumers or services. Pub-Sub is a great example of how message queues are implemented, based on my understanding on this subject I am gonna use pubsub related terms for simplicity. 

So lets consider the same scenario we used in the REST example where the client makes a request to the server. The requests hits the server 1, records its request to a *topic* in the message queue, sends back an acknowledgement to the client. Now the client recieves a notification that the request is submitted and he/she is happy. Now the services that *subscribe* to the topics service 1 just posted will take its request from the queue and processes it and puts it back in another *topic* that is gonna be subscribed by another service. We are doing something called *Asynchronous Processing* where we a not waiting until the response is collected but sending the request and coming back later to collect the response. 

Some things to remember, 
1. Services can only take the requests from the queue if they are subscribed to that topic.
2. Message queues are useful if we have many services in the backend that talk with each other.

There are 2 parts that are not covered in this post, 
1. How does the subscriber get the topic or the request from the queue? (Long polling, Push/Pull Model)
2. How are we putting stuff in the queue? 

## Conclusion
> Yes, there is a part 2

We barely touched the surface of what are message queues and slightly got an idea on how pubsub works. We will see how this stuff works in more detail in the next part where we will also try to code something fun using message queues. 

Feel free to contact me if you think the contents incorrect at any place.




[hussain-link-1]: [https://www.youtube.com/watch?v=O1PgqUqZKTA]
[hussain-link-2]: [https://www.youtube.com/watch?v=DXTHb9TqJOs]
[gcs-pub-sub]: [https://cloud.google.com/pubsub/docs/overview]