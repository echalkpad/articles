# Learning Golang: Build a Stock Notifier · Kyle Redelinghuys

[Original URL](http://blog.ksred.me/post/learning-golang-stock-notifier/)

> I've been wanting to learn Golang for some while. I own the amazing, and very technical, Programming in Go and have been making my way through it over time. I've found though that...

I've been wanting to learn Golang for some while. I own the amazing, and very technical, [Programming in Go](http://www.amazon.com/gp/product/B007Y6KDTG?psc=1&redirect=true&ref_=oh_aui_search_detailpage) and have been making my way through it over time. I've found though that learning a language is much easier when you have a clear project you want to build out. I took a [short course on Udemy](https://www.udemy.com/learn-go-the-programming-language-by-google/learn/) to give me a primer on the basics of Go and then was ready to build.

A non-trivial application I have been wanting to build for some time is a [stock notifier](https://github.com/ksred/go-stock-notifier/).

## The easy problem

I trade shares and want to keep on top of them throughout the day without manually having to check online. I want to be sent an email peridiocally, at intervals of my choosing, of a list of stocks I have chosen ordered by biggest gainers to biggest losers.

## The slightly tougher problem

I want to be notified of daily trends in stocks. The trend is simple: every increasing or decreasing closing price over three days, and an increase in volume in one of the three days. I want this email to be sent to me before open or after close.

After doing some research around data sources, I felt that a JSON API with basic information would more than suffice. Google provided some great data across all of the stocks I was interested in on the Johannesburg Stock Exchange, so I chose their officially defunct finance API. Althought it is no longer advertised, it is still available for use. One day it will be shut off, so I have made the program to be able to use a variety of APIs, with the ability to switch on the source. The idea would be for the JSON to be parsed and cleaned according to the source, and for it to be stored in a suitable variable for use later.

Go uses structs as storage for specific variables. I wanted to make the config for the site as easy as possible, so I decided to use a JSON config file that would get read on program start up, and used throughout the lifecycle. The config file has the following format:

```
{
 "MailSMTPServer" : "mail.server.com",
 "MailSMTPPort" : "587",
 "MailUser" : "user@mail.com",
 "MailPass" : "plaintext_password",
 "MailRecipient" : "recipient@mail.com",
 "MailSender" : "sender@mail.com",
 "Symbols" : ["NASDAQ:GOOGL", "NYSE:BLK"] //Format: exhange:stock
 "UpdateInterval" : "100", // in seconds
 "TimeZone" : "America/New_York",
 "MySQLUser" : "mysql_user",
 "MySQLPass" : "mysql_password",
 "MySQLHost" : "mysql_ip",
 "MySQLPort" : "mysql_port" 
 "MySQLDB" : "mysql_database" 
}
```

Which is read into the the following matching struct:

```
type Configuration struct {
 MailUser string
 MailPass string
 MailSMTPServer string
 MailSMTPPort string
 MailRecipient string
 MailSender string
 Symbols []string
 UpdateInterval string
 TimeZone string
 MySQLUser string
 MySQLPass string
 MySQLHost string
 MySQLPort string
 MySQLDB string
}
```

The symbols are a slice (slices in Go are equatable to arrays in other languages) and can be looped through. Go uses some pretty powerful JSON parsing built in, which maps JSON data to a struct. As such, each field in the struct has a matching field in the JSON. The config is then loaded:

```
configuration := Configuration{}
loadConfig(&configuration)

...

func loadConfig(configuration *Configuration) {
 // Get config
 file, _ := os.Open("config.json")
 decoder := json.NewDecoder(file)
 err := decoder.Decode(&configuration)
 if err != nil {
 fmt.Println("error:", err)
}
```

As every stock has certain known fields, these would be parsed into the struct objects and used as these variables for saving and data analysis. The structs were based on the Google API return values:

```
type Stock struct {
 Symbol string `json:"t"`
 Exchange string `json:"e"`
 Name string `json:"name"`
 Change string `json:"c"`
 Close string `json:"l"`
 PercentageChange string `json:"cp"`
 Open string `json:"op"`
 High string `json:"hi"`
 Low string `json:"lo"`
 Volume string `json:"vo"`
 AverageVolume string `json:"avvo"`
 High52 string `json:"hi52"`
 Low52 string `json:"lo52"`
 MarketCap string `json:"mc"`
 EPS string `json:"eps"`
 Shares string `json:"shares"`
}
```

Once this is parsed and everything is happy, we can move onto storage and analysis.

### Getting the data

We use the config Symbols variable to form the url for data retrieval. The response is then massaged and the result set parsed. There are some sanitizing functions which I won't go into, the most important function is the `parseJSONData`.

```
symbolString := convertStocksString(configuration.Symbols)

var urlStocks string = "https://www.google.com/finance/info?infotype=infoquoteall&q=" + symbolString
body := getDataFromURL(urlStocks)

jsonString := sanitizeBody("google", body)

stockList := make([]Stock, 0)
stockList = parseJSONData(jsonString)

...

func parseJSONData(jsonString []byte) (stockList []Stock) {
 raw := make([]json.RawMessage, 10)
 if err := json.Unmarshal(jsonString, &raw); err != nil {
 log.Fatalf("error %v", err)
 }

 for i := 0; i < len(raw); i += 1 {
 stock := Stock{}
 if err := json.Unmarshal(raw[i], &stock); err != nil {
 fmt.Println("error %v", err)
 }

 stockList = append(stockList, stock)
 }

 return
}
```

Now that we have the data in a usable format, the string representation of numbers is adapted into full digits: `M = X 10_misconceptions_about_neural_networks.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md html httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md keendashboards.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md natural_language_basics_with_textblob.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh png prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md tgriesserbookshelf.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md webxray.md whose_xterm_is_it_anyway.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md 1 000 000`, `B = X 10_misconceptions_about_neural_networks.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md basecamptrix.md better_logging_in_nodejs_using_bunyan.md be_with_130_premade_layouts_the_hard_work_was_already_done_for_you.md binaryjs.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md clipboardjs.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md digitaloceangodo.md document_clustering_with_python.md dokku__the_smallest_paas_implementation_youve_ever_seen.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md exploring_the_relationship_between_javascript_and_the_modern_html_dom.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_convert_videos_in_linux_using_the_command_line.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md how_to_split_large_audio_files_on_linux.md html httpie_a_cli_http_client_that_will_make_you_smile.md httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jack000expose.md jamiehdigitaloceancsharp.md javascript_animation_physics_and_input_tracking.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md jwagnersmartcropjs.md keendashboards.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md learning_jquery.md letsencryptletsencrypt.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md natural_language_basics_with_textblob.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh png prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quench.md quintus_javascript_html5_game_engine.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md selzplyr.md setting_up_mastermaster_replication_with_mysql_on_debian_8_jessie.md setting_up_nginx_with_docker.md silentbicycleguff.md single_page_apps_in_depth.md skavanaghkeybox.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md tgriesserbookshelf.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md voice_activated_light_switch.md webxray.md whose_xterm_is_it_anyway.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md 1 000 000 000`. This data can now finally be stored.

### Storage

I chose a relational database for the data storage, namely MySQL. It's common and scales pretty well, and Go has really good support for it. The following code illustrates a write operation for saving the data:

```
// Prepare statement for inserting data
insertStatement := "INSERT INTO st_data (`symbol`, `exchange`, `name`, `change`, `close`, `percentageChange`, `open`, `high`, `low`, `volume` , `avgVolume`, `high52` , `low52`, `marketCap`, `eps`, `shares`, `time`, `minute`, `hour`, `day`, `month`, `year`) "
insertStatement += "VALUES( ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ? )"
stmtIns, err := db.Prepare(insertStatement)
if err != nil {
 panic(err.Error()) // proper error handling instead of panic in your app
}
defer stmtIns.Close() // Close the statement when we leave main() / the program terminates

... Massage data ...

_, err = stmtIns.Exec(stock.Symbol, stock.Exchange, stock.Name, sqlChange, sqlClose,
 sqlPercChange, sqlOpen, sqlHigh, sqlLow, sqlVolume, sqlAvgVolume,
 sqlHigh52, sqlLow52, sqlMarketCap, sqlEps, sqlShares,
 sqlTime, sqlMinute, sqlHour, sqlDay, sqlMonth, sqlYear)

if err != nil {
 fmt.Println("Could not save results: " + err.Error())
}
```

At the end of this we have:

- Config loaded from file
- Stock data being retrieved
- Stock data parsed and massaged
- Stock data saved into a database

We can now get onto the interesting part: _analysis_.

### Analysis of data: Gainers and Losers

I wanted to receive a nice (HTML) email periodically with a list of stocks ordered by gain. This requires: an interval, a HTML parser, and an SMTP mail call.

**The interval**

Interval timing is relatively easy in Go:

```
for _ = range time.Tick(n * time.Second) {
 ...
}
```

During the interval various checks are done to make sure the emails are only sent when they should be: during stock opening hours, Monday to Friday. The hours they are sent on is also harcoded, but can easily be set into the config.

**The HTML parser**

Go also has a great parsing library. An HTML template is injected with a variable and parsed accordingly:

```
mailTpl.Title = "Stock update"
t, err := template.ParseFiles("tpl/notification.html")
if err != nil {
 fmt.Println("template parse error: ", err)
 return
}
err = t.Execute(&templateString, mailTpl)
if err != nil {
 fmt.Println("template executing error: ", err)
 return
}
```

Where `mailTpl` is a struct containing stock data and some meta, such as the title above. The template contains variables to be parsed, as well as looping functions. A section:

```
{{ with .Stocks }}
 {{ range . }}
 <table with="100%" border="1px solid #ccc">
 <tr width="100%" style="border: 1px solid #ccc">
 <td><strong>Name</strong></td><td>{{ .Name }}</td>
 <td></td><td></td>
 </tr>
 ...
 </table>
 <br /><br />
 {{ end }}
{{ end }}
```

With the HTML, we are now ready to send the email.

**Sending the formatted email**

Sending SMTP mail with Go is straightforward:

```
func sendMail(configuration Configuration, notifyMail string) {
 // Send email
 // Set up authentication information.
 auth := smtp.PlainAuth("", configuration.MailUser, configuration.MailPass, configuration.MailSMTPServer)

 // Connect to the server, authenticate, set the sender and recipient,
 // and send the email all in one step.
 mime := "MIME-version: 1.0;\nContent-Type: text/html; charset=\"UTF-8\";\n\n"
 to := []string{configuration.MailRecipient}
 msg := []byte("To: " + configuration.MailRecipient + "\r\n" +
 "Subject: Quote update!\r\n" +
 mime + "\r\n" +
 "\r\n" +
 notifyMail +
 "\r\n")

 err := smtp.SendMail(configuration.MailSMTPServer+":"+configuration.MailSMTPPort, auth, configuration.MailSender, to, msg)
 if err != nil {
 log.Fatal(err)
 }
}
```

### Deeper analysis: Trends

After we have gathered enough data (I was doing calls in 15 minute intervals), we can start doing some good analysis. My trend analysis is really simple. You take a 3 day sample of data, check for increasing (or decreasing) prices day on day, and a once-off increase in volume over the same period.

```
if closes[0] > closes[1] && closes[1] > closes[2] && (volumes[0] > volumes[2] || volumes[0] > volumes[1]) {
 return true
}
```

I then also added in a standard deviation function to get some idea of the volatility of the stock in question. The function is fairly long, but you can see the [full calculation here](https://github.com/ksred/go-stock-notifier/blob/master/analysis.go#L149).

The result is a separate mail, sent end of day or before market open, with the trending stocks as calculated above including an indication of volatility. This can be extended to include other more complex technical analysis, such as overlaying bollinger bands, stochastic graphs for price points, and more.

## Conclusion

As my first work in Go, I am really pleased with the results. Loads of improvements can be made for sure, and I'll be updating periodically with these as I learn more about Go and it's deeper functionality.

I've also fallen in love with Go. It is a beautiful language to code in, and I am definitely going to do more projects in it. If you find this project useful, or feel you can improve some of the functionality, feel to to submit a pull request. [The code to this project lives on Github](https://github.com/ksred/go-stock-notifier/).
