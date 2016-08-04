# You Are Most Likely Misusing Docker

[Original URL](http://www.mpscholten.de/docker/2016/01/27/you-are-most-likely-misusing-docker.html)

> Docker started as a tool to enable easier scaling of your applications. It was built to support the needs of a platform as a service provider[0]. Today docker is usually used to distribute...

Docker started as a tool to enable easier scaling of your applications. It was built to support the needs of a platform as a service provider[[0]](http://www.mpscholten.de/docker/2016/01/27/you-are-most-likely-misusing-docker.html#note-0).

Today docker is usually used to distribute applications instead of just for easier scaling. If you're using docker for your development environment you're probably not using docker for scaling, you're using it to get a reproducable development environment.

## Distributing Software Sucks

Docker is used for distributing applications because distributing software sucks. Just look at how many different package managers there are: yum, apt-get, npm, composer, gem, cabal etc. Each language ecosystem now has it's own package manager. Basically each of these package managers are doing the same tasks: Ensuring that a dependency is available to a given application. Sometimes this means downloading binaries and putting them into the `PATH`, sometimes this means downloading javascript source code and making it available to the runtime environment.

Many binary package managers are storing the installed packages in a global mutable package directory, e.g. `/usr/local/bin`. Let's say we have an application `A`, depending on ruby 1.8.1 which is installed in `/usr/local/bin` and available in the `PATH`. Now we want to install a different application `B` which requires ruby 2.2.1, thus we're going to upgrade ruby. Now `A` is broken because `A` is not compatible with the new ruby version. So by installing `B` we've broken `A`. In other words: installing a package has side effects. This is because of the global mutable package directory.

Because of these side effects you cannot _just install something_. You always have to worry about breaking your existing setup. This is why containers are awesome: You can play around and later just restart the container. You avoid the side effects of package managers by just shipping the final cake instead of the recipe to make it. It also means that the thing we want to run will just work. There are no errors because of missing dependencies.

The down side is that the container will be huge in size. This means slow deployments and slower tools. The containers also usually contain an entire operating system. Think about it: You're running an operating system just to run containers with their own operating systems. This kind of feels dirty. But having side effects is also not very nice.

So we're using containers to get reproducible builds and the magical "it just works" feeling. We're using containers to avoid the downsides of bad package managers.

## Welcome to the future: Nix

Nix is a package manager which breaks with the dogma of having a global mutable package directory. To cite the nix website:

> Nix's purely functional approach ensures that installing or upgrading one package cannot break other packages. This is because it won't overwrite dependencies with newer versions that might cause breakage elsewhere. It allows you to roll back to previous versions, and ensures that no package is in an inconsistent state during an upgrade.

Let us get back to the example applications `A` and `B`. With nix we can just say "A needs ruby 1.8.1" and "B needs ruby 2.2.1". [Nix can now run the applications with specific environments for each of the applications](https://nixos.org/nix/manual/#sec-nix-shell). `A` has ruby 1.8.1 in it's `PATH` and `B` has ruby 2.2.1 in it's `PATH`.

In the background nix is managing a [side effect free package store](https://nixos.org/nix/about.html). Two different ruby versions can live there because there is no global environment in the sense of typical package managers. A typical package manager is adding more and more stuff to the global `PATH`. Nix however is not adding stuff to the `PATH`[[1]](http://www.mpscholten.de/docker/2016/01/27/you-are-most-likely-misusing-docker.html#note-1). To use a package you have to tell nix explicitly otherwise the package is not available to you.

Because nix is side effect free you don not need containers to get reproducible builds. Nix removes the downsides of package management and thus replaces an integral part of what docker is used for today. You don't need to ship a second operating system with your containers when you can replace the whole container stuff with nix.

There are some cases where containers makes sense. As already said scaling software with containers is easy. But most docker users are not having these kind of problems. They're into docker for the "it just works" feeling.

Docker and nix are not even contrary. You can use nix to build your docker images. Now your developers can run your software without using docker because they can get the same setup as in the container running on their native machine while the production setup is using docker for easier scaling.

## Takeaways

Docker is for scaling, but is misused to distribute reproducible builds. The reason: Package management sucks and is unpredictable because of mutable global state which leads to side effects. Use a good package manager like nix for reproducible builds of your software and you most likely don't need docker anymore. A good package manager allows you to play around without breaking your environment, just like containers do.

_[Learn more about nix here. You will not regret it.)](https://nixos.org/nix/about.html)_

_Thanks for reading :) [Follow me on twitter](https://twitter.com/_marcscholten) if you're interested in more of this stuff!_

[0] Initial docker was part of dotCloud, a platform-as-a-service company.

[1] Except when using [nix-env](https://nixos.org/nix/manual/#sec-nix-env). But this is for users (e.g. you, when using the shell) and not applications.

Hey! I'm Marc, an eighteen years old software engineer located in Germany. [Learn more about me](http://www.mpscholten.de/about/).
