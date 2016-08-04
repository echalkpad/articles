# Just a Theory

[Original URL](http://justatheory.com/computers/os/macosx/rsnapshot_and_launchd.html)

> Just a few quick notes on how I set up launchd to run rsnapshot to backup my new iMac. The configurations I made are based on Kenn Christ's blog entry. Installed the rsnapshot port: sudo port install...

Just a few quick notes on how I set up `launchd` to run `rsnapshot` to backup my new iMac. The configurations I made are based on Kenn Christ's [blog entry](http://www.inmostlight.org/2006/03/easy-backups-with-rsnapshot "Easy backups with rsnapshot").

1. Installed the rsnapshot port:

  ```
  sudo port install rsnapshot
  ```

2. Changed _/opt/local/etc/rsnapshot.conf_ as follows:

  ```
  snapshot_root   /Volumes/Demiterra/Backup/
  #interval   hourly  6
  rsync_long_args --delete --numeric-ids --relative --delete-excluded -extended-attributes
  exclude *.cpan*
  link_dest   1
  #backup /home/      localhost/
  #backup /etc/       localhost/
  #backup /usr/local/ localhost/
  backup  /Users/     
  ```

  Note that I've commented out hourly backups and the default backup directories. I'm using the _Backups_ subdirectory on a [My Book](http://www.wdc.com/en/products/Products.asp?DriveID=224 "Western Digital My Book™ Premium Edition™") half terrabyte drive that I picked up at Costco for $220\. Your configuration may of course differ.

3. Tested it by manually running:

  ```
  sudo /opt/local/bin/rsnapshot daily
  ```

4. Created hourly, daily, weekly, and monthly `launchd` plist files for `rsnapshot`. The hourly one runs every six hours and I threw it in just for completeness. You can download them all from [here](http://justatheory.com/downloads/rsnapshot_launchd_plists.tar.gz "Download my rsnapshot launchd plist files"). Just put them into _/Library/LaunchDaemons_ and run:

  ```
  sudo launchctl load -w /Library/LaunchDaemons/org.rsnapshot.periodic-*.plist
  ```

And that's it. Enjoy!
