# jQuery webcam plugin

[Original URL](http://www.xarg.org/project/jquery-webcam-plugin/)

> The jQuery webcam plugin is a transparent layer to communicate with a camera directly in JavaScript. Overview This plugin provides three different modes to access a webcam through a small API...

The jQuery webcam plugin is a transparent layer to communicate with a camera directly in JavaScript.

## Overview

This plugin provides three different modes to access a webcam through a small API directly with JavaScript - or more precisely jQuery. Thus, it is possible to bring the image on a Canvas (callback mode), to store the image on the server (save mode) and to stream the live image of the Flash element on a Canvas (stream mode). If you just want to download the plugin, click here:

[Download the jQuery webcam plugin](https://github.com/infusion/jQuery-webcam)

## jQuery webcam example

![](http://www.xarg.org/image/antenna.png) 

<span>jQuery</span>

 [![](http://www.xarg.org/image/icon_filter.png)]()

[Take a picture after 3 seconds]() | [Take a picture instantly]()

### Available Cameras

If you activate the filter with the button on the right side of the picture, methods of my already published [jQuery plugin xcolor](http://www.xarg.org/project/jquery-color-plugin-xcolor/) will be used to distort the colors of the Canvas.

## General information about the interface

The following snippet describes the interface of the webcam API:

```
$("#camera").webcam({
    width: 320,
    height: 240,
    mode: "callback",
    swffile: "/download/jscam_canvas_only.swf",
    onTick: function() {},
    onSave: function() {},
    onCapture: function() {},
    debug: function() {},
    onLoad: function() {}
});
```

### Config Parameter

**width**<br>
The width of the flash movie.

**height**<br>
The height of the flash movie. Both parameters have to be changed in the Flash file as well. Follow the instructions below to recompile the swf after the size change.

**mode**<br>
The storage mode can be one of the following: _callback_, _save_, _stream_. Details about the usage of each parameter can be found under the according heading below.

**swffile**<br>
Points to the swf file of the Flash movie, which provides the webcam API. There are two swf files provided via the download archive: **jscam.swf**, which provides the full API and **jscam_canvas_only.swf** which have no embedded JPEG library (I embedded an adjusted JPGEncoder of the [AS 3 corelib](http://code.google.com/p/as3corelib/)). Thereby, the file is only one third as large as the original.

**onTick**, **onSave**, **onCapture**<br>
These callbacks are described in detail below, since they change with each mode.

**onLoad**<br>
The _onLoad_ callback is called as soon as the registration of the interface is done. In the example above, I use the callback to get a list of all cameras available:

```
onLoad: function() {

 var cams = webcam.getCameraList();
 for(var i in cams) {
 jQuery("#cams").append("<li>" + cams[i] + "</li>");
 }
}
```

Once the _onLoad_ callback is called, a global object **window.webcam** is available, which provides the following methods:

- **capture(_[delay]_)**<br>
  Captures an image internally.
- **save(_[file]_)**<br>
  Saves the captured image accordingly to the storage mode.
- **getCameraList()**<br>
  Get's an array of available cameras. If no camera is installed, an error is thrown and an empty array is returned.
- **setCamera(_[index]_)**<br>
  Switches to a different camera. The parameter is the index of the element in the resulting array of _getCameraList()_

**debug**<br>
The _debug_ callback is called whenever there is a note or an error you should be notified. In the example above, I just replace the html content of the output container:

```
debug: function (type, string) {
    $("#status").html(type + ": " + string);
}
```

## Callback Interface

The callback mode is used to get the raw data via a callback method to write it on a canvas element for example. The example above uses the callback mode.

As for the processing, one can imagine how it works as follows: Once the user has completely loaded the entire page and has accepted the security setting of Flash, she should be able to see herself. Then, the user triggers the method _window.capture()_. This may optionally receive a parameter that specifies the time to wait until the image is shot. To view the passage of time, the method _onTick()_ is called after every second. The received parameter of this method is the amount of seconds remaining. In the example above, I simply change the status message like this:

```
onTick: function(remain) {

 if (0 == remain) {
 jQuery("#status").text("Cheese!");
 } else {
 jQuery("#status").text(remain + " seconds remaining...");
 }
}
```

Is copying finished, the _onCapture_ callback is called, which in the example of above immediately calls the method _webcam.save()_ to ultimately write the image to the canvas. The sample code also contains a small gimmick to **simulate a flash** using a lightbox and jQuery's _fadeOut()_ fx method.

```
onCapture: function () {

    jQuery("#flash").css("display", "block");
    jQuery("#flash").fadeOut("fast", function () {
        jQuery("#flash").css("opacity", 1);
    });

    webcam.save();
}
```

In callback mode, for every line the callback _onSave()_ is invoked, which gets an integer CSV of color values (separator is the semicolon). To write the data on the canvas, I use the following method in the example above:

```
onSave: function(data) {

 var col = data.split(";");
 var img = image;

 for(var i = 0; i 

Save Interface
From the view of processing, the save mode is almost identical to the callback mode. The only difference is that the webcam.save() method get's the file name passed as parameter. Then the shot photo is sent via HTTP_RAW_POST_DATA to the server and can be read for example with the following snippet to store or further process it in any way (Warning, input validation is not considered here!).

webcam.save('/upload.php');
```

And on the server side, you get the image like this:

```
<?php

$str = file_get_contents("php://input");
file_put_contents("/tmp/upload.jpg", pack("H*", $str));

?>
```

### Alternative method to the upload via Flash

The Flash method has several problems. The implementation can lock the entire Flash movie and in the worst case the whole browser until the picture was uploaded sucessfully. A better approach is Ajax to upload the image asynchronously. Take a look at this example. It uploads a simple picture CSV if canvas elements are not implemented in the browser and sends a data url formatted string otherwise:

```
$(function() {

    var pos = 0, ctx = null, saveCB, image = [];

    var canvas = document.createElement("canvas");
    canvas.setAttribute('width', 320);
    canvas.setAttribute('height', 240);

    if (canvas.toDataURL) {

        ctx = canvas.getContext("2d");

        image = ctx.getImageData(0, 0, 320, 240);

        saveCB = function(data) {

            var col = data.split(";");
            var img = image;

            for(var i = 0; i < 320; i++) {
                var tmp = parseInt(col[i]);
                img.data[pos + 0] = (tmp >> 16) & 0xff;
                img.data[pos + 1] = (tmp >> 8) & 0xff;
                img.data[pos + 2] = tmp & 0xff;
                img.data[pos + 3] = 0xff;
                pos+= 4;
            }

            if (pos >= 4 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md animated_svg_vs_gif_cagematch.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md creating_fun_and_immersive_audio_experiences_with_web_audio.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md css_toggle_switch.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_html5_media_framework.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md 320 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md animated_svg_vs_gif_cagematch.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md creating_fun_and_immersive_audio_experiences_with_web_audio.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md css_toggle_switch.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_html5_media_framework.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md 240) {
                ctx.putImageData(img, 0, 0);
                $.post("/upload.php", {type: "data", image: canvas.toDataURL("image/png")});
                pos = 0;
            }
        };

    } else {

        saveCB = function(data) {
            image.push(data);

            pos+= 4 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md animated_svg_vs_gif_cagematch.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md creating_fun_and_immersive_audio_experiences_with_web_audio.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md css_toggle_switch.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_html5_media_framework.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md 320;

            if (pos >= 4 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md animated_svg_vs_gif_cagematch.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md creating_fun_and_immersive_audio_experiences_with_web_audio.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md css_toggle_switch.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_html5_media_framework.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md 320 10_misconceptions_about_neural_networks.md 10_things_to_do_after_installing_ubuntu_1510.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md altitudeloginwithssh.md alvarotrigopagepilingjs.md andrewchildsovercast.md animatecss.md animated_svg_vs_gif_cagematch.md an_introduction_to_visual_programming_using_noflo.md anyone_can_learn_to_code_an_lstmrnn_in_python_part_1_rnn.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md blog.md bndwpick.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md cmusatyalabopenface.md coding_math.md connorathertonwalkway.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md coreos.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md creating_fun_and_immersive_audio_experiences_with_web_audio.md cryptdbcryptdb.md cryptographic_module_standards_at_a_crossroads_after_snowden’s_revelations.md css_toggle_switch.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md developer_edition_44_new_visual_editing_and_memory_management_tools.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md fuzzing_with_american_fuzzy_lop.md fyeah_swift_4_features_that_changed_how_i_code_with_jacob_schwartz.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md hacking_gsm_signals_with_an_rtlsdr_and_topguw.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md how_to_use_an_xbox_360_controller_with_os_x_yosemite.md html html5_games_3d_collision_detection.md httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh http_live_streaming_in_javascript.md httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md iiseymourchromote.md imjasonhghfs.md install_sandstorm.md instant_loading_web_apps_with_an_application_shell_architecture_—_google_developers.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamesobdesk.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md kairos_by_rodrigogs.md keendashboards.md khankatex.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_golang_build_a_stock_notifier_·_kyle_redelinghuys.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt looking_at_open_source_paas_technologies.md mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md maxogdenlinux.md md metabase_documentation.md metadata_investigation__inside_hacking_team.md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md monitor_and_control_applications_using_supervisor_part_2__tuts_code_tutorial.md nanobox.md nats__cloud_native_open_source_high_performance_messaging.md natural_language_basics_with_textblob.md nightwatchjs.md nodejs_mvc_framework.md now_available_for_download_processing.md null.md offline_web_apps_on_github_pages.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh pattern_lab.md png prototype_interaction_and_animation.md psdjs.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md raathigeshsemanticedit.md rain1017memdb.md readtext.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md richardknopgooauth2server.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md simplify_your_life_with_an_ssh_config_file.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md sql_vs_nosql_you_do_want_to_have_a_relational_storage_by_default.md storejs.md teslajs.md tgriesserbookshelf.md the_html5_media_framework.md the_http2_web_server_with_automatic_https.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_trusted_source_for_javascript_packages_meteor_resources_and_tools.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy timelinemax_a_primer.md time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tridiv.md tugboat.md tutorials.md twitterfabricgalley.md twofactor_authentication_download.md unity_2d_flappy_bird_tutorial.md unixdaemon.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md watchman__a_file_watching_service.md webxray.md what_is_scholarly_html.md whose_xterm_is_it_anyway.md why_buy_an_electric_bike_if_we_can_transform_that_one_we_have.md wikiimunesexamples_–_imunes.md wkoszekflviz.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md your_pull_request_tracking_tool.md zenit.md 240) {
                $.post("/upload.php", {type: "pixel", image: image.join('|')});
                pos = 0;
            }
        };
    }

    $("#webcam").webcam({

        width: 320,
        height: 240,
        mode: "callback",
        swffile: "/download/jscam_canvas_only.swf",

        onSave: saveCB,

        onCapture: function () {
            webcam.save();
        },

        debug: function (type, string) {
            console.log(type + ": " + string);
        }
    });

});
```

The server could then do something like this:

```
<?php

if ($_POST['type'] == "pixel") {
    // input is in format 1,2,3...|1,2,3...|...
    $im = imagecreatetruecolor(320, 240);

    foreach (explode("|", $_POST['image']) as $y => $csv) {
        foreach (explode(";", $csv) as $x => $color) {
            imagesetpixel($im, $x, $y, $color);
        }
    }
} else {
    // input is in format: data:image/png;base64,...
    $im = imagecreatefrompng($_POST['image']);
}

// do something with $im

?>
```

## Stream interface

The stream mode is also quite the same procedure as the callback mode, with the difference that the _onSave_ callback is called non-stop. The streaming starts with the method _webcam.capture()_. The _webcam.save()_ method has no further effect.

## Recompile the Flash binary

If you've made changes to the code or did just adjust the size of the video in the XML specification file, you can easily recompile the swf file from Linux console with the provided Makefile. You are required to install the two open source projects **swfmill** and **mtasc** that can be easily installed using apt-get under Debian/Ubuntu:

```
apt-get install swfmill mtasc
vim src/jscam.xml
make
```

### Hint about empty screens after recompilation

There is a bug in the current version of swfmill. Please try to downgrade swfmill to **2.0.12**, which fixes the issue!