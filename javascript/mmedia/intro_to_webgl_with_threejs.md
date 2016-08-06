# Intro to WebGL with Three.js

[Original URL](http://davidscottlyons.com/threejs/presentations/frontporch14/#slide-0)

> Intro to WebGLwith Three.js WebGL JavaScript API for rendering interactive 2D and 3D graphics inside an HTML <canvas> element. Browser Support three.js 3D Javascript Library Renderers: WebGL,...

## Intro to WebGLwith Three.js

## WebGL

JavaScript API for rendering interactive 2D and 3D graphics<br>
inside an HTML `<canvas>` element.

### Browser Support

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/webgl-browser-support.png)

## three.js

3D Javascript Library

Renderers: WebGL, <canvas>, <svg>, CSS3D / DOM, and more

Scenes, Cameras, Geometry, 3D Model Loaders, Lights, Materials, Shaders, Particles, Animation, Math Utilities

```
<!DOCTYPE html>
<html>
 <head>
 <title>Basic Three.js App</title>
 <style>
 html, body { margin: 0; padding: 0; overflow: hidden; }
 </style>
 </head>
 <body>
 <script src="js/three.min.js"></script>
 <script>
 // Javascript will go here.
 </script>
 </body>
</html>

var scene = new THREE.Scene();
var aspect = window.innerWidth / window.innerHeight;
var camera = new THREE.PerspectiveCamera( 75, aspect, 0.1, 1000 );
var renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight );
document.body.appendChild( renderer.domElement );

var geometry = new THREE.BoxGeometry( 1, 1, 1 );
var material = new THREE.MeshNormalMaterial();
var cube = new THREE.Mesh( geometry, material );
scene.add( cube );
camera.position.z = 5;
var render = function () {
 requestAnimationFrame( render );
 cube.rotation.x += 0.1;
 cube.rotation.y += 0.1;
 renderer.render( scene, camera );
};

render();
```

## Object3D

```
var group = new THREE.Object3D();
scene.add( group );

group.add( mesh1 );
group.add( mesh2 );

mesh2.visible = false;
group.remove( mesh2 );

group.children // mesh1
group.parent // scene
```

## Object3D Transforms

```
mesh.rotation.y = Math.PI / 4

mesh.rotation.y = Math.PI 10_misconceptions_about_neural_networks.md a_developer’s_journey_into_linux_containers.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md better_logging_in_nodejs_using_bunyan.md binaryjs.md brief_survey_on_methods_for_attacking_tor_hidden_service.md checkpoint_and_restore_docker_container_with_criu.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md digitaloceangodo.md document_clustering_with_python.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web—the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md jamiehdigitaloceancsharp.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md keendashboards.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md let’s_write_a_web_extension.md let_your_applications_fly.md links_list.txt mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md natural_language_basics_with_textblob.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh png prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quintus_javascript_html5_game_engine.md reclaiming_a_timemachine_volume’s_disk_space.md resilient_substructure_for_your_microservice.md selzplyr.md setting_up_nginx_with_docker.md silentbicycleguff.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md tgriesserbookshelf.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md ˈtræfɪk_·_ˈtræfɪk.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md webxray.md whose_xterm_is_it_anyway.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md 5 / 4
```

## Unit Circle

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/unit-circle.png)

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/unit-circle-radians.png)

```
mesh.rotation.y = THREE.Math.degToRad(45);
```

## Unit Circle

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/unit-circle-trig.png)

```
mesh.position.x = Math.cos( time );
mesh.position.y = Math.sin( time );
```

## Cameras

```
cam = new THREE.PerspectiveCamera( fov, aspect, near, far )

cam = new THREE.PerspectiveCamera( fov, aspect, near, far )

cam = new THREE.PerspectiveCamera( fov, aspect, near, far )

camera = new THREE.OrthographicCamera( left, right, top, bottom, near, far );
```

## Camera Controls

/three.js/examples/js/controls/OrbitControls.js

```
<script src="path/to/OrbitControls.js"></script>

controls = new THREE.OrbitControls( camera );

function render() {
 requestAnimationFrame( render );
 controls.update();
 renderer.render( scene, camera );
}

controls.noPan = true;
controls.noZoom = true;
controls.noRotate = true;

controls.minDistance
controls.maxDistance

controls.minPolarAngle
controls.maxPolarAngle
```

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/geometry.png)

## Geometry

## Geometry

```
var geo = new THREE.BoxGeometry( width, height, depth );

var geo = new THREE.SphereGeometry( 60, 24, 16 );

var geo = new THREE.CylinderGeometry( ... );

var geo = new THREE.TorusGeometry( ... );
```

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/marble.jpg)

## Materials

## Materials

```
var material = new THREE.MeshBasicMaterial({ ... });

var material = new THREE.MeshLambertMaterial({ ... });

var material = new THREE.MeshPhongMaterial({ ... });

var material = new THREE.MeshNormalMaterial({ ... });

var material = new THREE.MeshNormalMaterial({ ... });
```

## Material Properties

```
shading: THREE.SmoothShading

shading: THREE.FlatShading

shading: THREE.FlatShading // face normals

shading: THREE.FlatShading // face normals

shading: THREE.SmoothShading // vertex normals

transparent: true, opacity: 0.5
```

## Texture Mapping

```
texture = THREE.ImageUtils.loadTexture("color-map.jpg");

map: colorMap, specularMap: specMap, normalMap: normalMap

var material = new THREE.MeshPhongMaterial({
 color: 0xaaaaaa,
 ambient: 0xaaaaaa,
 specular: 0x333333,
 shininess: 15,
 map: colorMap,
 specularMap: specMap,
 normalMap: normalMap
});
```

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/sunny-sky.jpg)

## Lights

## Lights

```
light = new THREE.DirectionalLight( 0xdddddd, 0.8 );

light.position.set( -80, 80, 80 );

light.target.position = 160;

light = new THREE.DirectionalLight( 0xdddddd, 0.8 );

light = new THREE.DirectionalLight( 0xb4e7f2, 0.8 );

light = new THREE.DirectionalLight( 0xb4e7f2, 0.2 );

light = new THREE.DirectionalLight( 0xb4e7f2, 1.5 );

light = new THREE.DirectionalLight( 0xb4e7f2, 0.8 );

light = new THREE.PointLight( 0xb4e7f2, 0.8 );

light = new THREE.PointLight( 0xb4e7f2, 0.8 );

light = new THREE.SpotLight( 0xb4e7f2, 0.8 );

light.angle = Math.PI / 9;

light.angle = Math.PI / 5;

light = new THREE.AmbientLight( 0x444444 );

light = new THREE.AmbientLight( 0x000000 );

light = new THREE.AmbientLight( 0x444444 );
```

## Three Point Lighting

Key, Fill, Rim

Key, Fill, Rim

## Model Converter

OBJ to JSON converter python tool<br>
/three.js/utils/converters/obj/convert_obj_three.py

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/obj-json.png)

```
python convert_obj_three.py -i teapot.obj -o teapot.js
```

## Model Loader

```
var loader = new THREE.JSONLoader();

loader.load("teapot.js", function( geometry, materials ) {
 material = new THREE.MeshFaceMaterial( materials );
 mesh = new THREE.Mesh( geometry, material );
 scene.add( mesh );
});
```

![](http://davidscottlyons.com/threejs/presentations/frontporch14/images/interaction.png)

## Interaction

```
mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

projector = new THREE.Projector();
raycaster = new THREE.Raycaster();

var vector = new THREE.Vector3( mouse.x, mouse.y, 1 );

projector.unprojectVector( vector, camera );

raycaster.set( camera.position, vector.sub( camera.position ).normalize() );

var intersects = raycaster.intersectObjects( scene.children );

INTERSECTED = intersects[ 0 ].object;
```
