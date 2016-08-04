# Setting up Master-Master Replication with MySQL on Debian 8 (Jessie)

[Original URL](https://www.howtoforge.com/tutorial/mysql-master_master-replication-on-debian-jessie/)

> This tutorial describes a replicated MySQL setup (Mater/Master replication) with 2 nodes where data can be read and written to both nodes at the same time. MySQL takes care to replicate the data to...

This tutorial describes a replicated MySQL setup (Mater/Master replication) with 2 nodes where data can be read and written to both nodes at the same time. MySQL takes care to replicate the data to the other node and ensures that primary auto increment keys don't collide.

Since version 5, MySQL comes with built-in support for master-master replication, solving the problem that can happen with self-generated keys. In former MySQL versions, the problem with master-master replication was that conflicts arose immediately if node A and node B both inserted an auto-incrementing key on the same table. The advantages of master-master replication over the traditional master-slave replication are that you don't have to modify your applications to make write accesses only to the master, and that it is easier to provide high-availability because if the master fails, you still have the other master.

## 1 Preliminary Note

In this tutorial I will show how to replicate the database 

<span class="system">exampledb</span>

 from the server 

<span class="system">server1.example.com</span>

 with the IP address 

<span class="system">192.168.1.101</span>

 to the server 

<span class="system">server2.example.com</span>

 with the IP address 

<span class="system">192.168.1.102</span>

 and vice versa. Each system is the slave of the other master and the master of the other slave at the same time. Both systems are running Debian 8; however, the configuration should apply to almost all distributions with little or no modifications.

## 2 Installing MySQL 5.5

If MySQL isn't already installed on 

<span class="system">server1</span>

 and 

<span class="system">server2</span>

, install it now:

server1/server2:

apt-get -y install mysql-server-5.5 mysql-client-5.5

To make sure that the replication can work, we must make MySQL listen on all interfaces, therefore we comment out the line 

<span class="system">bind-address = 127.0.0.1</span>

 in 

<span class="system">/etc/mysql/my.cnf</span>

:

<span class="highlight">server1/server2:</span>

nano /etc/mysql/my.cnf

```
[...]
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address = 127.0.0.1
[...]
```

Restart MySQL afterwards:

<span class="highlight">server1/server2:</span>

service mysql restart

Then check with

<span class="highlight">server1/server2:</span>

netstat -tap | grep mysql

that MySQL is really listening on all interfaces:

netstat -tap | grep mysql<br>
tcp 0 0 *:mysql *:* LISTEN 15437/mysqld<br>
server1:~#

Now we set up a replication user 

<span class="system">slave2_user</span>

 that can be used by 

<span class="system">server2</span>

 to access the MySQL database on 

<span class="system">server1.</span>

<span class="highlight">server1:</span>

Login to the MySQL shell:

mysql –defaults-file=/etc/mysql/debian.cnf

On the MySQL shell, run the following commands:

<span class="highlight">server1: </span>

GRANT REPLICATION SLAVE ON 10_misconceptions_about_neural_networks.md a_developer's_journey_into_linux_containers.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md digitaloceangodo.md document_clustering_with_python.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web--the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won't_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md keendashboards.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_jquery.md let's_write_a_web_extension.md let_your_applications_fly.md links_list.txt mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md natural_language_basics_with_textblob.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md reclaiming_a_timemachine_volume's_disk_space.md resilient_substructure_for_your_microservice.md selzplyr.md setting_up_nginx_with_docker.md silentbicycleguff.md single_page_apps_in_depth.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md tgriesserbookshelf.md the_incomplete_collection_of_nodejs_performance_tips_--_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker's_projects.md ˈtræfɪk_·_ˈtræfɪk.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_--_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md webxray.md whose_xterm_is_it_anyway.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md TO repl@'%' IDENTIFIED BY 'secretpassword';<br>
FLUSH PRIVILEGES;<br>
quit;

Replace the word "_secretpassword_" with a secure password of your choice. Now we do the last two steps again on 

<span class="system">server2</span>

:

server2:

mysql –defaults-file=/etc/mysql/debian.cnf

<span>GRANT REPLICATION SLAVE ON 10_misconceptions_about_neural_networks.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md digitaloceangodo.md document_clustering_with_python.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md keendashboards.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_jquery.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md natural_language_basics_with_textblob.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md selzplyr.md setting_up_nginx_with_docker.md silentbicycleguff.md single_page_apps_in_depth.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md tgriesserbookshelf.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md webxray.md whose_xterm_is_it_anyway.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md TO repl@’%’ IDENTIFIED BY ’</span>

<span>secretpassword</span>

<span>’;</span>

<br>
FLUSH PRIVILEGES;<br>
quit;

<span>Replace the word "</span>

_secretpassword_

<span>" with a secure password here as well. Note down the passwords as we need them later.</span>

## 3 Some Notes

In the following I will assume that both MySQL servers are empty (don't contain any database yet except of the 'mysql' database).

If that's not the case on your server, then you have to lock and dump the databases on the first server and import them on the second one before you continue. Don't unlock the databases before the replication is setup. Below a few commands that show how to copy over all databases to a new server in case you don't start with a "clean" MySQL setup.

Example on how to lock all database tables in a MySQL database.

```
FLUSH TABLES WITH READ LOCK;
SET GLOBAL read_only = ON;
```

Example on how to dump all databases into a file all_databases.sql.

mysqldump –defaults-file=/etc/mysql/debian.cnf -cCeQ –hex-blob –quote-names –routines –events –triggers –all-databases -r all_databases.sql

Example on how to import all tables on the second server from file all_databses.sql.

mysql –defaults-file=/etc/mysql/debian.cnf < all_databases.sql

## 4 Setting Up Replication

Now we set up master-master replication in 

<span class="system">/etc/mysql/my.cnf</span>

. The crucial configuration options for master-master replication are 

<span class="system">auto_increment_increment</span>

 and 

<span class="system">auto_increment_offset</span>

:

- <span class="system">auto_increment_increment</span>

   controls the increment between successive AUTO_INCREMENT values.
- <span class="system">auto_increment_offset</span>

   determines the starting point for AUTO_INCREMENT column values.

Let's assume we have N MySQL nodes (N=2 in this example), then 

<span class="system">auto_increment_increment</span>

 has the value N on all nodes, and each node must have a different value for 

<span class="system">auto_increment_offset</span>

 (1, 2, ..., N).

Now let's configure our two MySQL nodes:

server1:

nano /etc/mysql/my.cnf

Search for the section that starts with 

<span class="system">[mysqld]</span>

, and put the following options into it (commenting out all existing **conflicting** options):

```
[...]
[mysqld]

# Unique Server ID
server-id = 1# Do not replicate the following databases
binlog-ignore-db = mysql
replicate-ignore-db = mysql# Auto increment offset
auto-increment-increment = 2# Do not replicate sql queries for the local server ID
replicate-same-server-id = 0# Beginne automatisch inkrementelle Werte mit 1
auto-increment-offset = 1# Delete binlog data after 10 days
expire_logs_days = 10# Max binlog size
max_binlog_size = 500M# Binlog file path
log_bin = /var/log/mysql/mysql-bin.log[...]
```

Then restart MySQL:

<span class="highlight">server1:</span>

service mysql restart

Now do the same on 

<span class="system">server2</span>

:

server2:

nano /etc/mysql/my.cnf

```
[...]# Unique Server ID
server-id = 2# Do not replicate the following databases
binlog-ignore-db = mysql
replicate-ignore-db = mysql# Auto increment offset
auto-increment-increment = 2# Do not replicate sql queries for the local server ID
replicate-same-server-id = 0# Beginne automatisch inkrementelle Werte mit 1
auto-increment-offset = 1# Delete binlog data after 10 days
expire_logs_days = 10# Max binlog size
max_binlog_size = 500M# Binlog file path
log_bin = /var/log/mysql/mysql-bin.log[...]
```

<span class="highlight">server2:</span>

service mysql restart

Next we lock the 

<span class="system">exampledb</span>

 database on 

<span class="system">server1</span>

, find out about the master status of 

<span class="system">server1</span>

, create an SQL dump of 

<span class="system">exampledb</span>

 (that we will import into 

<span class="system">exampledb</span>

 on 

<span class="system">server2</span>

 so that both databases contain the same data), and unlock the database so that it can be used again:

server2:

Now we start the replication on Server 2\. Open the MySQL shell:

<span>mysql –defaults-file=/etc/mysql/debian.cnf</span>

<span>And execute the following SQL command to activate the replication from server1 to server2:</span>

```
CHANGE MASTER TO MASTER_HOST='192.168.1.101', MASTER_USER='repl', MASTER_PASSWORD='secretpassword';
```

Replace _secretpassword_ with the password for the _repl_ MySQL user that you have set in chapter 2.

Now check the slave status by executing the command "show slave status\G" in the MySQL shell.

show slave status\G

The output will be similar to this:

```
mysql> show slave status\G
*************************** 1\. row ***************************
 Slave_IO_State:
 Master_Host: 192.168.1.101
 Master_User: repl
 Master_Port: 3306
 Connect_Retry: 60
 Master_Log_File: mysql-bin.000001
 Read_Master_Log_Pos: 107
 Relay_Log_File: mysqld-relay-bin.000003
 Relay_Log_Pos: 253
 Relay_Master_Log_File: mysql-bin.000001
 Slave_IO_Running: No
 Slave_SQL_Running: No
 Replicate_Do_DB:
 Replicate_Ignore_DB: mysql
 Replicate_Do_Table:
 Replicate_Ignore_Table:
 Replicate_Wild_Do_Table:
 Replicate_Wild_Ignore_Table:
 Last_Errno: 0
 Last_Error:
 Skip_Counter: 0
 Exec_Master_Log_Pos: 107
 Relay_Log_Space: 410
 Until_Condition: None
 Until_Log_File:
 Until_Log_Pos: 0
 Master_SSL_Allowed: No
 Master_SSL_CA_File:
 Master_SSL_CA_Path:
 Master_SSL_Cert:
 Master_SSL_Cipher:
 Master_SSL_Key:
 Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
 Last_IO_Errno: 0
 Last_IO_Error:
 Last_SQL_Errno: 0
 Last_SQL_Error:
 Replicate_Ignore_Server_Ids:
 Master_Server_Id: 1
1 row in set (0.00 sec)
```

The Lines that you should check are these:

```
Master_Host: 192.168.1.101
Master_User: repl
Master_Port: 3306
Master_Log_File: mysql-bin.000001
Relay_Log_File: mysqld-relay-bin.000003
Slave_IO_Running: No
Slave_SQL_Running: No
```

Now start the replication with this command on the MySQL shell:

start slave;

and then check the slave status again:

<span>show slave status\G</span>

The following two lines should show "yes" now:

```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Seconds_Behind_Master: 0
```

If "Seconds_Behind_Master" is not 0, then wait a few seconds and check the status again. This field shows if master and slave are in sync.

For the next step, we need to know the values of "Master_Log_File" and "Read_Master_Log_Pos" the "

<span>show slave status\G" command. In my case these are:</span>

```
Master_Log_File: mysql-bin.000001
Read_Master_Log_Pos: 107
```

Write down the values that you get on your server, we need it for the next step on server 1.

Afterward you can leave the MySQL shell:

quit

<span class="highlight">server1:</span>

We continue on the first server, open the MySQL shell on server1:

<span>mysql –defaults-file=/etc/mysql/debian.cnf</span>

And execute the following MySQL command:

```
CHANGE MASTER TO MASTER_HOST='192.168.1.102', MASTER_USER='repl', MASTER_PASSWORD='secretpassword', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=107;
```

You have to replace a few things in the above command:

1. The IP address has to be the IP of your second MySQL server.
2. The password "secretpassword" has to be the one that you have choosen in chapter 2 for the user repl.
3. The MASTER_LOG_FILE and MASTER_LOG_POS have to be the values that we have written down in the last step.

Now check with:

<span>show slave status\G</span>

on the MySQL shell if there are no errors.

```
mysql> show slave status\G
*************************** 1\. row ***************************
 Slave_IO_State:
 Master_Host: 192.168.1.102
 Master_User: repl
 Master_Port: 3306
 Connect_Retry: 60
 Master_Log_File: mysql-bin.000001
 Read_Master_Log_Pos: 107
 Relay_Log_File: mysqld-relay-bin.000001
 Relay_Log_Pos: 4
 Relay_Master_Log_File: mysql-bin.000001
 Slave_IO_Running: No
 Slave_SQL_Running: No
 Replicate_Do_DB:
 Replicate_Ignore_DB: mysql
 Replicate_Do_Table:
 Replicate_Ignore_Table:
 Replicate_Wild_Do_Table:
 Replicate_Wild_Ignore_Table:
 Last_Errno: 0
 Last_Error:
 Skip_Counter: 0
 Exec_Master_Log_Pos: 107
 Relay_Log_Space: 107
 Until_Condition: None
 Until_Log_File:
 Until_Log_Pos: 0
 Master_SSL_Allowed: No
 Master_SSL_CA_File:
 Master_SSL_CA_Path:
 Master_SSL_Cert:
 Master_SSL_Cipher:
 Master_SSL_Key:
 Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
 Last_IO_Errno: 0
 Last_IO_Error:
 Last_SQL_Errno: 0
 Last_SQL_Error:
 Replicate_Ignore_Server_Ids:
 Master_Server_Id: 0
1 row in set (0.00 sec)
```

And start the slave.

<span>start slave;</span>

Check the slave status again:

<span>show slave status\G</span>

The following two lines should show "yes" now:

```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```

Afterward you can leave the MySQL shell:

quit

If nothing went wrong, MySQL master-master replication should now be working. If it isn't, please check 

<span class="system">/var/log/syslog</span>

 for MySQL errors on 

<span class="system">server1</span>

 and 

<span class="system">server2</span>

.

## 5 Test the Replication

Now it's time to test our replication setup. I will create a database 

<span>exampledb1 on server1 and then check on server2 if the database has been replicated to the second server:</span>

server1:

<span>Login to the MySQL console on server1 and create the database:</span>

<span>mysql –defaults-file=/etc/mysql/debian.cnf</span>

```
CREATE DATABASE exampledb1;
```

<span class="highlight">server2</span>

Now login to the MySQL console on server2 and check if exampledb1 exists there now:

<span>mysql –defaults-file=/etc/mysql/debian.cnf</span>

```
show databases;
```

As we can see, the new database shows up on server2 as well.

```
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| exampledb1 |
| mysql |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)
```

Next I'll test if the replication works in the other direction as well. We are still logged in on server2 and create there a database exampledb2:

```
CREATE DATABASE exampledb2;
```

Now go back to server1 and run "show databases" in the MySQL console:

<span class="highlight">server1</span>

```
show databases;
```

The result shows our new database exampledb2, so the replication is working in both directions.

```
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| exampledb1 |
| exampledb2 |
| mysql |
| performance_schema |
+--------------------+
5 rows in set (0.01 sec)
```

- MySQL: <http://www.mysql.com>
- Debian: [http://www.debian.org](http://www.debian.org/)

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)
