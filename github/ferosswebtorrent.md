# feross/webtorrent

[Original URL](https://github.com/feross/webtorrent)

[Show File Finder](https://github.com/feross/webtorrent/find/master) 

<span class="name"> README.md</span>

 A streaming torrent client in your browser, powered by webRTC and black magic.

[![Magic](https://raw.github.com/feross/webtorrent/master/logo.png)](https://raw.github.com/feross/webtorrent/master/logo.png)

## [](https://github.com/feross/webtorrent#features)Features

- **BitTorrent in your browser!**
- **No plugins** (uses WebRTC Data Channels for peer-to-peer data)
- **Streaming playback** (get first pieces first)

  - Into `video` tag with MediaSource API when possible
  - Flash player with JS bridge for other media types

- Works with .torrent files and magnet links
- Supports DHT (trackerless torrents) over WebRTC

  - Extensions to DHT protocol to work over WebRTC
  - DHT nodes do "peer introductions" so WebRTC can work without a centralized signaling server

- **Supports completely serverless, trackerless operation**

## [](https://github.com/feross/webtorrent#useful-links)Useful Links

### [](https://github.com/feross/webtorrent#dht)DHT
