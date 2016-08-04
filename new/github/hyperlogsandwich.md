# Home

[Original URL](https://github.com/chanian/hyperlogsandwich/wiki)

> A probabilistic data structure for frequency/k-occurrence cardinality estimation of multisets. Sample implementation "How many colours appear n-times?" In the figure above, we first group nodes by...

A probabilistic data structure for frequency/k-occurrence cardinality estimation of multisets. [Sample implementation](https://github.com/chanian/hyperlogsandwich/blob/master/lib/hyperlog-sandwich.js)

![Group by uuid, Count, Group by Count](https://camo.githubusercontent.com/fb2607518e2c44d64272a384c35370afce9c8a25/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4237646b446f58436341454e656c552e706e67)

> "How many colours appear n-times?" In the figure above, we first group nodes by colour, then count each occurrence, then regroup on the frequency of those occurrences, finally counting the number of coloured nodes which appeared more than n times. Our HyperLogSandwich is an estimator on the size of each grouped bucket.

## [](https://github.com/chanian/hyperlogsandwich/wiki#application)Application

This aims to solve the general counting problem for the question:

> **Given multiset A, how many items have appeared k times?**

Unlike the HyperLogLog[[1](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf)], which estimates the cardinality of unique items in a set, and unlike a CountMinSketch, which estimates the frequency of a specific item, the HyperLogSandwich estimates the number of unique items that have occurred for any frequency. While these exact counts could easily be computed, we aim to support arbitrary composition of these structures, support for stream compute, and space efficiency at the cost of ideally low error rates.

Real world examples:

- How many people viewed my Tweets 4+ times this month?
- How many users watched this video 2 times this week?
- How many users visited my website 3 times during an arbitrary time range?
- How many log exceptions happened 10+ times last week?
- How many people bought 4+ items this week?

## [](https://github.com/chanian/hyperlogsandwich/wiki#requirements--desires)Requirements / Desires

- significantly more space efficient than naive counting
- support associative binary operation

### [](https://github.com/chanian/hyperlogsandwich/wiki#hyperlogsandwich-hyperloglog--countminsketch)HyperLogSandwich: HyperLogLog + CountMinSketch

(k+ occurrence approximation)

![CountMinSketch index](https://camo.githubusercontent.com/7bbce428c5ffca83bab1cec9e46487370ccc38c7/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4237646d377a6f43594145517861392e706e67)

Using the Count Min Sketch[[2](http://dimacs.rutgers.edu/%7Egraham/pubs/papers/cm-full.pdf)] to approximate the frequency of a specific item, we then use those approximations as indices into a table of HyperLogLogs, which approximate the cardinality of unique items that had that frequency in the multiset. We can arbitrarily choose how many levels of depth/frequency we care to store by simply adding more HyperLogLogs.

```
 // using 1-indexed arrays
 depth = d
 initialize collection of d HyperLogLogs, HLL[1]..HLL[d]
 initialize cms CountMinSketch

 // iterate through each item in set or stream
 for each item in stream:

 // insert item into CMS
 cms.count(item, 1)

 // fetch the current frequency approximation
 frequency = cms.query(item)

 // count all i or more occurrence of this item
 for i in 1 .. max(d, frequency)
 // Each HLL by design will ignore/correct for duplicates inserted
 H[i].insert(item)
```

## [](https://github.com/chanian/hyperlogsandwich/wiki#example-usages)Example usages:

Take n unique items - and artificially introduce duplicates with the frequency distribution as described below: ![](https://camo.githubusercontent.com/665b785393d1c80df0c25fbea4596082a4f8e523/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4236357a5730314351414156636a332e706e67)

![HyperLogSandwich: Count Min Sketch, Cardinality / Error](https://camo.githubusercontent.com/c2aac959ae23b2c40cd0c32e9a981aca07e4fce2/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4236356d4b7a6f43554141306f51462e706e67)

> Relative error vs. True Cardinality. Given this data distribution, we expect to see error increase as we start estimating larger frequencies. The probability an item has occurred with high frequency decreases given our distribution, however the probability for a mis-estimation of item frequency increases, especially as the CountMinSketch becomes saturated.

![Error/Depth](https://camo.githubusercontent.com/b86ec6b9006ca420fb0f65dcb6f345f787136b9e/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4236353956784a43454141634f49492e706e67)

> Tuning the CMS and choosing a correct ε, such that 99.9% of estimations are within ε 01orgvirtualstoragemanager.md 21_best_nodejs_frameworks_for_developers_2014.md 30_python_language_features_and_tricks_you_may_not_know_about.md avrtutorial.md a_whirlwind_tutorial_on_creating_really_teensy_elf_executables_for_linux.md azureazurexplatcli.md build_your_own_application_monitoring_system.md comparing_seven_monitoring_options_for_docker.md data.json data_ml devops docker electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md embedded gaming getting_started.md github go gulp_as_a_development_web_server__tuts_code_tutorial.md haxe__the_crossplatform_toolkit.md herokulike_wordpress_deployments_with_git__dokku__exygy.md how_to_create_flexible_services_for_a_coreos_cluster_with_fleet_unit_files.md how_to_generate_millions_of_http_requests.md how_to_serve_flask_applications_with_uwsgi_and_nginx_on_ubuntu_1404.md how_to_set_up_automatic_deployment_with_git_with_a_vps.md how_to_use_fabric_to_automate_administration_tasks_and_deployments.md how_to_use_fleet_and_fleetctl_to_manage_your_coreos_cluster.md html installing_paz_on_digital_ocean_·_paz.md javascript learning_javascript_design_patterns.md linux mobile null.md one_ui_to_rule_them_all_manage_your_docker_containers_along_with_your_servers_and_vms.md piwigo_is_open_source_photo_gallery_software_for_the_web.md png programming quick_start_guide_--_gluster.md recordit_blog.md running_a_meteor_app_on_dokku.md science simple_blog_deployment_using_ghost_and_docker__packt.md social storage_ceph.md tidy tyblog.md url_to_filename.csv using_reactjs_and_application_cache_for_a_fast_synced_app.md web_dev working_with_indexeddb__tuts_code_tutorial.md yodlrcoregi.md M from the true frequency, where M is the total stream size [[3](https://github.com/twitter/algebird/blob/develop/algebird-core/src/main/scala/com/twitter/algebird/CountMinSketch.scala#L52)]

Plotting different epsilon values for our CountMinSketch, as the likelihood of a high frequency cardinality decreases (and actual cardinality becomes smaller), our errors become much higher.

## [](https://github.com/chanian/hyperlogsandwich/wiki#associative-binary-operations)Associative Binary Operations

The merge or addition operation can be achieved by merging the underlying individual HyperLogLog. This also means we can have differently sized/configured internal HyperLogLogs if desirable while still supporting binary operations (eg: for space efficiency if we have prior knowledge of the distribution of the dataset).

```
 Define HLS1, HLS2
 Define HLS1.hll_i to be the ith sub internal HyperLogLog
 Define |HLS1.hll_i| as the cardinality estimation for items appearing i or more times
 For all i, configuration of HLS1.hll_i == HLS2.hll_i

 Then:
 Union
 |(HLS1 ∪ HLS2).hll_k| = |HLS1.hll_k ∪ HLS2.hll_k|

 Intersection
 |(HLS1 ∩ HLS2).hll_k| = |HLS1.hll_k| + |HLS2.hll_k| - |HLS1.hll_k ∪ HLS2.hll_k|
```

HLL Unions & Intersections (<http://research.neustar.biz/2012/12/17/hll-intersections-2/>)

**Example1** Storing a HLS per week, each storing occurrences of a specific log events. HLS1 ∪ HLS2 ∪ HLS3, combines the approximations across 3 weeks, and would let us query, for example, "how many log events occurred k times during this 3 week window?".

**Example2:** Storing a HLS per user, each storing phone numbers called ever. HLS1 ∩ HLS2 ∩ HLS3, intersects approximations across 3 users, and would let us query, for example, "how many numbers did all 3 people call 5 times?".

## [](https://github.com/chanian/hyperlogsandwich/wiki#tuning)Tuning

We can configure each HLL separately whilst still supporting binary operations (provided the internal configuration of each nth HLL match. This is especially important if there is some known information about the distribution of the data being stored.

## [](https://github.com/chanian/hyperlogsandwich/wiki#summary)Summary

The HyperLogSandwich is an efficient method for approximating k-occurrence cardinalities and also tasty snack. Further research would analyze specific space efficiencies, error propagation and much more.

### [](https://github.com/chanian/hyperlogsandwich/wiki#hyperlogsandwich-with-bloomfilter-alternative-approaches)HyperLogSandwich with Bloomfilter (alternative approaches)

![Bloomfilter Chain](https://camo.githubusercontent.com/697cc27855e90cae5d1d6d8c0b24e00cde2ad7c1/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4236356d742d6a43514145445351502e706e67)

```
 depth = d
 initialize collection of d HyperLogLogs, HLL[0]..HLL[d - 1]
 initialize bf Bloomfilters, BF[0]..BF[d - 1]
 for each item in stream:
 for i = 0..depth-1
 h[i].set(item)
 if(!bf[i].query(item)):
 bf[i].set(item)
 break;
```
