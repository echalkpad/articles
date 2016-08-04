# Monitor and Control Applications Using Supervisor: Part 2 - Tuts+ Code Tutorial

[Original URL](http://code.tutsplus.com/tutorials/monitor-and-control-applications-using-supervisor-part-2--cms-24236)

> In the first part of this two-part tutorial series, we saw to set up and control Supervisor to work with our web applications. In this part, we will see how we can programmatically control Supervisor...

In the [first](http://code.tutsplus.com/tutorials/monitor-and-control-applications-using-supervisor-part-1--cms-23770) part of this two-part tutorial series, we saw to set up and control Supervisor to work with our web applications. In this part, we will see how we can programmatically control Supervisor processes remotely via the [XML-RPC API](http://supervisord.org/api.html).

## Getting Started

In the previous part, we saw that Supervisor provides a web-based GUI which allows us to remotely control the processes with the help of a few clicks. Below is a screenshot of this GUI for the application we created in the previous part:

![Supervisor](https://cms-assets.tutsplus.com/uploads/users/665/posts/24236/image/Screen%20Shot%202015-06-22%20at%2010.42.17%20am.png) A question immediately starts ringing in the brain. What if it were possible to control the Supervisor processes programmatically? This would open up a lot of possibilities where processes can be started or stopped conditionally rather than having them run indefinitely or controlling them manually by clicking buttons or running terminal commands. This is very much possible using the XML-RPC API provided by Supervisor.

## The XML-RPC API

The API itself is pretty straightforward and easy to use. We saw that the HTTP server for Supervisor runs on port 9001 by default. The same can be used to connect using any XML-RPC library. In the case of Python, we can simply use the built-in `xmlrpclib`. Assuming that the Supervisor server runs on <http://localhost:9001/>:

```
>>> import xmlrpclib
>>> server = xmlrpclib.Server('http://localhost:9001/RPC2')
```

To check the current state of the Supervisor server, run:

```
>>> server.supervisor.getState()
{'statename': 'RUNNING', 'statecode': 1}
```

The server daemon can be restarted and stopped by:

```
>>> server.supervisor.restart()
>>> server.supervisor.shutdown()
```

Similarly, the list of all available methods can be fetched by:

```
>>> server.system.listMethods()
```

The [API reference](http://supervisord.org/api.html) provided in the Supervisor documentation is pretty clean and self-explanatory. Going ahead in this tutorial, I will cover an example of how to control the processes based on a condition, and also how we can extend this API in order to implement our own methods.

## Controlling Processes Using the API

Lets say we want a start a new process to send an email every time an action is performed by some user. (There are many ways of doing this, and maybe this is not the best one, but I am just using it as an example to demonstrate this). Let's say the name of this process as defined in `supervisord.conf` is `send_mail`.

```
if user.action_performed:
 server.supervisor.startProcess('send_email')
```

Similarly, we can also trigger automatic restarting of the `supervisord` server daemon in case we have a code change in our application.

```
if app.code_change():
 server.supervisor.restart()
```

This API can similarly be applied to countless such instances.

## Extending the XML-RPC API

We can not only use the standard API methods provided by Supervisor but also write our own methods, extending the API by using the XML-RPC interface factories. This can simply be done by adding a new `[rpcinterface:x]` section in `supervisord.conf`.

```
[rpcinterface:custom]
supervisor.rpcinterface_factory = my_interface:make_custom_rpcinterface
```

Now I will create a small custom interface for the same in a file named `my_interface.py`.

```
from supervisor.rpcinterface import SupervisorNamespaceRPCInterface


class CustomRPCInterface:
 def __init__(self, supervisord):
 self.supervisord = supervisord
 self.retries = 3

 def startProcessOrRetry(self, name, wait=True):
 interface = SupervisorNamespaceRPCInterface(self.supervisord)
 retry = 0

 while not interface.startProcess(name) or retry < self.retries:
 retry = retry + 1


# this is not used in code but referenced via an entry point in the conf file
def make_custom_rpcinterface(supervisord):
 return CustomRPCInterface(supervisord)
```

Above, I have created a method which when called will retry the process three times in case of failure to start.

## Conclusion

In this tutorial series, we learnt how to use Supervisor to monitor and control our applications. We also saw how we can use the Web UI and XML-RPC Interface for the same. Finally, the XML-RPC interface is extended in order to leverage the power of Supervisor to our benefit.
