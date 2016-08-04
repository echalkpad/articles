# Simplify Your Life With an SSH Config File

[Original URL](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/)

> If you're anything like me, you probably log in and out of a half dozen remote servers (or these days, local virtual machines) on a daily basis. And if you're even more like me, you have trouble...

If you're anything like me, you probably log in and out of a half dozen remote servers (or these days, local virtual machines) on a daily basis. And if you're even _more_ like me, you have trouble remembering all of the various usernames, remote addresses and command line options for things like specifying a non-standard connection port or forwarding local ports to the remote machine.

## Shell Aliases

Let's say that you have a remote server named `dev.example.com`, which has _not_ been set up with public/private keys for password-less logins. The username to the remote account is _fooey_, and to reduce the number of scripted login attempts, you've decided to change the default SSH port to `2200` from the normal default of `22`. This means that a typical command would look like:

```
$ ssh fooey@dev.example.com -p 22000
password: *************
```

Not too bad.

We can make things simpler and more secure by using a public/private key pair; I highly recommend using [ssh-copy-id](http://linux.die.net/man/1/ssh-copy-id) for moving your public keys around. It will save you quite a few folder/file permission headaches.

```
$ ssh fooey@dev.example.com -p 22000
# Assuming your keys are properly setup…
```

Now this doesn't seem all that bad. To cut down on the verbosity you could create a simple alias in your shell as well:

```
$ alias dev='ssh fooey@dev.example.com -p 22000'
$ dev # To connect
```

This works surprisingly well: Every new server you need to connect to, just add an alias to your `.bashrc` (or `.zshrc` if you hang with the cool kids), and voilà.

## ~/.ssh/config

However, there's a much more elegant and flexible solution to this problem. Enter the SSH config file:

```
# contents of $HOME/.ssh/config
Host dev
 HostName dev.example.com
 Port 22000
 User fooey
```

This means that I can simply `$ ssh dev`, and the options will be read from the configuration file. Easy peasy. Let's see what else we can do with just a few simple configuration directives.

Personally, I use quite a few public/private keypairs for the various servers and services that I use, to ensure that in the event of having one of my keys compromised the damage is as restricted as possible. For example, I have a key that I use uniquely for my [Github](https://github.com/jperras) account. Let's set it up so that that particular private key is used for all my github-related operations:

```
Host dev
 HostName dev.example.com
 Port 22000
 User fooey
Host github.com
 IdentityFile ~/.ssh/github.key
```

The use of `IdentityFile` allows me to specify exactly which private key I wish to use for authentification with the given host. You can, of course, simply specify this as a command line option for "normal" connections:

```
$ ssh -i ~/.ssh/blah.key username@host.com
```

but the use of a config file with `IdentityFile` is [pretty much your only option](https://git.wiki.kernel.org/index.php/GitTips#How_to_pass_ssh_options_in_git.3F) if you want to specify which identity to use for any git commands. This also opens up the very interesting concept of further segmenting your github keys on something like a per-project or per-organization basis:

```
Host github-project1
 User git
 HostName github.com
 IdentityFile ~/.ssh/github.project1.key</p>
Host github-org
 User git
 HostName github.com
 IdentityFile ~/.ssh/github.org.key</p>
Host github.com
 User git
 IdentityFile ~/.ssh/github.key
```

Which means that if I want to clone a repository using my organization credentials, I would use the following:

```
$ git clone git@github-org:orgname/some_repository.git
```

## Going further

As any security-conscious developer would do, I set up firewalls on all of my servers and make them as restrictive as possible; in many cases, this means that the only ports that I leave open are `80/443` (for webservers), and port `22` for SSH (or whatever I might have remapped it to for obfuscation purposes). On the surface, this seems to prevent me from using things like a desktop MySQL GUI client, which expect port `3306` to be open and accessible on the remote server in question. The informed reader will note, however, that a simple local port forward can save you:

```
$ ssh -f -N -L 9906:127.0.0.1:3306 coolio@database.example.com
# -f puts ssh in background
# -N makes it not execute a remote command
```

This will forward all local port `9906` traffic to port `3306` on the remote `dev.example.com` server, letting me point my desktop GUI to localhost (`127.0.0.1:9906`) and have it behave exactly as if I had exposed port `3306` on the remote server and connected directly to it.

Now I don't know about you, but remembering that sequence of flags and options for [SSH](http://linux.die.net/man/1/ssh) can be a complete pain. Luckily, our config file can help alleviate that:

```
Host tunnel
 HostName database.example.com
 IdentityFile ~/.ssh/coolio.example.key
 LocalForward 9906 127.0.0.1:3306
 User coolio
```

Which means I can simply do:

```
$ ssh -f -N tunnel
```

And my local port forwarding will be enabled using all of the configuration directives I set up for the tunnel host. Slick.

## Homework

There are quite a few configuration options that you can specify in `~/.ssh/config`, and I highly suggest consulting the online [documentation](http://linux.die.net/man/5/ssh_config) or the **ssh_config** man page. Some interesting/useful things that you can do include: change the default number of connection attempts, specify local environment variables to be passed to the remote server upon connection, and even the use of 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer's_journey_into_linux_containers.md aerofsgockerize.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden's_revelations.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web--the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won't_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let's_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md reclaiming_a_timemachine_volume's_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md teslajs.md tgriesserbookshelf.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_--_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker's_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_--_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md and ? wildcards for matching hosts.

I hope that some of this is useful to a few of you. Leave a note in the comments if you have any cool tricks for the SSH config file; I'm always on the lookout for fun hacks.

Share this --

[Twitter](http://twitter.com/share?text=Simplify%20Your%20Life%20With%20an%20SSH%20Config%20File&url=http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/&hashtags=nerderati&via=jperras) [Facebook](https://www.facebook.com/sharer/sharer.php?u=http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/)
