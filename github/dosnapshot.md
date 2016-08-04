# DoSnapshot

[Original URL](http://dosnapshot.merqlove.ru/)

> Use this tool to backup DigitalOcean droplet's vi

[![Gem Version](https://badge.fury.io/rb/do_snapshot.svg)](http://badge.fury.io/rb/do_snapshot) [![Build Status](https://travis-ci.org/merqlove/do_snapshot.svg?branch=master)](https://travis-ci.org/merqlove/do_snapshot) [![Dependency Status](https://gemnasium.com/merqlove/do_snapshot.svg)](https://gemnasium.com/merqlove/do_snapshot) [![Coverage Status](https://coveralls.io/repos/merqlove/do_snapshot/badge.png?branch=master)](https://coveralls.io/r/merqlove/do_snapshot?branch=master) [![Inline docs](http://inch-ci.org/github/merqlove/do_snapshot.png?branch=master)](http://inch-ci.org/github/merqlove/do_snapshot) [![Code Climate](https://codeclimate.com/github/merqlove/do_snapshot.png)](https://codeclimate.com/github/merqlove/do_snapshot)

Use this tool to backup DigitalOcean droplet's via snapshot method, on the fly!

Here some features:

- Multiple threads out of the box. No matter how much droplet's you have.
- Snapshots Auto-Cleanup.
- Auto-Boot Droplet back if Snapshot Event is failed or bad connection exception.
- Binary special for cron and command-line. Homebrew, Standalone installers.
- Mail notifications when fail or maximum of snapshots is reached for one or multiple droplets.
- Custom mail settings (You can set [Pony](https://github.com/benprew/pony) mail settings).
- Stop mode (when you don't want to create new snapshots when maximum is reached).
- Timeout option for long requests or uncaught loops. By default it 600 seconds, but you can change it by hand.
- Logging into selected directory.
- Verbose mode for research.
- Quiet mode for silence.

## [](http://dosnapshot.merqlove.ru/#compatibility)Compatibility

Ruby versions: 1.9.3 and higher. JRuby in 1.9 mode is also supported.

![DoSnaphot example](https://raw.githubusercontent.com/merqlove/do_snapshot/master/assets/example.png)

### [](http://dosnapshot.merqlove.ru/#you-can-ask-me-why-you-made-this-tool)You can ask me, "Why you made this tool?"

- First. I needed stable tool, which can provide for me automatic Snapshot feature for all of my Droplets via Cron planner.
- I don't want to think how much snapshots for each droplet i have.
- I don't wont to sleep when my droplets Offline!!! And i wanted tool which can BOOT back droplets, which failed to snapshot.
- Also i want to understand what's going on if there some error. Mail is my choice. But logs also good.
- And ... sure ;) We want to do it fast as rocket! :)
- more more more...
- So this tool can save a lot of time for people.

## [](http://dosnapshot.merqlove.ru/#installation)Installation

Install it yourself as:

```
$ gem install do_snapshot
```

For **OSX** users ([Homebrew Tap](http://github.com/merqlove/homebrew-do-snapshot)):

```
$ brew tap merqlove/do-snapshot && brew install do_snapshot

$ do_snapshot -V
```

Standalone pack for **Unix/Linux** users: [Download](https://assets.merqlove.ru.s3.amazonaws.com/do_snapshot/do_snapshot.tgz)

```
$ wget https://assets.merqlove.ru.s3.amazonaws.com/do_snapshot/do_snapshot.tgz # if not done.

# Example Install into /usr/local

$ tar -xzf do_snapshot.tgz /usr/local/ && ln -s /usr/local/do_snapshot/bin/do_snapshot /usr/local/bin/do_snapshot 
$ do_snapshot help 
```

Standalone Zip pack for others: [Download](https://assets.merqlove.ru.s3.amazonaws.com/do_snapshot/do_snapshot.zip)

Or add this line to Gemfile:

```
gem 'do_snapshot'
```

And then execute:

```
$ bundle
```

## [](http://dosnapshot.merqlove.ru/#usage)Usage

First you may need to set DigitalOcean API keys:

```
$ export DIGITAL_OCEAN_CLIENT_ID="SOMEID"
$ export DIGITAL_OCEAN_API_KEY="SOMEKEY"
```

If you want to set keys without environment, than set it via options when you run do_snapshot:

```
$ do_snapshot --digital-ocean-client-id YOURLONGAPICLIENTID --digital-ocean-api-key YOURLONGAPIKEY
```

### [](http://dosnapshot.merqlove.ru/#how-to)How-To

Here we `keeping` only 5 **latest** snapshots and cleanup older after new one is created. If creation of snapshots failed no one will be deleted. By default we keeping `10` droplets.

```
$ do_snapshot --keep 5 -c
```

Keep latest 3 from selected droplets:

```
$ do_snapshot --only 123456 1234567 --keep 3
```

Working with all except selected droplets:

```
$ do_snapshot --exclude 123456 123457
```

Keep latest 5 snapshots, send mail notification instead of creating new one:

```
$ do_snapshot --keep 10 --stop --mail to:yourmail@example.com
```

![DoSnapshot Safe Mode Example](https://raw.githubusercontent.com/merqlove/do_snapshot/master/assets/safe_mode.png)

E-mail notifications disabled out of the box. For working mailer you need to set e-mail settings via run options.

```
--mail to:mail@somehost.com from:from@host.com --smtp address:smtp.gmail.com port:25 user_name:someuser password:somepassword
```

### [](http://dosnapshot.merqlove.ru/#cron-example)Cron example

```
0 4 10_misconceptions_about_neural_networks.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md binaryjs.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md cryptdbcryptdb.md cyrusandgdbdashboard.md data data.json develop_command_line_application_using_nodejs.md digitaloceangodo.md dosa_by_shon.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flowchartjs.md free_ssltls_certificates.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md hacker_codex.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md introduction.md introduction_to_monte_carlo_tree_search.md jamiehdigitaloceancsharp.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md jssequencediagrams.md just_a_theory.md koalalorenzopythondigitalocean.md let_your_applications_fly.md links_list.txt md micc83edittable.md mistiomistio.md null.md opsing_with_packer_and_terraform.md parse_urls_json.sh png reclaiming_a_timemachine_volume’s_disk_space.md setting_up_nginx_with_docker.md silentbicycleguff.md smdahlenvagrantdigitalocean.md sqljs.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md zenit.md 10_misconceptions_about_neural_networks.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md binaryjs.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md cryptdbcryptdb.md cyrusandgdbdashboard.md data data.json develop_command_line_application_using_nodejs.md digitaloceangodo.md dosa_by_shon.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flowchartjs.md free_ssltls_certificates.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md hacker_codex.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md introduction.md introduction_to_monte_carlo_tree_search.md jamiehdigitaloceancsharp.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md jssequencediagrams.md just_a_theory.md koalalorenzopythondigitalocean.md let_your_applications_fly.md links_list.txt md micc83edittable.md mistiomistio.md null.md opsing_with_packer_and_terraform.md parse_urls_json.sh png reclaiming_a_timemachine_volume’s_disk_space.md setting_up_nginx_with_docker.md silentbicycleguff.md smdahlenvagrantdigitalocean.md sqljs.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md zenit.md 7 . /.../.digitalocean_keys && /.../bin/do_snapshot -k 5 -m to:TO from:FROM -t address:HOST user_name:LOGIN password:PASSWORD port:2525 -q -c
```

### [](http://dosnapshot.merqlove.ru/#real-world-example)Real world example

```
$ bin/do_snapshot --only 123456 -k 3 -c -m to:TO from:FROM -t address:HOST user_name:LOGIN password:PASSWORD port:2525 -v

Checking DigitalOcean Id's.
Start performing operations
Setting DigitalOcean Id's.
Loading list of DigitalOcean droplets
Working with list of DigitalOcean droplets
Preparing droplet id: 123456 name: mrcr.ru to take snapshot.
Shutting down droplet.
Start creating snapshot for droplet id: 123456 name: mrcr.ru.
Wait until snapshot will be created.
Snapshot name: mrcr.ru_2014_07_18 created successfully.
Droplet id: 123456 name: mrcr.ru snapshots: 4.
For droplet with id: 123456 and name: mrcr.ru the maximum number 3 of snapshots is reached.
Cleaning up snapshots for droplet id: 123456 name: mrcr.ru.
Snapshot name: mrcr.ru_2014_07_17 delete requested.
All operations has been finished.
Sending e-mail notification.
```

### [](http://dosnapshot.merqlove.ru/#all-options)All options:

```
> $ do_snapshot c 

aliases: s, snap, create

Options:
 -o, [--only=123456 123456 123456] # Select some droplets.
 -e, [--exclude=123456 123456 123456] # Except some droplets.
 -k, [--keep=5] # How much snapshots you want to keep?
 # Default: 10
 -d, [--delay=5] # Delay between snapshot operation status requests.
 # Default: 10 
 [--timeout=250] # Timeout in sec's for events like Power Off or Create Snapshot.
 # Default: 600 
 -m, [--mail=to:yourmail@example.com] # Receive mail if fail or maximum is reached.
 -t, [--smtp=user_name:yourmail@example.com password:password] # SMTP options.
 -l, [--log=/Users/someone/.do_snapshot/main.log] # Log file path. By default logging is disabled.
 -c, [--clean], [--no-clean] # Cleanup snapshots after create. If you have more images than you want to `keep`, older will be deleted.
 -s, [--stop], [--no-stop] # Stop creating snapshots if maximum is reached.
 -v, [--trace], [--no-trace] # Verbose mode.
 -q, [--quiet], [--no-quiet] # Quiet mode. If don't need any messages in console.
 [--digital-ocean-client-id=YOURLONGAPICLIENTID] # DIGITAL_OCEAN_CLIENT_ID. if you can't use environment.
 [--digital-ocean-api-key=YOURLONGAPIKEY] # DIGITAL_OCEAN_API_KEY. if you can't use environment. 

Description:
 `do_snapshot` able to create and cleanup snapshots on your droplets.

 You can optionally specify parameters to select or exclude some droplets. 
```

## [](http://dosnapshot.merqlove.ru/#dependencies)Dependencies:

- [Thor](https://github.com/erikhuda/thor) for CLI.
- [Digitalocean](https://github.com/scottmotte/digitalocean) for API requests.
- [Pony](https://github.com/benprew/pony) for mail notifications.

## [](http://dosnapshot.merqlove.ru/#contributing)Contributing

1. Fork it ( <https://github.com/merqlove/do_snapshot/fork> )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
