# wmira/dropkit

[Original URL](https://github.com/wmira/dropkit)

> dropkit dropkit is a node.js module for Digital Ocean's V2 REST API. Usage: npm install –save dropkit DropKit uses BlueBird and returns promises. var Dropkit

## [](https://github.com/wmira/dropkit#dropkit)dropkit

dropkit is a node.js module for Digital Ocean's V2 REST API.

[![Build Status](https://camo.githubusercontent.com/9c06af63a6a5186e5054b4251875ec21328fb30d/68747470733a2f2f7472617669732d63692e6f72672f776d6972612f64726f706b69742e7376673f6272616e63683d6d6173746572)](https://travis-ci.org/wmira/dropkit)

## [](https://github.com/wmira/dropkit#usage)Usage:

npm install –save dropkit

DropKit uses [BlueBird](https://github.com/petkaantonov/bluebird) and returns promises.

```
 var Dropkit = require("dropkit");
 var v2 = new Dropkit("TOKEN");


 v2.accounts().then(function(account) {
 console.log(account);
 }).error(function(error) {
 console.log("httpStatusCode: " + error.statusCode);
 console.log("response: " + error.res);
 });
```

## [](https://github.com/wmira/dropkit#api)API:

See the DigitalOcean's [V2 Rest API](https://developers.digitalocean.com/#introduction) for return definition

## [](https://github.com/wmira/dropkit#accounts)Accounts

```
 v2.accounts();

 v2.account.keys(); //return all keys
 v2.account.keys(keyIdOrFingerPrint); //retrieve key info
 v2.account.keys({name: 'keyname',public_key: "ssh-rsa ..."}); //create a new key
 v2.account.key.update(keyIdOrFingerPrint,newname);
 v2.account.key.destroy(keyIdOrFingerPrint);
```

## [](https://github.com/wmira/dropkit#actions)Actions

```
 v2.actions(); //list all actions
 v2.actions({page=1,per_page=2}); //pass parameter on action list
 v2.actions(actionId); //retrieve specific actionid
```

## [](https://github.com/wmira/dropkit#domains)Domains

```
 v2.domains(); //list all domains
 v2.domains(domainName); //get domain info
 v2.domain.create({name:'domain.com','ip_address':"127.0.0.1"});
 v2.domain.delete("domain.com"); //delete
```

## [](https://github.com/wmira/dropkit#domain-records)Domain Records

```
 v2.records(domainName); //list all records for a given domain
 v2.records(domainName,recordId);//return info for a particular record id

 //create a new data, where recordData is https://developers.digitalocean.com/#create-a-new-domain-record
 v2.record.create(domainName,recordData);

 v2.record.update(domainName,recordId,"newrecordname");//update the name of a record
 v2.record.delete(domainName,recordId); //delete the record id
```

## [](https://github.com/wmira/dropkit#droplets)Droplets

```
 v2.droplets(); //return list of droplets
 v2.droplets(dropletId); //return info for droplet id

 v2.droplet_upgrades(); //return droplet upgrades

 //create a droplet where droplet is https://developers.digitalocean.com/#create-a-new-droplet
 v2.droplet.create(droplet);

 v2.droplet.kernels(dropletId);
 v2.droplet.snapshots(dropletId);
 v2.droplet.backups(dropletId);
 v2.droplet.delete(dropletId);

 //action here can be disable_backups,reboot,power_cycle,shutdown,power_off,power_on,restore,password_reset ..etc
 //see https://developers.digitalocean.com/#droplet-actions
 v2.droplet.action(dropletId,action);
```

## [](https://github.com/wmira/dropkit#droplets-1)Droplets

```
 v2.images(); //return all images
 v2.images({type:"distribution"}); //list all distribution images
 v2.images({type:"application"}); //list all application images
 v2.images(id); //retrieve an image id

 v2.image.update(id,"newName"); //update name of id
 v2.image.delete(id); //as specified
 v2.image.transfer(imageId,'nyc2'); //transfer image id to specified region
 v2.image.action(imageId,actionId); //retrieve actionId
```

## [](https://github.com/wmira/dropkit#others)Others

```
 v2.regions();
 v2.sizes();
```

## [](https://github.com/wmira/dropkit#dev)Dev

## [](https://github.com/wmira/dropkit#todo)TODO
