# Centralized logs with rsyslog, systemd and golang

[Original URL](http://indevwith.streamroot.io/centralized-logs-with-rsyslog-systemd-and-golang/)

> Here is how to build easily a centralized logging pipeline - log drain - with minimal dependencies. Through structured logging syntax, we will show how to effortlessly support monitoring and...

Here is how to build easily a centralized logging pipeline - log drain - with minimal dependencies. Through _structured logging syntax_, we will show how to effortlessly support **monitoring and alerting**.

At Streamroot we like simple, pragmatic yet scalable solutions. Here the only dependencies are `rsyslog` and `systemd` (a.k.a Ubuntu 15.x, etc...).

For demonstration purposes our scenario will use:

- some applications deployed on machine that have `rsyslog` and `systemd` installed (ex: Ubuntu 15.x). We'll call that the **sender side**.
- one machine with `rsyslog` that will receive incoming logs from all applications. We'll call that the **receiver side**.
- _optionally_ an application on the receiver end that can monitor and alert according to your own rules
- _Golang_ to build handy application binaries without introducing new dependencies

Some direct benefits:

- our business applications are unaware they participate in a centralized logging system. That is a good practice of **externalized configuration** and injection through environment
- by using a structured logging syntax, exposing/exporting application metrics imposes no overhead on the application runtime (as far as the application is concerned this is just regular logging)
- you have a central place to tail your logs and apply some alerting and monitoring rules
- adding new applications that send logs down our pipeline is painless and can be easily automated if needed

## Systemd configuration

Our business applications will be managed by `systemd`. You would typically replicate the following configuration to all your applications.

Here is a simple systemd service file for a binary application. Create a new service file at `/etc/systemd/system/myapp.service` with the following content:

```
[Unit]
Description=My application 
After=network.target

[Service]
SyslogIdentifier=streamroot-myapp 
ExecStart=/home/streamroot/goapps/myapp 
Restart=on-failure

[Install]
WantedBy=multi-user.target 
```

The **key part** here is the `SyslogIdentifier` which will be the rsyslog tag to differentiate our applications among other syslog usual system or kernel logging.

If you need a quick and dirty business application to test your log drain create a new `myapp.go` file with:

```
package main

import ( 
 "log"
 "os"
 "time"
)

func main() { 
 hostname, _ := os.Hostname()
 log.Println("Starting business application")
 for {
 time.Sleep(10 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii time.Second)
 log.Printf("hello from %s", hostname)
 }
}
```

Then compile and copy that to the sender machine with:

```
GOARCH=amd64 GOOS=linux go build myapp.go 
scp myapp sendermachine:~/goapps 
sudo systemctl start myapp 
```

## Rsyslog configuration

Next at the end of the rsyslog configuration file `/etc/rsyslog.conf` add:

```
# TCP forwarding
:syslogtag, startswith, "streamroot-"@@10.0.0.51
```

- the ip address will be the ip of the receiver side (the port default conveniently to 514)
- `:syslogtag` allow us to only forward to the receiving side the logs from our business applications using the `SyslogIdentifier` defined above
- if you want to use UDP forwarding use only one `@` sign instead of two

Then restart your local syslog with:

```
sudo systemctl restart rsyslog 
```

## Journald configuration

Still on the sender side, we need to allow `journald` (logging system of systemd) to forward to the local syslog. So make sure you uncomment the line `ForwardToSyslog=yes` from `/etc/systemd/journald.conf`

Additionally, using `journalctl`, you can tail locally your application in many ways. Either through it systemd service name. You will get all kinds off logs related to your application (systemd itself, ...):

```
journalctl -f -u myapp 
```

Or you can tail using the `SyslogIdentifier` (a.k.a syslog tag). In this case, the logs lines will only be the one written by your application:

```
journalctl -f -t streamroot-myapp 
```

The receiver side is the machine receiving all logs stream from our business applications. Its `/etc/rsyslog.conf` must have the following line uncommented to receive the traffic:

```
# provides TCP syslog reception
module(load="imtcp") 
input(type="imtcp" port="514") 
```

Then restart your local syslog.

From this machine, you can already follow the incoming stream of logs with:

```
sudo tail -f /var/log/syslog 
```

Handy!

## Monitoring and alerting

On the receiver side, you can deploy a small monitoring application that would subscribe to the local log stream.

In order to do that, configure the local rsyslog receiver to forward to our monitoring application. Let's say this application listens on port 5000\. Append to the local `/etc/rsyslog.conf` file:

```
# Forward all message through TCP
*.* @@localhost:5000
```

Restart.

All that is left to do, is to write an code that listens on port 5000, knows how to parse syslog message and that contains your monitoring and alerting rules.

Below would be the skeleton of such an app written in Go:

```
package main

import ( 
 "log"
 "net"
 "strings"

 "github.com/jeromer/syslogparser"
 "github.com/jeromer/syslogparser/rfc3164"
)

const ( 
 SYSLOG_APP_PREFIX = "streamroot-"
)

type event struct { 
 Machine string `json:"machine"`
 App string `json:"app"`
 Message string `json:"message"`
}

func main() { 
 ln, err := net.Listen("tcp", ":5000")
 if err != nil {
 log.Fatalf(err)
 }

 log.Println("Starting receiving Syslog stream on :5000")

 for {
 conn, err := ln.Accept()
 if err != nil {
 log.Printf("new conn: %s", err)
 continue
 }

 go parseMessage(conn)
 }
}

func parseMessage(conn net.Conn) { 
 defer conn.Close()

 buff := make([]byte, 4096)

 if _, err := conn.Read(buff); err != nil {
 log.Printf("reading on conn: %s", err)
 return
 }

 evt, err := parseEvent(buff)
 if err != nil {
 log.Printf("parsing event: %s", err)
 }

 if evt != nil {
 // alert and monitor away!
 }
}

func parseEvent(syslogMsg []byte) (*event, error) { 
 p := rfc3164.NewParser(syslogMsg)

 if err := p.Parse(); err != nil {
 return nil, err
 }

 parts := p.Dump()

 if ok, app := isStreamrootApp(parts); ok {
 return &event{
 App: app,
 Message: parts["content"].(string),
 Machine: parts["hostname"].(string),
 }, nil
 }

 return nil, nil
}

func isStreamrootApp(parts syslogparser.LogParts) (bool, string) { 
 tag := parts["tag"].(string)
 index := strings.Index(tag, SYSLOG_APP_PREFIX)
 if index != 0 {
 return false, ""
 }

 return true, tag[len(SYSLOG_APP_PREFIX):]
}
```

A first simple use case - extending this code - could be to expose parsed events through a websocket subscription: internal log dashboards, etc ...

## Structured logging syntax

To help parsing and running your alerting rules on incoming syslog messages, you should decide on a common logging syntax. Hopefully, such a syntax has been thought out already. You can use for instance the `l2met` one.

The simplest form is to use the `key=value` format in your log line.

You want to send a notification in **Slack** into the _alert_ room, do it with:

```
log.Printf("cannot start application slack=alert") 
```

You want to send an _sms_ to the 1st line of support using **Twilio**, do it with:

```
log.Printf("cannot start application sms=roberto") 
```

For advance metrics storage you can use the `measure#key=value` format.

You want to store into **InfluxDB** the number of request per seconds, do it with:

```
log.Printf("collector throughput measure#collector.rps=156900") 
```

Hack away!
