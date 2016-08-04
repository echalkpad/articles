# OpenWhisk

[Original URL](https://developer.ibm.com/open/openwhisk/)

> Microservice architectures have emerged as the preferred way to engineer robust, scalable cloud-native solutions. Microservices hold application logic in small, loosely coupled distributed services,...

Microservice architectures have emerged as the preferred way to engineer robust, scalable cloud-native solutions. Microservices hold application logic in small, loosely coupled distributed services, communicating through language-agnostic APIs.

![OpenWhisk architecture](http://developer.ibm.com/open/wp-content/uploads/sites/50/2016/02/openwhisk-arch2.png)

Despite the benefits, microservice-based solutions are hard to build using mainstream cloud technologies, which often require you to control a complex toolchain and build/operations pipeline.

As a result, developers spend too much time dealing with infrastructure and operational complexities like fault-tolerance, load balancing, auto-scaling, and logging features. And that's where OpenWhisk shines.

The OpenWhisk model consists of three concepts:

- **trigger**: a class of events that can happen
- **action**: an event handler -- some code that runs in response to an event
- **rule**: an association between a trigger and an action

Services define the events they emit as triggers, and developers define the actions to handle the events. The developer only needs to be concerned with implementing the desired app logic -- the system handles the rest.

Out of the box, OpenWhisk supports Cloudant and Github as event sources, as well as the execution of scheduled actions. Packages to invoke the Watson Translation and Weather services are being provided, as well as an open API, giving any party the ability to contribute a package of actions and triggers back to OpenWhisk.

## Why should I contribute?

Are you an app developer who's already built a cool solution with OpenWhisk? Share your solution with the community and get feedback on your work. Show off your rules, actions, and triggers; show others the way and help them avoid the same issues you encountered. At the same time, you might pick up some ideas from people who've done similar work.
