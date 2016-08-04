
[Original URL](https://medium.com/@arpith/resetting-a-ticker-in-go-63858a2c17ec)

> Raft is the distributed consensus algorithm that I'm using for the database I'm building at the

[Raft](https://raft.github.io/) is the distributed consensus algorithm that I'm using for the [database](https://github.com/arpith/mmapd) [I'm](https://medium.com/@arpith/adventures-with-mmap-463b33405223#.kbv1sdpce) [building](https://medium.com/@arpith/go-sharing-memory-by-communicating-in-practice-4761bbe5dcf1#.49fe4mdon) [at](https://medium.com/@arpith/reading-a-go-map-safely-277bb08ed144#.vjbxfptm9) the [Recurse Center](http://recurse.com). The [algorithm](https://raft.github.io/raft.pdf) has [two timeouts](http://thesecretlivesofdata.com/raft/#election) -- a heartbeat timeout and an election timeout. When a server receives a heartbeat from the leader, it resets its election timeout. If it doesn't receive a heartbeat before its election timeout runs out, it tries to elect itself as leader!

As a first step towards this, I need to write code that resets a timeout (say 2 seconds) every time another timeout (say 5 seconds) expires. I would expect to see output that looked like:

```
Elapsed: 0
Elapsed: 2 Ticker A
Elapsed: 4 Ticker A
Elapsed: 5 Ticker B - Going to reset Ticker A!
Elapsed: 7 Ticker A
Elapsed: 9 Ticker A
```

If I hadn't reset the other ticker, of course, I would expect to see:

```
Elapsed: 0 
Elapsed: 2 Ticker A
Elapsed: 4 Ticker A
Elapsed: 5 Ticker B
Elapsed: 6 Ticker A
Elapsed: 8 Ticker A
```

Here's how I wrote this in Go, using [ticking channels](https://golang.org/pkg/time/#Tick). I set up a goroutine that listens on two channels, tickerA and tickerB. Every time tickerB goes off, it resets tickerA.

```
type server struct {
 doneChan chan bool
 tickerA ticker
 tickerB ticker
}

func (s *server) listener() {
 start := time.Now()
 for {
 select {
 case <-s.tickerA.ticker:
 elapsed := time.Since(start)
 fmt.Println("Elapsed: ", elapsed, " Ticker A")
 case <-s.tickerB.ticker:
 s.tickerA.resetTicker()
 elapsed := time.Since(start)
 fmt.Println("Elapsed: ", elapsed, " Ticker B - Going to reset ticker A")
 }
 }
 s.doneChan <- true
}

func main() {
 doneChan := make(chan bool)
 tickerA := createTicker(2 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js MITIE netdata png pngquant start-thesrc thesrc ucii time.Second)
 tickerB := createTicker(5 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js MITIE netdata png pngquant start-thesrc thesrc ucii time.Second)
 s := &server{doneChan, *tickerA, *tickerB}
 go s.listener()
 <-doneChan
}
```

And my tickers and the methods to create and reset them look like:

```
type ticker struct {
 period time.Duration
 ticker <-chan time.Time
}

func createTicker(period time.Duration) *ticker {
 return &ticker{period, time.Tick(period)}
}

func (t *ticker) resetTicker() {
 t.ticker = time.Tick(t.period)
}
```

The actual output is:

```
$ go run tick.go

Elapsed: 2.00074535s Ticker A

Elapsed: 4.002086857s Ticker A

Elapsed: 5.004973744s Ticker B - Going to reset ticker A

Elapsed: 7.008515813s Ticker A

Elapsed: 9.009572001s Ticker A
```

That's about it! [Check it out](https://play.golang.org/p/Hj9I8j_IFb)!

Have a better way to do this? Just want to say hi? [I'm](http://arpith.co) [on Twitter](http://twitter.com/arpith)!
