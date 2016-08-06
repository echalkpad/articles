# Minimal Docker Containers for Node.js

[Original URL](https://blog.risingstack.com/minimal-docker-containers-for-node-js/)

> As some of the most innovative companies like Oculus and Gilt Groupe jump into Docker containers, more and more base images appear on Docker Hub that can be used to containerize your applications....

As some of the most innovative companies like Oculus and Gilt Groupe jump into [Docker containers](https://blog.risingstack.com/shipping-node-js-applications-with-docker-and-codeship/), more and more base images appear on [Docker Hub](http://hub.docker.com) that can be used to containerize your applications. With this said most of these Docker images has one or more of the following issues:

- big in size
- strange / non-existent versioning
- mutable tags
- unmaintained

**We are happy to release our [Alpine-based Node.js Docker images](https://hub.docker.com/r/risingstack/alpine/tags/)** that tries to solve those pain points. In this article, you will learn about why we went with Alpine, how we version our images and how you can start building applications using them today.

## Meet Alpine Linux

> Alpine Linux is a Linux distribution based on musl and BusyBox, primarily designed for "power users who appreciate security, simplicity and resource efficiency". It uses PaX and grsecurity patches in the default kernel and compiles all userspace binaries as position-independent executables with stack-smashing protection.

![Alpine\_Linux\_in\_Docker\_Containers\_for\_Node\_js\_post](https://risingstack-blog.s3.amazonaws.com/2016/Feb/Alpine_Linux_in_Docker_Containers_for_Node_js_post-1455116850143.png)

Lately, rumors arose that even Docker, Inc is planning to move its infrastructure from Ubuntu to Alpine.

## Versioning our Docker Images

At first, it may seem strange how we versioned our Alpine-based docker images - let me explain.

Each tag that we released and going to release has the following format: `[linux-version]-[node-version]-[project-version]`. So if you see something like this: `3.3-v4.2.6-1.1.3` then that means that you are going to use 3.3 of the given Linux distribution, v4.2.6 of Node.js and the project that generated the image is at 1.1.3\. You may wonder: why do we need the project version? As we love immutable deployments we believe that Docker tags should be immutable as well - we achieve this immutability by appending that extra version number.

The reason for this is the following: if dependent package that's not related to the operation system nor Node.js itself directly (like a security update) we bump the package version number.

## Dockerizing Your Node.js Application

Once you have [Docker installed](https://docs.docker.com/mac/step_one/) on your computer containerizing your application is straightforward.

First, you have to create a `Dockerfile`.

Once you have it simply copy-paste the following code snippet:

So far so good - but what's going on here? We just copy our `package.json` into the image, then run `npm install` then copy the rest. These copy steps are done separately because Docker works using layers that can be cached. So if our package.json does not change, Docker doesn't need to install the dependencies on each build saving us a lot of time.

## Next up

In the coming weeks, you will see more articles on how can you use Docker containers with Node.js and microservices on the blog. To be notified, subscribe to our newsletter!

Get early access to our posts
