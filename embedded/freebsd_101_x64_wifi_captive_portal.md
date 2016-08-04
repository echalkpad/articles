# FreeBSD 10.1 x64 WiFi Captive Portal

[Original URL](http://www.unixmen.com/freebsd-10-1-x64-wifi-captive-portal/)

> Our goal is to build a FreeBSD server Captive Portal as it is in hotels and airports. Guest joins to wifi without entering a username or password, but when trying to join internet the user will be...

Our goal is to build a FreeBSD server Captive Portal as it is in hotels and airports. Guest joins to wifi without entering a username or password, but when trying to join internet the user will be asked for username and password. If the username and password is true, the guest will be able to use internet. In my environment I used AP ubiquiti(official site: <https://www.ubnt.com/>). It is cheaper, stable and have open source wireless controller. The network structure will be as follows:<br>
[![UNIXMEN-FR](http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/UNIXMEN-FR-1.jpg?resize=300%2C232%20300w,%20http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/UNIXMEN-FR-1.jpg?resize=768%2C594%20768w,%20http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/UNIXMEN-FR-1.jpg?w=854%20854w)](http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/UNIXMEN-FR-1.jpg)

It is assumed that you have already configured FAMP and Apache PHP is stable. Apache web server is working on a group and user named **unixmen** which is created before. (In other words, in **httpd.conf** file directives are available: **User unixmen** and **Group unixmen**)

In all Access Points IP addresses is configured as it is shown in picture and DHCP server and ROUTER, FreeBSD Server's internal network card's IP address is shown. In Apache configured **VirtualHost** and it operates under the domain name **wifi.unixmen.com**

VirtualHost's **Public_html** folder is the address of **/usr/local/www/wifi/**, and the membership and authority of folder is **unixmen**. And same as **/usr/local/etc/apache24/httpd.conf** configured file **Listen 80** and **Listen 443** defined. **/usr/local/domen/wifi.unixmen.com** virtualhos content of the file are as follows:<br>
`<VirtualHost *:80> RewriteEngine on ReWriteCond %{SERVER_PORT} !^443$ RewriteRule ^/(.*) https://%{HTTP_HOST}/$1 [NC,R,L] </VirtualHost> <VirtualHost *:443> SSLEngine on SSLCertificateFile /usr/local/etc/apache24/ssl/wifi.pem SSLCertificateKeyFile /usr/local/etc/apache24/ssl/wifi.key DocumentRoot /usr/local/www/wifi/ <Directory "/usr/local/www/wifi"> AllowOverride All Require all granted </Directory> </VirtualHost>`

It is needed to install **pear** for php and create database and its user at MySQL We install **pear** and configure for php.<br>
``# cd `whereis pear | awk '{ print $2 }'` - enter Port Path # make -DBATCH install - install``

After we copy php file and change properly the changes of the context.<br>
`# cp /usr/local/etc/php.ini-production /usr/local/etc/php.ini`

We do the changes in the file **/usr/local/etc/php.ini**:<br>
`date.timezone = 'Asia/Baku' include_path = '.:/usr/local/share/pear'`

Now lets create MySQL database and define a user for this database. And create a schedule for wifi users<br>
``mysql> CREATE database wifi; mysql> grant all privileges on wifi.* to wifidbuser@localhost identified by 'wifidbpassword'; mysql> use wifi; mysql> CREATE TABLE `users` ( `id` int(10) unsigned NOT NULL auto_increment, `username` varchar(50) default NULL, `password` varchar(50) default NULL, `created` timestamp NOT NULL default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP, `time_begin` timestamp NOT NULL default '0000-00-00 00:00:00', `time_end` timestamp NOT NULL default '0000-00-00 00:00:00', `status` tinyint(4) NOT NULL default '0', `rule_num` smallint(5) unsigned NOT NULL, PRIMARY KEY (`id`), KEY `rule_num` (`rule_num`) ) ENGINE=MyISAM AUTO_INCREMENT=6 DEFAULT CHARSET=cp1251 AUTO_INCREMENT=6 ;``

**/etc/rc.conf** the configuration file will be as follows<br>
`hostname="wifinat.unixmen.com" ifconfig_em0="inet 88.8.9.10 netmask 255.255.255.0" ifconfig_em1="inet 10.0.0.1 netmask 255.255.255.0" defaultrouter="88.8.9.1" sshd_enable="YES" dumpdev="NO"`

#### Local Disabled Services ####<br>
**sendmail_enable="NO"**<br>
**sendmail_submit_enable="NO"**<br>
**sendmail_outbound_enable="NO"**<br>
**sendmail_msp_queue_enable="NO"**<br>
**sendmail_rebuild_aliases="NO"**<br>
**syslogd_enable="YES"**<br>
**syslogd_program="/usr/sbin/syslogd"**<br>
**syslogd_flags="-ss"**

#### Local worked services ####<br>
**tcp_drop_synfin="YES"**<br>
**icmp_drop_redirects="YES"**<br>
**dhcpd_enable="YES"**<br>
**dhcpd_ifaces="em1″**<br>
**dhcpd_conf="/usr/local/etc/dhcpd.conf"**<br>
**gateway_enable="YES"**<br>
**natd_enable="YES"**<br>
**natd_interface="em0″**<br>
**firewall_enable="YES"**<br>
**firewall_type="UNKNOWN"**<br>
**firewall_script="/etc/ipfw.conf"**

#### Third Party Services ####<br>
**apache24_enable="YES"**<br>
**mysql_enable="YES"**

we add the following lines to the file **/etc/ipfw.conf** in order our configured firewall to run after reboot.<br>
`ipfw add 00200 divert 8668 ip from any to any via em0 ipfw add 10800 allow ip from any to 88.8.9.58 ipfw add 10900 allow ip from 88.8.9.58 to any ipfw add 11000 allow ip from any to 88.8.9.59 ipfw add 12000 allow ip from 88.8.9.59 to any ipfw add 60000 fwd 10.0.0.1,80 tcp from any to any dst-port 80 via em1 ipfw add 60001 fwd 10.0.0.1,443 tcp from any to any dst-port 443 via em1 ipfw add 65000 allow ip from any to any ipfw add 65535 deny ip from any to any`

Minimize system kernel and compile the following options:<br>
`options IPDIVERT options IPFIREWALL options IPFIREWALL_VERBOSE options IPFIREWALL_VERBOSE_LIMIT=3 options DUMMYNET options IPFIREWALL_FORWARD options IPFIREWALL_NAT options LIBALIAS`

Install DHCP server from ports<br>
`# cd /usr/ports/net/isc-dhcp42-server/ - enter the port address # make config - Choose the required modules # make -DBATCH install - install`

**/usr/local/etc/dhcpd.conf** we make installation file content as following:<br>
`option domain-name "wifiofis.unixmen.com"; option domain-name-servers ns1.unixmen.com, ns2.unixmen.com; default-lease-time 3600; max-lease-time 86400; ddns-update-style none; authoritative; subnet 10.0.0.0 netmask 255.255.255.0 { range 10.0.0.50 10.0.0.254; option routers 10.0.0.1; } # we reserve Access Points for the IP addresses shown in the picture host Wifi-1f.1 { hardware ethernet 04:18:76:8c:9a:a3; fixed-address 10.0.0.10; } host Wifi-2f.1 { hardware ethernet 04:18:66:43:11:11; fixed-address 10.0.0.20; } host Wifi-3f.1 { hardware ethernet 04:18:36:68:a9:9b; fixed-address 10.0.0.30;`

we create a journal file for DHCP and filter from Syslog(**/var/db/dhcpd/dhcpd.leases** (you can look in this file)<br>
`# touch /var/log/dhcp.log`

We add the follofing lines to the end of **/etc/syslog.conf** file:<br>
`!dhcpd *.* /var/log/dhcp.log`

We run the DHCP and check listening:<br>
`# /usr/local/etc/rc.d/isc-dhcpd start # sockstat -l|grep dhcp dhcpd dhcpd 4695 7 udp4 *:67 *:* dhcpd dhcpd 4695 20 udp4 *:8997 *:*`

Install Sudo from the ports:<br>
``# cd `whereis sudo | awk '{ print $2 }'` - change directory to the port # make config - choose required modules # make -DBATCH install - install``

Add to **/usr/local/etc/sudoers** file the following lines(it is required for access of web server to firewall):<br>
`unixmen ALL=(ALL) NOPASSWD: SETENV: ALL`

**/usr/local/www/wifi/config.php** the content of configuration file will be as following lines(the registration and stop registered users is fulfill by adding or removing the rules of IPFW):<br>
`# cat /usr/local/www/wifi/config.php <?php define('DEBUG', true); define('conf_DB_HOST', 'localhost'); //Database IP define('conf_DB_USER', 'wifidbuser'); //username of database define('conf_DB_PASS', 'wifidbpassword'); //the password of database define('conf_DB_NAME', 'wifi'); //the name of database define('RULE_NUM_MIN', 400); //IPFW rules begin from 400 define('RULE_NUM_MAX', 600); //IPFW rule finishs with 600 define('CLIENTS_IP_BEGIN', '10.0.0.50'); // the clients will start IP adress from define('CLIENTS_IP_COUNT', '200'); define('CLIENTS_TIME', '3600'); //the period of internet access for users(an hour) define('RULE_ADD_IP', 'sudo ipfw add %s allow ip from any to %s'); define('RULE_ADD_IP2', 'sudo ipfw add %s allow ip from %s to any'); define('RULE_DEL_IP', 'sudo ipfw del %s'); define('RULE_DEL_IP2', 'sudo ipfw del %s'); /* STATUS: 0 – If the information of connection is true you are connected, otherwise the rule is not added! 1 - You have already connected 2 – The username has been already used. 3 – The user is stopped. */ $db_link = mysql_connect(conf_DB_HOST, conf_DB_USER, conf_DB_PASS); if (!$db_link) return cms_errors('The connection to database is failed!); if (!mysql_select_db(conf_DB_NAME, $db_link)) return cms_errors('The connection to database is failed!!!'); function cms_errors($text) { if (DEBUG) echo $text; return false; } function dumpVarX(&$Var, $Var_s = null) { echo "<div align='left' class='debug'>"; dumpVar($Var, 0, $Var_s); echo "<div>"; return true; } function dumpVar(&$Var, $Level = 0, $Var_s = null) { if ($Level > 4) { echo "<b>...</b> LEVEL > 4<br>\n"; return; } $is_ob_ar = false; $Type = gettype($Var); if (is_array($Var)) {$is_ob_ar = true; $Type = "Array[".count($Var)."]";} if (is_object($Var)) $is_ob_ar = true; if ($Level == 0) { if ($Var_s) echo "\n<br>\n<b><span style=\"color:#ff0000\">".$Var_s." = {</span></b>"; if ($is_ob_ar && count($Var)) echo "<pre>\n"; else echo "\n<tt>"; $Level_zero = 0; } if ($is_ob_ar) { echo "<span style=\"color:#05a209\">$Type</span>\n"; for (Reset($Var), $Level++; list($k, $v)=each($Var);) { if (is_array($v) && $k==="GLOBALS") continue; for ($i=0; $i<$Level*3; $i++) echo " "; echo "<b>".HtmlSpecialChars($k)."</b> => "; dumpVar($v, $Level); } } else { if (is_string($Var) && strlen($Var)>400) echo '('.$Type.') <span style="color:#35BBFA">strlen = '.strlen($Var).'</span>'."\n"; else echo '('.$Type.') "<span style="color:#0000FF">',HtmlSpecialChars($Var),'</span>"'."\n"; } if (isset($Level_zero)) { if ($is_ob_ar && count($Var)) echo "</pre>"; else echo "</tt>"; if ($Var_s) echo "<b><span style=\"color:#ff0000\">}</span></b><br>\n"; } return true; } ?>`

User's registration script: **/usr/local/www/wifi/add.php** file will be as follows:<br>
`# cat /usr/local/www/wifi/add.php <?php require_once('config.php'); $user = get_user($_GET['login'], $_GET['pass']); if ($user) { switch ($user['status']) { case 0: if (add_rule($user)) echo '<h2>You are connected!</h2>'; else echo 'The wrong rule is not added!'; break; case 1: echo '<h2>You have already connected</h2>'; break; case 2: echo '<h2>Username has already been used</h2>'; break; case 2: echo '<h2>Username is stopped</h2>'; break; default: echo 'Error'; break; } } else echo '<h2>Username or password is wrong</h2>';`

// Registration<br>
**function get_user($login, $pass)**<br>
**{**<br> 
**$user = null;**<br> 
**if (!$login || !$pass) return null;**<br> 
**$login = addslashes($login);**<br> 
**$sql = 'SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM users WHERE username="'.$login.'" AND password="'.$pass.'" LIMIT 1';**<br> 
**$res = mysql_query($sql);**<br> 
**if ($res) $user = mysql_fetch_assoc($res);**<br> 
**return $user;**<br>
**}**

// Adding the rule<br>
**function add_rule($user)**<br>
**{**<br> 
**$user_ip = $_SERVER['REMOTE_ADDR'];**<br> 
**$current_date = time();**<br> 
**if (!checkip($user_ip)) return false;**<br> 
**$temp = 0;**<br> 
**$sql = 'SELECT rule_num FROM users WHERE status=1 ORDER BY rule_num';**<br> 
**$res = mysql_query($sql);**<br> 
**if ($res)**<br> 
**{**<br> 
**$t = mysql_fetch_array($res);**<br> 
**if (!$t) $rule_num = RULE_NUM_MIN;**<br> 
**else {**<br> 
**while ($temp = mysql_fetch_array($res))**<br> 
**{**<br> 
**if (($t[0]+1) < $temp[0]) break;**<br> 
**$t = $temp;**<br> 
**}**<br> 
**if ($t[0] < RULE_NUM_MAX) $rule_num = $t[0]+1; else return false;**<br> 
**}**<br> 
**} else return false;**<br> 
**$command = sprintf(RULE_ADD_IP, $rule_num, $user_ip);**<br> 
**exec($command);**<br> 
**$command2 = sprintf(RULE_ADD_IP2, $rule_num+100, $user_ip);**<br> 
**exec($command2);**<br> 
**$sql = 'UPDATE users SET status=1, time_begin=NOW(), rule_num='.$rule_num.' WHERE id='.$user['id'];**<br> 
**mysql_query($sql);**<br> 
**return true;**<br>
**}**<br>
**function checkip($ip)**<br>
**{**<br> 
**if (!$ip) return false;**<br> 
**$user_ip = explode('.', $ip);**<br> 
**$check_ip = explode('.', CLIENTS_IP_BEGIN);**<br> 
**if (($check_ip[0] != $user_ip[0]) && $check_ip[0] != "*") return false;**<br> 
**if (($check_ip[1] != $user_ip[1]) && $check_ip[1] != "*") return false;**<br> 
**if (($check_ip[2] != $user_ip[2]) && $check_ip[2] != "*") return false;**<br> 
**if (!(($check_ip[3] <= $user_ip[3] && ($check_ip[3] + CLIENTS_IP_COUNT) >= $user_ip[3])) && $check_ip[3] != "*") return false;**<br> 
**return true;**<br>
**}**<br>
**?>**

User's closed script according to the end of time **/usr/local/www/wifi/cron.php**:<br>
`# cat /usr/local/www/wifi/cron.php <?php require_once('config.php'); check_users(); function check_users() { $sql = 'SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM users WHERE status=1 AND time_begin > 0 AND (TIME_TO_SEC(TIMEDIFF(NOW(), time_begin)) > '.CLIENTS_TIME.')'; $res = mysql_query($sql); if ($res) { while ($user = mysql_fetch_assoc($res)) { $command = sprintf(RULE_DEL_IP, $user['rule_num']); exec($command); $command2 = sprintf(RULE_DEL_IP2, $user['rule_num']+100); exec($command2); $sql = 'UPDATE users SET status=2, time_end=NOW() WHERE id='.$user['id']; mysql_query($sql); } } return true; } ?>`

The form for adding username and pasword (such as **index.html** file):<br>
`# cat /usr/local/www/wifi/index.html <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en"> <head> <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /> <title>Administration</title> <link rel="stylesheet" type="text/css" href="admin.css" /> <!--[if lt IE 7]><link rel="stylesheet" type="text/css" href="style-ie.css" /><![endif]--> </head> <body> <div class="login"> <div class="form"> <form method="get" action="add.php"> <p><label>Login:</label><input class="text" name="login" type="text" size="17"/></p> <p><label>Password:</label><input class="text" name="pass" type="password" size="16"/></p> <p><input class="submit" type="submit" value="Everything is OK!"/> </form> </div> <div class="rules"> <h1>Wi-Fi how to use</h1> <ol> <li>It is free for guests!</li> <li>Come to reception</li> <li>Get username and password</li> <li>Use wifi</li> </ol> </div> </div> </body> </html>`

Administration panel will be in **/usr/local/www/wifi/admin** folder. We must protect this folder with **htpasswd** for security. The content of file **/usr/local/www/wifi/admin/admin.php** will be as follows:<br>
`<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> <html> <head> <title>Administration panel</title> <link type="text/css" rel="stylesheet" href="style.css"> </head> <body> <form method="post" action="admin.php"> The amount of users: <input type="text" value="" name="num" size=2> count.<br><br> <input type="submit" value="Generate"> </form><hr> <?php require_once('/usr/local/www/wifi/config.php'); $n = (int) $_POST['num']; if ($n > 10) { echo 'The number of users that can be created is over limited! <br><br>'; $n=0; } function generate_password($number=10) { $arr = array('1','2','3','4','5','6', '7','8','9','0'); // Generate the password $pass = ""; for($i = 0; $i < $number; $i++) { // Calculate random index of massive $index = rand(0, count($arr) - 1); $pass .= $arr[$index]; } return $pass; } for ($i=0; $i<$n; $i++) { $login = generate_password(4); $pass = generate_password(6); $sql = 'INSERT INTO users (username, password, status, rule_num) VALUES("apt'.$login.'", "'.$pass.'", 0, 0)'; $res = mysql_query($sql); } if ($res) echo 'Count of users <b>'.$n.'</b> is added.<br><br>'; $sql = 'SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM users'; $res = mysql_query($sql); echo '<table width=\'30%\'><td><b>Username</b></td><td><b>Password</b></td><td><b>Status</b></td><td><b>Rule</b></td>'; while ($data = mysql_fetch_assoc($res)) { echo '<tr>'; echo '<td>'.$data['username'].'</td>'; echo '<td>'.$data['password'].'</td>'; if ($data['status'] == 0) { echo '<td class=\'blue\'>Is not active</td>'; } if ($data['status'] == 1) { echo '<td class=\'green\'>Used</td>'; echo '<td>'.$data['rule_num'].'</td>';} if ($data['status'] == 2) { echo '<td class=\'reds\'>Was used</td>'; } if ($data['status'] == 3) { echo '<td><b>Was stopped</b></td>'; } echo '</tr>'; } echo '</table>'; ?> </body> </html>`

`/usr/local/www/wifi/admin/style.css content of the file will be as follows: .reds {color:#f30;} .blue {color:#0000cc;} .green {color:#0f0;}`

In the folder **/usr/local/www/wifi** we will create file named **.htaccess** and add the following lines.<br>
`AuthUserFile /usr/local/www/wifi/.htpasswd AuthName "Soft Admin" AuthType Basic Require valid-user`

We create user name and password in **/usr/local/www/wifi** folder:<br>
`htpasswd -bc .htpasswd admin freebsd` – Write **admin** username and **freebsd** password in **.htpasswd** file<br>
**-b** take username and password from the command line.<br>
**-c** –Create the file which is shown and add to it(if it exists,it will remove and rewrite)

To check users limits we will add cron file our global **/etc/crontab** configuration file. This will check users each 1 minutes interval.<br>
`# echo "*/1 * * * * root /usr/local/bin/php /usr/local/www/wifi/cron.php" >> /etc/crontab # /etc/rc.d/cron restart`

**/usr/local/www/wifi/admin.css** file's content will be as follows(This file set the pictures which are in background). But the pictures is read from **/usr/local/www/wifi/img/** folder. Pictures are **/usr/local/www/wifi/img/gp.gif** and **/usr/local/www/wifi/img/logo.png** (You can change these pictures to anywhere you want):<br>
`.login {width:800px; height:540px; position:absolute; left:50%; top:50%; margin:-250px 0 0 -400px; border:dashed 1px #ddd; background:url(img/logo.png) 30px 30px no-repeat #fff;} .login .form {margin:120px 0 0 450px;} .login .form p {position:relative; margin:0 0 30px 0;} .login .form label {font:normal 18px arial; position:absolute; margin:3px 0 0 0; color:#aaa;} .login .form input {margin:0 0 0 100px; padding:2px; font:normal 18px arial;} .login .form input.text {border-right:solid 1px #ccc; border-bottom:solid 1px #ccc; border-left:solid 1px #888; border-top:solid 1px #888;} .login .rules {padding:10px 20px; margin:50px 30px; background:url(img/gp.gif) 420px 20px no-repeat #ececec;} h1 {margin:10px 0; font:normal 20px tahoma; color:#c00;} ol {margin:20px 0 0 30px; padding:0;} ol li {margin:0 0 10px 15px; font: normal 16px arial; }`

We update all authorites in the folder, in order to set to all new files:<br>
`# chown -R unixmen:unixmen /usr/local/www/wifi/`

As we enter the page We will be asked for username and password(we type **admin** username and password that we created before and then push enter):<br>
[![Admin-panel first page](http://i2.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-panel-first-page-1.jpg?resize=300%2C146%20300w,%20http://i2.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-panel-first-page-1.jpg?resize=768%2C373%20768w,%20http://i2.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-panel-first-page-1.jpg?w=834%20834w)](http://i2.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-panel-first-page-1.jpg)

The administrator interface will be as follows:<br>
[![Admin-Panel](http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-Panel-1.jpg?resize=300%2C256%20300w,%20http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-Panel-1.jpg?w=593%20593w)](http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Admin-Panel-1.jpg)

The user interface will be so:<br>
[![Wifi-captive-portal](http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Wifi-captive-portal-1.jpg?resize=300%2C206%20300w,%20http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Wifi-captive-portal-1.jpg?resize=768%2C527%20768w,%20http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Wifi-captive-portal-1.jpg?w=829%20829w)](http://i0.wp.com/www.unixmen.com/wp-content/uploads/2016/01/Wifi-captive-portal-1.jpg)

The source of used PHP codes is from **<http://lissyara.su>**<br>
You can download source codes from [here](http://www.unixmen.com/wp-content/uploads/2016/01/php-codes.zip)

## _Related_
