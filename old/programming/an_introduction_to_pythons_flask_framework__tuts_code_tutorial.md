# An Introduction to Python's Flask Framework - Tuts+ Code Tutorial

[Original URL](http://code.tutsplus.com/tutorials/an-introduction-to-pythons-flask-framework--net-28822)

> Flask is a small and powerful web framework for Python. It's easy to learn and simple to use, enabling you to build your web app in a short amount of time. In this article, I'll show you how to build...

[Flask](http://flask.pocoo.org/) is a small and powerful web framework for Python. It's easy to learn and simple to use, enabling you to build your web app in a short amount of time.

In this article, I'll show you how to build a simple website, containing two static pages with a small amount of dynamic content. While Flask can be used for building complex, database-driven websites, starting with mostly static pages will be useful to introduce a workflow, which we can then generalize to make more complex pages in the future. Upon completion, you'll be able to use this sequence of steps to jumpstart your next Flask app.

## Installing Flask

Before getting started, we need to install Flask. Because systems vary, things can sporadically go wrong during these steps. If they do, like we all do, just Google the error message or leave a comment describing the problem.

### Install virtualenv

> Virtualenv is a useful tool that creates isolated Python development environments where you can do all your development work.

We'll use [virtualenv](http://pypi.python.org/pypi/virtualenv) to install Flask. Virtualenv is a useful tool that creates isolated Python development environments where you can do all your development work. Suppose you come across a new Python library that you'd like to try. If you install it system-wide, there is the risk of messing up other libraries that you might have installed. Instead, use virtualenv to create a sandbox, where you can install and use the library without affecting the rest of your system. You can keep using this sandbox for ongoing development work, or you can simply delete it once you've finished using it. Either way, your system remains organized and clutter-free.

It's possible that your system already has virtualenv. Refer to the command line, and try running:

```
$ virtualenv --version
```

If you see a version number, you're good to go and you can skip to this "Install Flask" section. If the command was not found, use `easy_install` or `pip` to install virtualenv. If running Linux or Mac OS X, one of the following should work for you:

```
$ sudo easy_install virtualenv
```

or:

```
$ sudo pip install virtualenv
```

or:

```
$ sudo apt-get install python-virtualenv
```

If you don't have either of these commands installed, there are several tutorials online, which will show you how to install it on your system. If you're running Windows, follow the "Installation Instructions" on [this page](http://pypi.python.org/pypi/setuptools) to get `easy_install` up and running on your computer.

### Install Flask

After installing `virtualenv`, you can create a new isolated development environment, like so:

```
$ virtualenv flaskapp
```

Here, `virtualenv` creates a folder, _flaskapp/_, and sets up a clean copy of Python inside for you to use. It also installs the handy package manager, `pip`.

Enter your newly created development environment and activate it so you can begin working within it.

```
$ cd flaskapp
$ . bin/activate
```

Now, you can safely install Flask:

```
$ pip install Flask
```

## Setting up the Project Structure

Let's create a couple of folders and files within _flaskapp/_ to keep our web app organized.

```
.
.
├── app
│ ├── static
│ │ ├── css
│ │ ├── img
│ │ └── js
│ ├── templates
│ ├── routes.py
│ └── README.md
```

Within _flaskapp/_, create a folder, _app/_, to contain all your files. Inside _app/_, create a folder _static/_; this is where we'll put our web app's images, CSS, and JavaScript files, so create folders for each of those, as demonstrated above. Additionally, create another folder, _templates/_, to store the app's web templates. Create an empty Python file **routes.py** for the application logic, such as URL routing.

And no project is complete without a helpful description, so create a **README.md** file as well.

Now, we know where to put our project's assets, but how does everything connect together? Let's take a look at "Fig. 1" below to see the big picture:

**Fig. 1**

![]()

1. A user issues a request for a domain's root URL `/` to go to its home page
2. **routes.py** maps the URL `/` to a Python function
3. The Python function finds a web template living in the _templates/_ folder.
4. A web template will look in the _static/_ folder for any images, CSS, or JavaScript files it needs as it renders to HTML
5. Rendered HTML is sent back to **routes.py**
6. **routes.py** sends the HTML back to the browser

We start with a request issued from a web browser. A user types a URL into the address bar. The request hits **routes.py**, which has code that maps the URL to a function. The function finds a template in the _templates/_ folder, renders it to HTML, and sends it back to the browser. The function can optionally fetch records from a database and then pass that information on to a web template, but since we're dealing with mostly static pages in this article, we'll skip interacting with a database for now.

Now that we know our way around the project structure we set up, let's get started with making a home page for our web app.

## Creating a Home Page

When you write a web app with a couple of pages, it quickly becomes annoying to write the same HTML boilerplate over and over again for each page. Furthermore, what if you need to add a new element to your app, such as a new CSS file? You would have to go into every single page and add it in. This is time consuming and error prone. Wouldn't it be nice if, instead of repeatedly writing the same HTML boilerplate, you could define your page layout just once, and then use that layout to make new pages with their own content? This is exactly what web templates do!

> Web templates are simply text files that contain variables and control flow statements (`if..else`, `for`, etc), and end with an `.html` or `.xml` extension.

The variables are replaced with your content, when the web template is evaluated. Web templates remove repetition, separate content from design, and make your application easier to maintain. In other, simpler words, web templates are awesome and you should use them! Flask uses the Jinja2 template engine; let's see how to use it.

As a first step, we'll define our page layout in a skeleton HTML document **layout.html** and put it inside the _templates/_ folder:

**app/templates/layout.html**

```
<!DOCTYPE html>
<html>
 <head>
 <title>Flask App</title> 
 </head>
 <body>

 <header>
 <div class="container">
 <h1 class="logo">Flask App</h1>
 </div>
 </header> 

 <div class="container">
 {% block content %}
 {% endblock %}
 </div>

 </body>
</html>
```

This is simply a regular HTML file...but what's going on with the `{% block content %}{% endblock %}` part? To answer this, let's create another file **home.html**:

**app/templates/home.html**

```
{% extends "layout.html" %}
{% block content %}
 <div class="jumbo">
 <h2>Welcome to the Flask app<h2>
 <h3>This is the home page for the Flask app<h3>
 </div>
{% endblock %}
```

The file **layout.html** defines an empty block, named `content`, that a child template can fill in. The file **home.html** is a child template that inherits the markup from **layout.html** and fills in the "content" block with its own text. In other words, **layout.html** defines all of the common elements of your site, while each child template customizes it with its own content.

This all sounds cool, but how do we actually see this page? How can we type a URL in the browser and "visit" **home.html**? Let's refer back to Fig. 1\. We just created the template **home.html** and placed it in the _templates/_ folder. Now, we need to map a URL to it so we can view it in the browser. Let's open up **routes.py** and do this:

**app/routes.py**

```
from flask import Flask, render_template

app = Flask(__name__) 

@app.route('/')
def home():
 return render_template('home.html')

if __name__ == '__main__':
 app.run(debug=True)
```

That's it for `routes.py`. What did we do?

1. First. we imported the Flask class and a function `render_template`.
2. Next, we created a new instance of the Flask class.
3. We then mapped the URL `/` to the function `home()`. Now, when someone visits this URL, the function `home()` will execute.
4. The function `home()` uses the Flask function `render_template()` to render the **home.html** template we just created from the _templates/_ folder to the browser.
5. Finally, we use `run()` to run our app on a local server. We'll set the `debug` flag to `true`, so we can view any applicable error messages if something goes wrong, and so that the local server automatically reloads after we've made changes to the code.

We're finally ready to see the fruits of our labor. Return to the command line, and type:

```
$ python routes.py
```

Visit <http://localhost:5000/> in your favorite web browser.

![]()

When we visited [http://localhost:5000/](http://localhost:5000), **routes.py** had code in it, which mapped the URL `/` to the Python function `home()`. `home()` found the web template **home.html** in the _templates/_ folder, rendered it to HTML, and sent it back to the browser, giving us the screen above.

Pretty neat, but this home page is a bit boring, isn't it? Let's make it look better by adding some CSS. Create a file, **main.css**, within _static/css/_, and add these rules:

**static/css/main.css**

```
body {
 margin: 0;
 padding: 0;
 font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
 color: #444;
}

/*
 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md 30_fantastic_online_business_apps_«_webappstorm.md a_beginners_guide_to_lvm.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul__scotts_weblog__the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion__the_analytical_approach.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals__introduction.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs__tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it’s_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back__simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash’s_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_access_ssh_terminal_in_web_browser_on_linux.md how_to_be_polite_—_the_message.md how_to_diff_and_merge_files_or_directories_on_linux.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_look_up_the_geographic_location_of_an_ip_address_from_the_command_line.md how_to_search_text_files_for_patterns_efficiently.md how_to_secure_a_mail_server_using_encryption.md how_to_set_up_webbased_network_traffic_monitoring_system_on_linux.md how_to_spoof_the_mac_address_of_a_network_interface_on_linux.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don’t_hire_0x_engineers.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine__make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain__helen_keller’s_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md tfidf_is_about_what_matters.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company’s_secrets.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md Create dark grey header with a white logo
 */

header {
 background-color: #2B2B2B;
 height: 35px;
 width: 100%;
 opacity: .9;
 margin-bottom: 10px;
}

header h1.logo {
 margin: 0;
 font-size: 1.7em;
 color: #fff;
 text-transform: uppercase;
 float: left;
}

header h1.logo:hover {
 color: #fff;
 text-decoration: none;
}

/*
 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md 30_fantastic_online_business_apps_«_webappstorm.md a_beginners_guide_to_lvm.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul__scotts_weblog__the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion__the_analytical_approach.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals__introduction.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs__tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it’s_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back__simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash’s_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_access_ssh_terminal_in_web_browser_on_linux.md how_to_be_polite_—_the_message.md how_to_diff_and_merge_files_or_directories_on_linux.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_look_up_the_geographic_location_of_an_ip_address_from_the_command_line.md how_to_search_text_files_for_patterns_efficiently.md how_to_secure_a_mail_server_using_encryption.md how_to_set_up_webbased_network_traffic_monitoring_system_on_linux.md how_to_spoof_the_mac_address_of_a_network_interface_on_linux.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don’t_hire_0x_engineers.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine__make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain__helen_keller’s_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md tfidf_is_about_what_matters.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company’s_secrets.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md Center the body content
 */

.container {
 width: 940px;
 margin: 0 auto;
}

div.jumbo {
 padding: 10px 0 30px 0;
 background-color: #eeeeee;
 -webkit-border-radius: 6px;
 -moz-border-radius: 6px;
 border-radius: 6px;
}

h2 {
 font-size: 3em;
 margin-top: 40px;
 text-align: center;
 letter-spacing: -2px;
}

h3 {
 font-size: 1.7em;
 font-weight: 100;
 margin-top: 30px;
 text-align: center;
 letter-spacing: -1px;
 color: #999;
}
```

Add this stylesheet to the skeleton file **layout.html** so that the styling applies to all of its child templates by adding this line to its <head> element:

```
 <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">;
```

We're using the Flask function, `url_for`, to generate a URL path for **main.css** from the _static_ folder. After adding this line in, **layout.html** should now look like:

**app/templates/layout.html**

```
<!DOCTYPE html>
<html>
 <head>
 <title>Flask</title> 
 <strong><link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}"></strong>
 </head>
 <body>
 <header>
 <div class="container">
 <h1 class="logo">Flask App</h1>
 </div>
     </header>

 <div class="container">
 {% block content %}
 {% endblock %}
 </div>
 </body>
</html>
```

Let's switch back to the browser and refresh the page to view the result of the CSS.

![]()

That's more like it! Now, when we visit [http://localhost:5000/](http://localhost:5000), **routes.py** still maps the URL `/` to the Python function `home()`, and `home()` still finds the web template **home.html** in the _templates/_ folder. But, since we added the CSS file **main.css**, the web template **home.html** looks in _static/_ to find this asset, before rendering to HTML and being sent back to the browser.

We've achieved a lot so far. We started with Fig. 1 by understanding how Flask works, and now we've seen how it all plays out, by creating a home page for our web app. Let's move on and create an About page.

## Creating an About Page

In the previous section, we created a web template **home.html** by extending the skeleton file **layout.html**. We then mapped the URL `/` to **home.html** in **routes.py** so we could visit it in the browser. We finished things up by adding some styling to make it look pretty. Let's repeat that process again to create an about page for our web app.

We'll begin by creating a web template, **about.html**, and putting it inside the _templates/_ folder.

**app/templates/about.html**

```
{% extends "layout.html" %}

{% block content %}
 <h2>About</h2>
 <p>This is an About page for the Intro to Flask article. Don't I look good? Oh stop, you're making me blush.</p>
{% endblock %}
```

Just like before with **home.html**, we extend from **layout.html**, and then fill the `content` block with our custom content.

In order to visit this page in the browser, we need to map a URL to it. Open up **routes.py** and add another mapping:

```
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
 return render_template('home.html')

@app.route('/about')
def about():
 return render_template('about.html')

if __name__ == '__main__':
 app.run(debug=True)
```

We mapped the URL `/about` to the function `about()`. Now we can open up the browser and go to <http://localhost:5000/about> and check out our newly created page.

![]()

## Adding Navigation

Most websites have links to their main pages within the header or footer of the document. These links are usually visible across all pages of a website. Let's open up the skeleton file, **layout.html**. and add these links so they show up in all of the child templates. Specifically, let's add a <nav> element inside the <header> element:

**app/templates/layout.html**

```
...
<header>
 <div class="container">
 <h1 class="logo">Flask App</h1>
 <strong><nav>
 <ul class="menu">
 <li><a href="{{ url_for('home') }}">Home</a></li>
 <li><a href="{{ url_for('about') }}">About</a></li>
 </ul>
 </nav></strong>
 </div>
</header>
...
```

Once again, we use the Flask function `url_for` to generate URLs.

Next, add some more style rules to **main.css** to make these new navigation elements look good:

**app/static/css/main.css**

```
...

/*
 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md 30_fantastic_online_business_apps_«_webappstorm.md a_beginners_guide_to_lvm.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul__scotts_weblog__the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion__the_analytical_approach.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals__introduction.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs__tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it’s_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back__simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash’s_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_access_ssh_terminal_in_web_browser_on_linux.md how_to_be_polite_—_the_message.md how_to_diff_and_merge_files_or_directories_on_linux.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_look_up_the_geographic_location_of_an_ip_address_from_the_command_line.md how_to_search_text_files_for_patterns_efficiently.md how_to_secure_a_mail_server_using_encryption.md how_to_set_up_webbased_network_traffic_monitoring_system_on_linux.md how_to_spoof_the_mac_address_of_a_network_interface_on_linux.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don’t_hire_0x_engineers.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine__make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain__helen_keller’s_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md tfidf_is_about_what_matters.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company’s_secrets.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md Display navigation links inline
 */

.menu {
 float: right;
 margin-top: 8px;
}

.menu li {
 display: inline;
}

.menu li + li {
 margin-left: 35px;
}

.menu li a {
 color: #999;
 text-decoration: none;
}
```

Finally, open up the browser and refresh <http://localhost:5000/> to see our newly added navigation links.

![]()

## Conclusion

Over the course of this article, we built a simple web app with two, mostly static, pages. In doing so, we learned a workflow that can be used to create more complex websites with dynamic content. [Flask](http://flask.pocoo.org/) is a simple, but powerful framework that enables you to efficiently build web apps. Go ahead - [check it out](http://flask.pocoo.org/)!
