# icodeforlove/doapi

[Original URL](https://github.com/icodeforlove/doapi)

> The doapi module allows you to communicate with the DigitalOcean API V2 from node.js in a promise friendly manner. It also supports automatic request retries.

[![NPM version](https://camo.githubusercontent.com/be0f28b1db348eb04822a25645649b6e8f501c22/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f762f646f6170692e7376673f7374796c653d666c61742d737175617265)](https://npmjs.org/package/doapi) [![Downloads](https://camo.githubusercontent.com/db96c3d5399df7cc6a700061360f77d637818c31/687474703a2f2f696d672e736869656c64732e696f2f6e706d2f646d2f646f6170692e7376673f7374796c653d666c61742d737175617265)](https://npmjs.org/package/doapi)

The doapi module allows you to communicate with the [DigitalOcean API V2](https://developers.digitalocean.com/documentation/v2/) from node.js in a promise friendly manner.

It also supports automatic request retries.

## [](https://github.com/icodeforlove/doapi#installation)Installation

This module is published in NPM:

```
npm install doapi --save
```

The `--save` tells NPM to automatically add it to your `package.json` file

## [](https://github.com/icodeforlove/doapi#usage)Usage

```
// Import a module
var DigitalOceanAPI = require('doapi');

// Create an instance with your API V2 credentials
var api = new DigitalOceanAPI({token: 'my_token'});

// Get things done
api.dropletGetAll().then(function (droplets) {
 console.log(droplets);
});
```

## [](https://github.com/icodeforlove/doapi#config)Config

```
new DigitalOceanAPI({
 token: 'my_token',
 itemsPerPage: 100, // default=100
 maxRetries: 5, // default=5
 raw: false // default=false
});
```

## [](https://github.com/icodeforlove/doapi#pagination)Pagination

you can pass pagination params into any method that has a `body` or `query` argument.

```
api.dropletGetAll({per_page: 1, page: 2}).then(function (droplets) {
 console.log(droplets);
});
```

## [](https://github.com/icodeforlove/doapi#raw)Raw

if you set raw it will return the full response body with `request info` and `ratelimiting details`, the default is false.

would return

```
 {
 account: {
 droplet_limit: 25,
 email: 'email@domain.com',
 uuid: 'f5bbaffce3a8792421593a7075b486bafd66672f',
 email_verified: true
 },
 ratelimit: {
 limit: '5000',
 remaining: '4993',
 reset: '1434197547'
 },
 requestinfo: {
 id: 'a24427fd-0d43-9536-a206-zac22d2696e1',
 runtime: '0.038537'
 }
 }
```

and with raw set to false (the default), it would return

```
 {
 droplet_limit: 25,
 email: 'email@domain.com',
 uuid: 'f5bbaffce3a8792421593a7075b486bafd66672f',
 email_verified: true
 }
```

## [](https://github.com/icodeforlove/doapi#debugging)Debugging

we use the debug module so you can debug the http requests by doing the following

```
DEBUG=http node myfile.js
```

[![image](http://cdn.img42.com/4ad5f305b6fe80613c90aadf54337598.png)](https://camo.githubusercontent.com/30febf8b3cdc03060c1bc322bce0164b225380c3/687474703a2f2f63646e2e696d6734322e636f6d2f34616435663330356236666538303631336339306161646635343333373539382e706e67)

also all methods enforce type checking so invalid usage would result in errors like this

[![image](http://cdn.img42.com/cf073d9c3bf95bc0355045a024ad0be4.png)](https://camo.githubusercontent.com/faf214c31c2d1e855499f41d92adc2b4506179b2/687474703a2f2f63646e2e696d6734322e636f6d2f63663037336439633362663935626330333535303435613032346164306265342e706e67)

## [](https://github.com/icodeforlove/doapi#methods)Methods

All methods follow the [official API documentation](https://developers.digitalocean.com/documentation/v2/).

### [](https://github.com/icodeforlove/doapi#droplets)Droplets

- [dropletGetAll(Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-droplets)
- [dropletNew(Object body, [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#create-a-new-droplet)
- [dropletGet(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#retrieve-an-existing-droplet-by-id)
- [dropletReboot(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#reboot-a-droplet)
- [dropletPowerCycle(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#power-cycle-a-droplet)
- [dropletShutdown(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#shutdown-a-droplet)
- [dropletPowerOff(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#power-off-a-droplet)
- [dropletPowerOn(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#power-on-a-droplet)
- [dropletPasswordReset(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#password-reset-a-droplet)
- [dropletResize(Number id, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#resize-a-droplet)
- [dropletSnapshot(Number id, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#snapshot-a-droplet)
- [dropletRestore(Number id, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#restore-a-droplet)
- [dropletRebuild(Number id, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#rebuild-a-droplet)
- [dropletRename(Number id, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#rename-a-droplet)
- [dropletDestroy(Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#delete-a-droplet)
- [dropletKernalsGetAll(Number id, Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-available-kernels-for-a-droplet)
- [dropletSnapshotsGetAll(Number id, Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-snapshots-for-a-droplet)
- [dropletBackupsGetAll(Number id, Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-backups-for-a-droplet)
- [dropletActionGetAll(Number id, Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-actions-for-a-droplet)
- [dropletNeighborsGetAll(Number id, Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-neighbors-for-a-droplet)
- [dropletUpgradesGetAll(Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-droplet-upgrades)
- [reportDropletNeighborsGetAll(Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-droplet-neighbors)

## [](https://github.com/icodeforlove/doapi#account)Account

- [accountGet([Boolean raw])](https://developers.digitalocean.com/documentation/v2/#get-user-information)

### [](https://github.com/icodeforlove/doapi#regions)Regions

- [regionGetAll(Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-regions)

### [](https://github.com/icodeforlove/doapi#images)Images

### [](https://github.com/icodeforlove/doapi#ssh-keys)SSH keys

### [](https://github.com/icodeforlove/doapi#sizes)Sizes

- [sizeGetAll(Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-sizes)

### [](https://github.com/icodeforlove/doapi#domains)Domains

- [domainGetAll(Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-domains)
- [domainNew(String name, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#create-a-new-domain)
- [domainGet(String name [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#retrieve-an-existing-domain)
- [domainDestroy(String name [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#delete-a-domain)
- [domainRecordGetAll(String name, Object query [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#list-all-domain-records)
- [domainRecordNew(String name, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#create-a-new-domain-record)
- [domainRecordGet(String name, Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#retrieve-an-existing-domain-record)
- [domainRecordEdit(String name, Number id, Object body [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#update-a-domain-record)
- [domainRecordDestroy(String name, Number id [, Boolean raw])](https://developers.digitalocean.com/documentation/v2/#delete-a-domain-record)

### [](https://github.com/icodeforlove/doapi#actions)Actions

### [](https://github.com/icodeforlove/doapi#floating-ips)Floating IPs
