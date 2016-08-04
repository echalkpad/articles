# koalalorenzo/python-digitalocean

[Original URL](https://github.com/koalalorenzo/python-digitalocean)

> This library provides easy access to Digital Ocean APIs to deploy droplets, images and more. How to install You can install python-digitalocean using pip pip install -U python-digitalocean or via...

This library provides easy access to Digital Ocean APIs to deploy droplets, images and more.

[![](https://camo.githubusercontent.com/4ea9cf978b9599c1ed355f115777ba12475a19a1/68747470733a2f2f7472617669732d63692e6f72672f6b6f616c616c6f72656e7a6f2f707974686f6e2d6469676974616c6f6365616e2e737667)](https://travis-ci.org/koalalorenzo/python-digitalocean)

## [](https://github.com/koalalorenzo/python-digitalocean#how-to-install)How to install

You can install python-digitalocean using **pip**

```
pip install -U python-digitalocean
```

or via sources:

```
python setup.py install
```

## [](https://github.com/koalalorenzo/python-digitalocean#features)Features

python-digitalocean support all the features provided via digitalocean.com APIs, such as:

- Get user's Droplets
- Get user's Images (Snapshot and Backups)
- Get public Images
- Get Droplet's event status
- Create and Remove a Droplet
- Resize a Droplet
- Shutdown, restart and boot a Droplet
- Power off, power on and "power cycle" a Droplet
- Perform Snapshot
- Enable/Disable automatic Backups
- Restore root password of a Droplet

## [](https://github.com/koalalorenzo/python-digitalocean#examples) Examples

### [](https://github.com/koalalorenzo/python-digitalocean#shutdown-all-droplets)Shutdown all droplets

This example shows how to shutdown all the active droplets:

```
import digitalocean
manager = digitalocean.Manager(token="secretspecialuniquesnowflake")
my_droplets = manager.get_all_droplets()
for droplet in my_droplets:
 droplet.shutdown()
```

### [](https://github.com/koalalorenzo/python-digitalocean#creating-a-droplet-and-checking-its-status)Creating a Droplet and checking its status

This example shows how to create a droplet and how to check its status

```
import digitalocean
droplet = digitalocean.Droplet(token="secretspecialuniquesnowflake",
 name='Example',
 region='nyc2', # New York 2
 image='ubuntu-14-04-x64', # Ubuntu 14.04 x64
 size_slug='512mb', # 512MB
 backups=True)
droplet.create()
```

### [](https://github.com/koalalorenzo/python-digitalocean#checking-the-status-of-the-droplet)Checking the status of the droplet

```
actions = droplet.get_actions()
for action in actions:
 action.load()
 # Once it shows complete, droplet is up and running
 print action.status
```

### [](https://github.com/koalalorenzo/python-digitalocean#add-sshkey-into-digitalocean-account)Add SSHKey into DigitalOcean Account

```
from digitalocean import SSHKey

user_ssh_key = open('/home/<$USER>/.ssh/id_rsa.pub').read()
key = SSHKey(token='secretspecialuniquesnowflake',
 name='uniquehostname',
 public_key=user_ssh_key)
key.create()
```

### [](https://github.com/koalalorenzo/python-digitalocean#creating-a-new-droplet-with-all-your-ssh-keys)Creating a new droplet with all your SSH keys

```
manager = digitalocean.Manager(token="secretspecialuniquesnowflake")
keys = manager.get_all_sshkeys()

droplet = digitalocean.Droplet(token="secretspecialuniquesnowflake",
 name='DropletWithSSHKeys',
 region='ams3', # Amster
 image='ubuntu-14-04-x64', # Ubuntu 14.04 x64
 size_slug='512mb', # 512MB
 ssh_keys=keys, #Automatic conversion
 backups=False)
droplet.create()
```

## [](https://github.com/koalalorenzo/python-digitalocean#testing)Testing

### [](https://github.com/koalalorenzo/python-digitalocean#test-using-docker)Test using Docker

To test this python-digitalocean you can use [docker](https://www.docker.com) to have a **clean environment automatically**. First you have to build the container by running in your shell on the repository directory:

```
docker build -t "pdo-tests" .
```

Then you can run all the tests (for both python 2 and python 3)

```
docker run pdo-tests
```

**Note**: This will use Ubuntu 14.04 as base and use your repository to run tests. So every time you edit some files, please run these commands to perform tests on your changes.

### [](https://github.com/koalalorenzo/python-digitalocean#testing-using-pytest-manually)Testing using pytest manually

Use [pytest](http://pytest.org/) to perform testing. It is recommended to use a dedicated virtualenv to perform tests, using these commands:

```
$ virtualenv /tmp/digitalocean_env
$ source /tmp/digitalocean_env/bin/activate
$ pip install -r requirements.txt
```

To run all the tests manually use py.test command:

```
$ python -m pytest
```

## [](https://github.com/koalalorenzo/python-digitalocean#links)Links
