# basic infrastructure patterns

[Original URL](http://www.scriptcrafty.com/basic-infrastructure-patterns/)

> Some basic patterns I've noticed come up over and over again while working on build/CI/deployment related things. Pipeline The pipeline is the bedrock on which pretty much everything else is...

Some basic patterns I've noticed come up over and over again while working on build/CI/deployment related things.

## Pipeline

The pipeline is the bedrock on which pretty much everything else is built. A properly designed pipeline takes well-defined inputs and produces well-defined outputs or it halts somewhere and indicates exactly what failed. The defining characteristic of the pipeline is it's compositional nature. If you've ever played with functional languages then this shouldn't be surprising since a pipeline is just another incarnation of a function (in the mathematical sense).

You don't need anything fancy to build a useful pipeline. Most of the time I get away with pretty vanilla bash and for the times when bash isn't enough I use Ruby and rake. You can pick your favorite language and make-like tool just as long as it's not grunt, gulp, or broccoli. If you do need to stick with JavaScript then use [jake](http://jakejs.com/). What follows are some other basic patterns that can be used to enhance the functionality of the basic pipeline.

## Hashed Cache

If you know what a hashmap is then this pattern is the hashmap carried over to the file system for managing well-defined collections of files, like node modules, and it comes in very handy in optimizing build/test pipelines.

Modern web applications have a shitload of dependencies both for the front-end and the back-end. Fortunately modern software development best practices encourage being very explicit about those dependencies. Ruby has bundler and Gemfile/Gemfile.lock, Node has npm and packages.json/npm-shrinkwrap.json, Python has pip and requirements.txt, Elixir has mix and mix.exs, etc. The defining characteristic of these things is that they unambiguously describe what the application depends on.

You're probably thinking that's great so what is the point of caching something that is a one time operation anyway. Well, when you're building things in Jenkins or some other CI environment you might not have the luxury of incremental development or the price of doing things incrementally might be more trouble than it's worth because there is all sorts of state that can stick around and muck up your pipeline and also keeping things around for doing things incrementally might not be possible (e.g. when using spot instances for worker pools). I have also yet to see cache management that works properly and doesn't unnecessarily fill-up the disk by clogging up /tmp or ${HOME}. In an ideal world this would not be a problem but in the real world it often times is and the easiest thing to do is not rely on cache management provided by the package managers.

My goto trick in these cases is to localize the installation directory for the dependencies (if the package manager doesn't do it already), hash the file that describes the dependencies (Gemfile.lock, npm-shrinkwrap.json, etc.), and then generate tar.xz file that contains all the installed dependencies named by the hash of the file that describes the dependencies (Gemfile.lock, npm-shrinkwrap.json, etc). Once the tar.xz file is generated I can stow it away locally or somewhere in S3 and keep re-using it as long as the hash matches. This means even when I blow away the local environment I don't have to go over the network to re-download my dependencies (well I do if I put things in S3 but I only have to go to one place instead of 300) and can simply unpack a tar.xz file to get things back in working order. I might also need to twiddle things by calling build scripts because some packages will put various files somewhere other than the project directory when they're installed but this is usually easy enough to fix.

Some basic pseudo-bash to demonstrate the point

```
for d in 'f1' 'f2' 'f3'; do
 pushd "${d}"
 # Get the hash of the file that describes the dependencies
 h="$(shasum npm-shrinkwrap.json | awk '{print $1}')"
 # Install and pack the dependencies
 npm install || exit 1
 xz_file="${d}-${h}.txz"
 XZ_OPT=-9e tar cJf "${xz_file}" node_modules
 # Stow them away in S3 (or locally depending on your preference)
 aws s3 cp "${xz_file}" "${s3endpoint}/${xz_file}" || exit 1
 popd
done
```

I did this for a project at work that has a bunch of node modules scattered throughout a few sub-folders and build/test failures because of network issues are now a long forgotten memory. There are other things that fail now but at least it's not because of npm. The same principles can be applied to other package management systems but unlike in the npm case you might need to dig through some documentation to figure out where exactly modules are stored before being installed.

## Retries and Fallbacks

So you have a pipeline with well-defined inputs and outputs at each step and you also cache various artifacts so that there is no unnecessary work but this still doesn't mean you have a foolproof pipeline. Again, doing the obvious thing will get you 90% of the way there and the other 10% should be rare enough that human intervention is not a high cost to pay.

Suppose somewhere in your pipeline you are fetching something from S3, e.g. some tar.xz that contains a bunch of node modules, but since this is something that requires the network and the network can and very likely will fail you want to make it a bit more robust. The simplest thing to do is just re-try a few times

```
h="$(shasum npm-shrinkwrap.json | awk '{print $1}')"
for i in $(seq 1 5); do
 if [[ $(wget -O "${h}.txz" "${s3endpoint}/${h}.txz" > /dev/null && \
 tar xf "${h}.txz" > /dev/null && echo good) ]]; then
 break
 fi
 echo "Failed to download and extract ${h}.txz. Sleeping and re-trying."
 sleep 20
done
```

The above piece of code will work but there are a few more things we can do to make things even more reliable.

If this is a persistent environment that you control or at least have enough control over to make sure some folders and files stick around for some period of time then instead of going to S3 every time you should first check locally and re-use whatever already exists (assuming of course that when you download something from S3 you stow it away locally)

```
h="$(shasum npm-shrinkwrap.json | awk '{print $1}')"
if [[ -e "${local}/${h}.txz" ]]; then
 tar xf "${local}/${h}.txz" && touch "${h}-done"
fi
if [[ ! -e "${h}-done" ]]; then
 # Download from S3, stow away locally, and unpack
 # ... && touch "${h}-done"
fi
```

This is pretty good because now we don't have to worry about the network if we already have things locally but there is one more thing we can do in case we can't find things locally or get them from S3

```
# Try local file system
# ...
# Try S3
# ...
# If both of the above fail then just do npm install
if [[ ! -e "${h}-done" ]]; then
 npm install || exit 1
fi
```

This is pretty good now. We have retries and fallback mechanisms in case things fail and we avoid unnecessary work as much as possible. Win all around.

## Pipeline in a Loop

In control theory there are the notions of stable and unstable feedback loops. When designing control loops for servers the loops must always be stable and fail as safely as possible (which basically means leaving the system as is and alerting someone of what happened). Auto-scaling groups in AWS are a basic example of such control mechanisms although I don't know what they offer in terms of making the loops stable.

I'm currently working on a such a loop for controlling a pool of Jenkins workers. The workload can vary throughout the day and there is no reason to keep 100 workers around when only 10 are required to clear the work queue. Since we are not trying to be very fancy we can get away with a very basic setup. Some pseudo-Ruby to demonstrate

```
# Grab the state on AWS and Jenkins side
aws_state = AwsState.new(aws_opts)
jenkins_state = JenkinsState.new(jenkins_opts)

# See if there are any defunct workers and get rid of them.
# Defunct worker means there is no corresponding EC2 node on
# the AWS side
defunct_workers = jenkins_state.workers - aws_state.nodes
if defunct_workers.any?
 defunct_workers.each do |worker|
 jenkins_state.delete_worker(worker)
 end
 # Flush the worker cache because we just deleted some
 jenkins_state.workers!
end

# Now see if there are any new nodes that have come online
# and register them with Jenkins. After registering new nodes
# we exit to give things some time to settle
new_nodes = aws_state.nodes - jenkins_state.workers
if new_nodes.any?
 new_nodes.each do |node|
 jenkins_state.register_worker(node)
 end
 exit
end

# If we get this far that means everything is synchronized
# so we can now figure out what we need to do in terms of capacity, i.e.
# kill some existing workers or bring up new ones to clear the queue
work_queue = jenkins_state.queue
free_workers = jenkins_state.workers!.select(&:free?)
# If there are free workers see if we need to downsize
if free_workers.any? and free_workers.length > min_worker_count
 downsizing_count = determine_downsizing_count(free_workers, 
 work_queue, min_count)
 if downsizing_count > 0
 # This is slightly unsafe but we have another pipeline
 # for making sure jobs are re-tried in case we 
 # accidentally kill a worker that is doing work
 (0...downsizing_count).each do |i|
 aws_state.kill_node(free_workers[i])
 end
 end
 # We again exit to give things some time to settle
 exit
end

# There are no free workers so we need to bring up some new ones
# and after bringing them up we exit because we want to give 
# the new nodes some time to get ready. Notice that we
# don't register them and instead wait to go back to the
# top of the loop
new_worker_count = determine_new_worker_count(work_queue, 
 jenkins_state.workers, max_worker_count)
if new_worker_count > 0
 aws_state.instantiate_workers(new_worker_count)
 exit
end
```

Hopefully you get the gist of the idea. Generalizing things a little bit here are the steps for the basic state synchronizing pipeline in a loop

```
# 1\. Grab current state of various sub-systems
# 2\. Do some basic diffing and get a list of 
# synchronization actions
# 2a. Perform actions as safely as possible
# 2b. If we performed any actions then
# wait a little bit and go to 1.
# 3\. Grab workload statistics for various sub-systems
# 3a. Crunch some numbers and get a list of 
# corrective actions
# 3b. Perform the corrective actions as 
# safely as possible
# 4\. Wait a little bit
# 5\. Goto 1.
```

Next up: basic deployment patterns.
