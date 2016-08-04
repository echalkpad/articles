# 30 Python Language Features and Tricks You May Not Know About

[Original URL](http://sahandsaba.com/thirty-python-language-features-and-tricks-you-may-not-know.html)

> >>> a = [1, 2, 3, 4, 5, 6] >>> # Using iterators >>> group_adjacent = lambda a, k: zip(*([iter(a)] 01orgvirtualstoragemanager.md 21_best_nodejs_frameworks_for_developers_2014.md avrtutorial.md azureazurexplatcli.md data.json data_ml devops docker electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md embedded gaming github go gulp_as_a_development_web_server__tuts_code_tutorial.md herokulike_wordpress_deployments_with_git__dokku__exygy.md how_to_create_flexible_services_for_a_coreos_cluster_with_fleet_unit_files.md how_to_serve_flask_applications_with_uwsgi_and_nginx_on_ubuntu_1404.md how_to_set_up_automatic_deployment_with_git_with_a_vps.md how_to_use_fabric_to_automate_administration_tasks_and_deployments.md how_to_use_fleet_and_fleetctl_to_manage_your_coreos_cluster.md html installing_paz_on_digital_ocean_·_paz.md javascript linux mobile null.md one_ui_to_rule_them_all_manage_your_docker_containers_along_with_your_servers_and_vms.md piwigo_is_open_source_photo_gallery_software_for_the_web.md png programming quick_start_guide_--_gluster.md recordit_blog.md running_a_meteor_app_on_dokku.md science simple_blog_deployment_using_ghost_and_docker__packt.md social storage_ceph.md tidy tyblog.md url_to_filename.csv web_dev working_with_indexeddb__tuts_code_tutorial.md yodlrcoregi.md k

> > > a = [1, 2, 3, 4, 5, 6]

> > > # Using iterators

> > > group_adjacent = lambda a, k: zip(_([iter(a)]_ k)) group_adjacent(a, 3) [(1, 2, 3), (4, 5, 6)] group_adjacent(a, 2) [(1, 2), (3, 4), (5, 6)] group_adjacent(a, 1) [(1,), (2,), (3,), (4,), (5,), (6,)]

> > > # Using slices

> > > from itertools import islice group_adjacent = lambda a, k: zip(*(islice(a, i, None, k) for i in range(k))) group_adjacent(a, 3) [(1, 2, 3), (4, 5, 6)] group_adjacent(a, 2) [(1, 2), (3, 4), (5, 6)] group_adjacent(a, 1) [(1,), (2,), (3,), (4,), (5,), (6,)]
