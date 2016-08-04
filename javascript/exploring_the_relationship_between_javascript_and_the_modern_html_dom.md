# Exploring the relationship between JavaScript and the modern HTML DOM

[Original URL](http://domenlightenment.com/)

> You might also like: JavaScript Enlightenment or jQuery Enlightenment An O'Reilly edited version (printed, epub, mobi, pdf) of this book is now avaliable from O'Reilly or Amazon Warning! This is the...

<span>You might also like: <a href="http://www.javascriptenlightenment.com/">JavaScript Enlightenment</a> or <a href="http://jqueryenlightenment.com/">jQuery Enlightenment</a></span>

An O'Reilly edited version (printed, epub, mobi, pdf) of this book is now avaliable from [O'Reilly](http://shop.oreilly.com/product/0636920027690.do) or [Amazon](http://www.amazon.com/DOM-Enlightenment-Cody-Lindley/dp/1449342841/)

**Warning!** This is the free online version of the DOM Enlightenment book in its pre-edited/draft form.

DOM Enlightenment

Exploring the relationship between JavaScript and the modern HTML DOM

By [Cody Lindley](http://twitter.com/codylindley) Version: 0.6.0

## Foreword

I make websites. Sometimes I make music. Over the years, I've noticed an interesting pattern of behavior from some musicians--often self-taught--who think of themselves as creative types: they display an aversion to learning any music theory. The logic, they say, is that knowing the theory behind music will somehow constrain their creative abilities. I've never understood that logic (and I secretly believe that it's a retroactive excuse for a lack of discipline). To my mind, I just don't see how any kind of knowledge or enlightenment could be a bad thing.

Alas, I have seen the same kind of logic at work in the world of web design. There are designers who not only don't know how to write markup and CSS, they actively refuse to learn. Again, they cite the fear of somehow being constrained by this knowledge (and again, I believe that's a self-justifying excuse).

In the world of front-end development, that attitude is fortunately far less prevalent. Most web devs understand that there's always more to learn. But even amongst developers who have an encyclopediac knowledge of HTML and CSS, there is often a knowledge gap when it comes to the Document Object Model. That's understandable. You don't need to understand the inner workings of the DOM if you're using a library like jQuery. The whole point of JavaScript libraries is to abstract away the browser's internal API and provide a different, better API instead.

Nonetheless, I think that many front-end devs have a feeling that they should know what's going on under the hood. That's the natural reaction of a good geek when presented with a system they're expected to work with. Now, thanks to DOM Enlightenment, they can scratch that natural itch.

Douglas Crockford gave us a map to understand the inner workings of the JavaScript language in his book JavaScript: The Good Parts. Now Cody Lindley has given us the corresponding map for the Document Object Model. Armed with this map, you'll gain the knowledge required to navigate the passageways and tunnels of the DOM. ix

You might not end up using this knowledge in every project. You might decide to use a library like jQuery instead. But now it will be your decision. Instead of having to use a library because that's all that you know, you can choose if and when to use a library. That's a very empowering feeling. That's what knowledge provides. That is true enlightenment.

--Jeremy Keith, founder and technical director of clearleft.com, and author of DOM Scripting: Web Design with JavaScript and the Document Object Model

## Introduction

This book is not an exhaustive reference on DOM scripting or [JavaScript](http://javascriptenlightenment.com/). It may, however, be the most exhaustive book written about DOM scripting without the use of a library/framework. The lack of authorship around this topic is not without good reason. Most technical authors are not willing to wrangle this topic because of the differences that exist among legacy browsers and their implementations of the DOM specifications (or lack thereof).

For the purpose of this book (i.e. grokking the concepts), I'm going to sidestep the browser API mess and dying browser discrepancies in an effort to expose the modern DOM. That's right, I'm going to sidestep the ugliness in an effort to focus on the here and now. After all, we have solutions like jQuery to deal with all that browser ugliness, and you should definitely be leveraging something like jQuery when dealing with deprecated browsers.

While I am not promoting the idea of only going native when it comes to DOM scripting, I did write this book in part so that developers may realize that DOM libraries are not always required when scripting the DOM. I also wrote for the lucky few who get to write JavaScript code for a single environment (i.e. one browser, mobile browsers, or HTML+CSS+JavaScript-to-native via something like PhoneGap). What you learn in this book may just make a DOM library unnecessary in ideal situations, say for example, some light DOM scripting for deployment on a Webkit mobile browser only.

## Who should read this book

As I authored this book, I specifically had two types of developers in mind. I assume both types already have an intermediate to advanced knowledge of JavaScript, HTML, and CSS. The first developer is someone who has a good handle on JavaScript or jQuery, but has really never taken the time to understand the purpose and value of a library like jQuery (the reason for its rhyme, if you will). Equipped with the knowledge from this book, that developer should fully be able to understand the value provided by jQuery for scripting the DOM. And not just the value, but how jQuery abstracts the DOM and where and why jQuery is filling the gaps. The second type of developer is an engineer who is tasked with scripting HTML documents that will only run in modern browsers or that will get ported to native code for multiple OS's and device distributions (e.g. PhoneGap) and needs to avoid the overhead (i.e. size or size v.s. use) of a library.

## Technical intentions, allowances, & limitations

- The content and code contained in this book was written with modern (IE9+, Firefox latest, Chrome latest, Safari latest, Opera latest) browsers in mind. It was my goal to only include concepts and code that are native to modern browsers. If I venture outside of this goal I will bring this fact to the readers attention. I've generally steered away from including anything in this book that is browser specific or implemented in a minority of the modern browsers.
- I'm not attempting in this book to dogmatically focus on a specific DOM, CSS, or HTML specification. Its not my goal here to dogmatically represent a specific specification. This would be too large of an undertaking (with little value IMO) given the number of specifications at work and the history/status of browsers correctly implementing the specifications. I have leverage and balanced in a very subjective manner the content from several specifications ([Document Object Model (DOM) Level 3 Core Specification](http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/core.html), [DOM4](http://www.w3.org/TR/dom/), [Document Object Model HTML](http://www.w3.org/TR/2003/REC-DOM-Level-2-HTML-20030109/html.html), [Element Traversal Specification](http://www.w3.org/TR/ElementTraversal/), [Selectors API Level 2](http://www.w3.org/TR/selectors-api2/), [DOM Parsing and Serialization](http://html5.org/specs/dom-parsing.html), [HTML 5 Reference](http://dev.w3.org/html5/html-author/), [HTML 5 - A vocabulary and associated APIs for HTML and XHTML](http://www.w3.org/TR/html5/), [HTML Living Standard](http://www.whatwg.org/specs/web-apps/current-work/multipage/), [HTML 5 - A technical specification for Web Developers](http://developers.whatwg.org/), [DOM Living Standard](http://dom.spec.whatwg.org/)). The content for this book is based more on where the community is and less on dogmatically attempting to express a specific spec.
- I'm covering several hand picked topics that are not DOM specific. I've included these topics in this book to help the reader build a proper understanding of the DOM in relationship to CSS and JavaScript.
- I've purposely left out any details as it pertains to XML or XHTML.
- I've purposely excluded the form and table api's to keep the book small. But I can see these sections being added in the future.

## License

The DOM Enlightenment HTML version is released under a [Creative Commons Attribution-Noncommercial-No Derivative Works 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/) unported license.

## Hard Copy & eBook

[O'Reilly](http://oreilly.com/) will release and sell a hard copy & eBook in the near future.

## Preface

Before you begin, it is important to understand various styles employed in this book. Please do not skip this section, because it contains important information that will aid you in the unique styles of this book.

## This book is not like other programming books

The enlightenment series ([jQuery Enlightenment](http://jqueryenlightenment.com/) & [JavaScript Enlightenment](http://javascriptenlightenment.com/)) is written in a style that favors small, isolated, immediately executable code over wordy explanations and monolithic programs. One of my favorite authors, C.S Lewis, asserts that words are the lowest form of communication that humans traffic in. I totally agree with this assertion and use it as the basis for the style of these books. I feel that technical information is best covered with as few words as possible, in conjunction with just the right amount of executable code and commenting required to express an idea. The style of this book attempts to present a clearly defined idea with as few words as possible, backed with real code. Because of this, when you first start grokking these concepts, you should execute and examine the code, thereby forming the foundation of a mental model for the words used to describe the concepts. Additionally, the format of these books attempts to systematically break ideas down into their smallest possible form and examine each one in an isolated context. All this to say that this is not a book with lengthy explanations or in-depth coverage on broad topics. Consider yourself warned. If it helps, think of it as a cookbook, but even more terse and to the point than usual.

## Color-coding conventions

In the code examples (example shown below), orange is used to highlight code directly relevant to the concept being discussed. Any additional code used to support the orange colored code will be green. The color gray in the code examples is reserved for comments.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

// this is a comment about a specific part of the code
var foo = 'calling out this part of the code';

</script> 
</body>
</html>
```

## jsFiddle

The majority of code examples in this book are linked to a corresponding [jsFiddle](http://jsfiddle.net/) page, where the code can be tweaked and executed online. The jsFiddle examples have been configured to use the [Firebug lite-dev plugin](https://getfirebug.com/firebug-lite-debug.js) to ensure the reader views the console.log prevalent in this book. Before reading this book, make sure you are comfortable with the usage and purpose of _console.log_.

In situations where jsFiddle caused complications with the code example, I simply chose not to link to a live example.

## About the Author

[Cody Lindley](http://www.codylindley.com) is a client-side engineer (aka front-end developer) and recovering Flash developer. He has an extensive background working professionally (11+ years) with HTML, CSS, JavaScript, Flash, and client-side performance techniques as it pertains to web development. If he is not wielding client-side code he is likely toying with interface/interaction design or authoring material and speaking at various conferences. When not sitting in front of a computer, it is a sure bet he is hanging out with his wife and kids in Boise, Idaho – training for triathlons, skiing, mountain biking, road biking, alpine climbing, reading, watching movies, or debating the rational evidence for a Christian worldview.

## Table of Contents

## Chapter 1 - Node Overview

## 1.1 The Document Object Model (aka the DOM) is a hierarchy/tree of JavaScript node objects

When you write an HTML document you encapsulate HTML content inside of other HTML content. By doing this you setup a hierarchy that can be expressed as a [tree](http://dvcs.w3.org/hg/domcore/raw-file/tip/Overview.html#trees). Often this hierarchy or encapsulation system is indicated visually by indenting markup in an HTML document. The browser when loading the HTML document interrupts and [parses this hierarchy to create a tree of node objects](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Parsing_general) that simulates how the markup is encapsulated.

```
<!DOCTYPE html>
<html lang="en">
<head>
<title>HTML</title>
</head>
<body>
<!-- Add your content here-->
</body>
</html>
```

The above HTML code when parsed by a browser creates a document that contains nodes structrured in a tree format (i.e. DOM). Below I reveal the tree struture from the above HTML document using Opera's Dragonfly DOM inspector.

![](http://domenlightenment.com/images/c1sec1.png)

On the left you see the HTML document in its tree form. And on the right you see the corresponding JavaScript object that represents the selected element on the left. For example, the selected _<body>_ element highlighted in blue, is an element node and an instance of the _HTMLBodyElement_ interface.

What you should take away here is that html documents get parsed by a browser and converted into a tree structure of node objects representing a live document. The purpose of the DOM is to provide a programatic interface for scripting (removing, adding, replacing, eventing, modifiying) this live document using JavaScript.

### Notes

The DOM originally was an application programming interface for XML documents that has been extended for use in HTML documents.

## 1.2 Node object types

The most common (I'm not highlighting all of them in the list below) types of nodes (i.e. _nodeType_/node classifications) one encounters when working with HTML documents are listed below.

- _DOCUMENT_NODE_ (e.g. _window.document_)
- _ELEMENT_NODE_ (e.g. _<body>_, _<a>_, _<p>_, _<script>_, _<style>_, _<html>_, _<h1>_ etc...)
- _ATTRIBUTE_NODE_ (e.g. _class="funEdges"_)
- _TEXT_NODE_ (e.g. text characters in an html document including carriage returns and white space)
- _DOCUMENT_FRAGMENT_NODE_ (e.g. _document.createDocumentFragment()_)
- _DOCUMENT_TYPE_NODE_ (e.g. _<!DOCTYPE html>_)

I've listed the node types above formatted (all uppercase with _ separating words) exactly as the constant property is written in the JavaScript browser environment as a property of the _Node_ object. These _Node_ properties are constant values and are used to store numeric code values which map to a specific type of node object. For example in the following code example, _Node.ELEMENT_NODE_ is equal to _1_. And _1_ is the code value used to identify element nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

console.log(Node.ELEMENT_NODE) //logs 1, one is the numeric code value for element nodes

</script>
</body>
</html>
```

In the code below I log all of the node types and there values.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

for(var key in Node){
    console.log(key,' = '+Node[key]); 
};

/* the above code logs to the console the following
ELEMENT_NODE = 1
ATTRIBUTE_NODE = 2
TEXT_NODE = 3
CDATA_SECTION_NODE = 4
ENTITY_REFERENCE_NODE = 5
ENTITY_NODE = 6
PROCESSING_INSTRUCTION_NODE = 7
COMMENT_NODE = 8
DOCUMENT_NODE = 9
DOCUMENT_TYPE_NODE = 10
DOCUMENT_FRAGMENT_NODE = 11
NOTATION_NODE = 12
DOCUMENT_POSITION_DISCONNECTED = 1
DOCUMENT_POSITION_PRECEDING = 2
DOCUMENT_POSITION_FOLLOWING = 4
DOCUMENT_POSITION_CONTAINS = 8
DOCUMENT_POSITION_CONTAINED_BY = 16
DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC = 32 */

</script>
</body>
</html>
```

The previous code example gives an exhaustive list of all node types. For the purpose of this book I'll be discussing the shorter list of node types listed at the start of this section. These nodes will most likely be the ones you come in contact with when scripting an HTML page.

In the table below I list the name given to the interface/constructor that instantiates the most common node types and their corresponding _nodeType_ classification by number and name. What I hope you take away from the table below is the _nodeType_ value (i.e. _1_) is just a numeric classificaiton used to describe a certain type of node constructed from a certain JavaScript interface/constructor. For example, the _HTMLBodyElement_ interface reprsents a node object that has a node type of _1_, which is a classification for _ELEMENT_NODE_'s.

### Notes

The DOM specification semantically labels nodes like _Node_, _Element_, _Text_, _Attr_, and _HTMLAnchorElement_ as an interface, which it is, but keep in mind its also the name given to the JavaScript constructor function that constructs the nodes. As you read this book I will be referring to these interfaces (i.e. _Element_, _Text_, _Attr_, _HTMLAnchorElement_) as objects or constructor functions while the specification refers to them as interfaces.

The _ATTRIBUTE_NODE_ is not actually part of a tree but listed for historical reasons. In this book I do not provide a chapter on attribute nodes and instead discuss them in the _Element_ node chapter given that attributes nodes are sub-like nodes of element nodes with no particiipation in the actual DOM tree structure. Be aware the ATTRIBUTE_NODE is being depreciated in DOM 4.

I've not included detail in this book on the _COMMENT_NODE_ but you should be aware that comments in an HTML document are _Comment_ nodes and similar in nature to _Text_ nodes.

As I discuss nodes throughout the book I will rarely refer to a specific node using its _nodeType_ name (e.g. _ELEMENT_NODE_). This is done to be consistent with verbiage used in the specifications provided by the W3C & WHATWG.

## 1.3 Sub-node objects inherit from the _Node_ object

Each node object in a typical DOM tree inherits properties and methods from _Node_. Depending upon the type of node in the document there are also additional sub node object/interfaces that extend the _Node_ object. Below I detail the inheritance model implemented by browsers for the most common node interfaces (< indicates inherited from).

- _Object_ < _Node_ < _Element_ < _HTMLElement_ < (e.g. _HTML\_Element*)
- _Object_ < _*_ Node _<_ Attr* (This is deprecated in DOM 4)
- _Object_ < _*_ Node _<_ CharacterData _<_ Text*
- _Object_ < _*_ Node _<_ Document _<_ HTMLDocument*
- _Object_ < _*_ Node _<_ DocumentFragment*

Its important not only to remember that all nodes types inherit from _Node_ but that the chain of inheritance can be long. For example, all _HTMLAnchorElement_ nodes inherit properties and methods from _HTMLElement_, _Element_, _Node_, and _Object_ objects.

### Notes

_Node_ is just a JavaScript constructor function. And so logically _Node_ inherits from _Object.prototype_ just like all objects in JavaScript

To verify that all node types inherit properties & methods from the _Node_ object lets loop over an _Element_ node object and examine its properties and methods (including inherited).

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#">Hi</a> <!-- this is a HTMLAnchorElement which inherits from... -->

<script>

//get reference to element node object
var nodeAnchor = document.querySelector('a');

//create props array to store property keys for element node object
var props = [];

//loop over element node object getting all properties & methods (inherited too)
for(var key in nodeAnchor){
    props.push(key);   
}

//log alphabetical list of properties & methods 
console.log(props.sort());

</script>
</body>
</html>
```

If you run the above code in a web browser you will see a long list of properties that are available to the element node object. The properties & methods inherited from the _Node_ object are in this list as well as a great deal of other inherited properties and methods from the _Element_, _HTMLElement_, _HTMLAnchorElement_, _Node_, and _Object_ object. Its not my point to examine all of these properties and methods now but simply to point out that all nodes inherit a set of baseline properties and methods from its constructor as well as properties from the prototype chain.

If you are more of visual learner consider the inheritance chain denoted from examining the above HTML document with Opera's DOM inspector.

![](http://domenlightenment.com/images/c1sec3.png) Notice that the anchor node inherits from _HTMLAnchorElement_, _HTMLElement_, _Element_, _Node_, and _Object_ all shown in the list of properties highlighted with a gray background. This inheritance chain provides a great deal of shared methods and properties to all node types.

## 1.4 Properties and methods for working nodes

Like we have been discussing all node objects (e.g _Element_, _Attr_, _Text_ etc...) inherit properties and methods from a primary _Node_ object. These properties and methods are the baseline values and functions for manipulating, inspecting, and traversing the DOM. In addtion to the properties and methods provided by the node interface there are a great deal of other relevant properties and methods that are provided by sub node interfaces such as the _document_, _HTMLElement_, or _HTML\_Element* interface.

Below I list out the most common _Node_ properties and methods inherited by all node objects including the relevant inherited properties for working with nodes from sub-node interfaces.

Node Properties:

- _childNodes_
- _firstChild_
- _lastChild_
- _nextSibling_
- _nodeName_
- _nodeType_
- _nodeValue_
- _parentNode_
- _previousSibling_

Node Methods:

- _appendChild()_
- _cloneNode()_
- _compareDocumentPosition()_
- _contains()_
- _hasChildNodes()_
- _insertBefore()_
- _isEqualNode()_
- _removeChild()_
- _replaceChild()_

Document Methods:

- _document.createElement()_
- _document.createTextNode()_

HTML 10_misconceptions_about_neural_networks.md a_developer's_journey_into_linux_containers.md aerofsgockerize.md andrewchildsovercast.md an_introduction_to_visual_programming_using_noflo.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md a_valid_ssl_certificate_for_every_ip_address__pivotal_pov.md a_walk_through_the_sais_algorithm__screwtapes_notepad.md b2_cloud_storage.md better_logging_in_nodejs_using_bunyan.md binaryjs.md brief_survey_on_methods_for_attacking_tor_hidden_service.md building_frontend_web_apps_with_plain_javascript.md checkpoint_and_restore_docker_container_with_criu.md containers_101.md cooking_with_docker_and_coreos_on_os_x.md coreos__kubernetes_step_by_step.md create_a_rest_api_with_nodejs.md create_your_own_streaming_service_with_emby.md cryptdbcryptdb.md cyrusandgdbdashboard.md dask__parallel_processing_through_blocked_algorithms¶.md data data.json delightimfreegeodb.md develop_command_line_application_using_nodejs.md digitaloceangodo.md document_clustering_with_python.md dosa_by_shon.md dosnapshot.md economic_secrets_of_the_dark_web--the_safe_easy_way_for_anyone_to_be_a_little_drug_lord.md eivind88prm.md even_easier_restful_api_with_nodejs_and_express_framework.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flaskpotion.md flowchartjs.md free_ssltls_certificates.md gernestutron.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md google_project_hosting.md grammarlyrockercompose.md hacker_codex.md headroomjs.md how_can_i_repair_my_hard_drive_if_my_mac_won't_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md icodeforlovedoapi.md imjasonhghfs.md install_sandstorm.md introducing_lemur.md introduction__building_web_apps_with_go.md introduction__getting_started_guide.md introduction.md introduction_to_monte_carlo_tree_search.md intro_to_webgl_with_threejs.md jamiehdigitaloceancsharp.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md json_lines.md jssequencediagrams.md just_a_theory.md keendashboards.md koalalorenzopythondigitalocean.md launching_nginscript_and_looking_ahead__nginx.md let's_write_a_web_extension.md let_your_applications_fly.md links_list.txt mangoslideout.md many_js_frameworks_but_vuejs_is_different.md mateogianoliosshync.md md meteor_react_tutorial_updated__match_made_in_heaven.md micc83edittable.md mistiomistio.md natural_language_basics_with_textblob.md null.md open_source_slackalternative_adopts_markdown.md openvswitchovs.md opsing_with_packer_and_terraform.md parse_urls_json.sh png prototype_interaction_and_animation.md pyra_full_prototypes_soon.md quintus_javascript_html5_game_engine.md reclaiming_a_timemachine_volume's_disk_space.md resilient_substructure_for_your_microservice.md selzplyr.md setting_up_nginx_with_docker.md silentbicycleguff.md smdahlenvagrantdigitalocean.md snapcraft_tutorial.md sqljs.md storejs.md tgriesserbookshelf.md the_incomplete_collection_of_nodejs_performance_tips_--_node_and_beyond.md the_javascript_library_for_modern_browsers_and_touch_devices_no jquery.md the_javascript_tutorial.md the_math_i_learned_after_i_thought_had_already_learned_math.md the_new_art_of_war_how_trolls_hackers_and_spies_are_rewriting_the_rules_of conflict.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker's_projects.md ˈtræfɪk_·_ˈtræfɪk.md tugboat.md url_to_filename.csv using_an_offline_gnupg_master_key.md using_feathersjs_as_an_open_source_alternative_to_firebase_--_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md valerylisaydigitaloceanapi.md webxray.md whose_xterm_is_it_anyway.md wikiimunesexamples_–_imunes.md wmiradropkit.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md wso2_machine_learner_why_would_you care.md yahoogryffin.md yoannmoinetnipplejs.md zenit.md Element Properties:

- _innerHTML_
- _outerHTML_
- _textContent_
- _innerText_
- _outerText_
- _firstElementChild_
- _lastElementChild_
- _nextElementChild_
- _previousElementChild_
- _children_

HTML element Methods:

## 1.5 Identifying the type and name of a node

Every node has a _nodeType_ and _nodeName_ property that is inherited from _Node_. For example _Text_ nodes have a _nodeType_ code of _3_ and _nodeName_ value of _'#text'_. As previously mentioned the numeric value _3_ is a numeric code representing the type of underlying object the node represents (i.e. _Node.TEXT_NODE === 3_).

Below I detail the values returned for _nodeType_ and _nodeName_ for the node objects discussed in this book. It makes sense to simply memorize these numeric code's for the more common nodes given that we are only dealing with 5 numeric codes.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#">Hi</a>

<script>

//This is DOCUMENT_TYPE_NODE or nodeType 10 because Node.DOCUMENT_TYPE_NODE === 10
console.log(
    document.doctype.nodeName, //logs 'html' also try document.doctype to get <!DOCTYPE html>
    document.doctype.nodeType //logs 10 which maps to DOCUMENT_TYPE_NODE
);

//This is DOCUMENT_NODE or nodeType 9 because Node.DOCUMENT_NODE === 9
console.log(
    document.nodeName, //logs '#document'
    document.nodeType //logs 9 which maps to DOCUMENT_NODE
);

//This is DOCUMENT_FRAGMENT_NODE or nodeType 11 because Node.DOCUMENT_FRAGMENT_NODE === 11
console.log(
    document.createDocumentFragment().nodeName, //logs '#document-fragment'
    document.createDocumentFragment().nodeType //logs 11 which maps to DOCUMENT_FRAGMENT_NODE
);

//This is ELEMENT_NODE or nodeType 1 because Node. ELEMENT_NODE === 1
console.log(
    document.querySelector('a').nodeName, //logs 'A'
    document.querySelector('a').nodeType //logs 1 which maps to ELEMENT_NODE
);

//This is TEXT_NODE or nodeType 3 because Node.TEXT_NODE === 3
console.log(
    document.querySelector('a').firstChild.nodeName, //logs '#text'
    document.querySelector('a').firstChild.nodeType //logs 3 which maps to TEXT_NODE
);

</script>
</body>
</html>
```

If its not obvious the fastest way to determine if a node is of a certain type is too simply check its _nodeType_ property. Below we check to see if the anchor element has a node number of 1\. If it does than we can conclude that its an _Element_ node because _Node.ELEMENT_NODE === 1_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#">Hi</a>

<script>

//is <a> a ELEMENT_NODE?
console.log(document.querySelector('a').nodeType === 1); //logs true, <a> is an Element node

//or use Node.ELEMENT_NODE which is a property containg the numerice value of 1
console.log(document.querySelector('a').nodeType === Node.ELEMENT_NODE); //logs true, <a> is an Element node

</script>
</body> 
</html>
```

Determining the type of node that you might be scripting becomes very handy so that you might know which properties and methods are available to script the node.

### Notes

The values returned by the _nodeName_ property vary according to the node type. Have a look at the [DOM 4 specification](http://www.w3.org/TR/dom/#dom-node-nodename) provided for the details.

## 1.6 Getting a nodes value

The _nodeValue_ property returns _null_ for most of the node types (except _Text_, and _Comment_). It's use is centered around extracting actual text strings from _Text_ and _Comment_ nodes. In the code below I demonstrate its use on all the nodes discussed in this book

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#">Hi</a>

<script>

//logs null for DOCUMENT_TYPE_NODE, DOCUMENT_NODE, DOCUMENT_FRAGMENT_NODE, ELEMENT_NODE below
console.log(document.doctype.nodeValue);
console.log(document.nodeValue);
console.log(document.createDocumentFragment().nodeValue);
console.log(document.querySelector('a').nodeVale);

//logs string of text
console.log(document.querySelector('a').firstChild.nodeValue); //logs 'Hi'

</script>
</body>
</html>
```

### Notes

_Text_ or _Comment_ node values can be set by providing new strings values for the _nodeValue_ property(i.e. _document.body.firstElementChild.nodeValue = 'hi'_).

## 1.7 Creating element and text nodes using JavaScript methods

When a browser parses an HTML document it constructs the nodes and tree based on the contents of the HTML file. The browser deals with the creation of nodes for the intial loading of the HTML document. However its possible to create your own nodes using JavaScript. The following two methods allow us to programatically create _Element_ and _Text_ nodes using JavaScript.

- _createElement()_
- _createTextNode()_

Other methods are avaliable but are not commonly used (e.g. _createAttribute()_ and _createComment()_) . In the code below I show how simple it is to create element and text nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

var elementNode = document.createElement('div');
console.log(elementNode, elementNode.nodeType); //log <div> 1, and 1 indicates an element node

var textNode = document.createTextNode('Hi');
console.log(textNode, textNode.nodeType); //logs Text {} 3, and 3 indicates a text node

</script>
</body>
</html>
```

### Notes

The _createElement()_ method accepts one parameter which is a string specifying the element to be created. The string is the same string that is returned from the _tagName_ property of an _Element_ object.

The _createAttribute()_ method is depricated and should not be used for creating attribute nodes. Instead developers typically use _getAttribute()_, _setAttribute()_, and _removeAttribute()_ methods. I will discus this in more detail in the _Element_ node chapter.

The _createDocumentFragment()_ will be discussed in the chapter covering this method.

You should be aware that a _createComment()_ method is available for creating comment nodes. Its not discussed in this book but is very much available to a developer who finds its usage valuable.

## 1.8 Creating and adding element and text nodes to the DOM using JavaScript strings

The _innerHTML_, _outerHTML_, _textContent_ and _insertAdjacentHTML()_ properties and methods provide the functionality to create and add nodes to the DOM using JavaScript strings.

In the code below we are using the _innerHTML_, _outerHTML_, and _textContent_ properties to create nodes from JavaScript strings that are then immediately added to the DOM.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div id="A"></div>
<span id="B"></span>
<div id="C"></div>
<div id="D"></div>
<div id="E"></div>

<script>

//create a strong element and text node and add it to the DOM
document.getElementById('A').innerHTML = '<strong>Hi</strong>'; 

//create a div element and text node to replace <span id="B"></div> (notice span#B is replaced)
document.getElementById('B').outerHTML = '<div id="B" class="new">Whats Shaking</div>'

//create a text node and update the div#C with the text node
document.getElementById('C').textContent = 'dude';


//NON standard extensions below i.e. innerText & outerText

//create a text node and update the div#D with the text node
document.getElementById('D').innerText = 'Keep it';

//create a text node and replace the div#E with the text node (notice div#E is gone)
document.getElementById('E').outerText = 'real!';

console.log(document.body.innerHTML);
/* logs
<div id="A"><strong>Hi</strong></div>
<div id="B" class="new">Whats Shaking</div>
<span id="C">dude</span>
<div id="D">Keep it</div>
real!
*/

</script>
</body>
</html>
```

The _insertAdjacentHTML()_ method which only works on _Element_ nodes is a good deal more precise than the previously mentioned methods_*. Using this method its possible to insert nodes before the beginning tag, after the beginning tag, before the end tag, and after the end tag. Below I construct a sentence using the_ insertAdjacentHTML()* method.

```
<!DOCTYPE html>
<html lang="en">
<body><i id="elm">how</i>

<script>

var elm = document.getElementById('elm');

elm.insertAdjacentHTML('beforebegin', '<span>Hey-</span>');
elm.insertAdjacentHTML('afterbegin', '<span>dude-</span>'); 
elm.insertAdjacentHTML('beforeend', '<span>-are</span>'); 
elm.insertAdjacentHTML('afterend', '<span>-you?</span>');  

console.log(document.body.innerHTML);
/* logs
<span>Hey-</span><i id="A"><span>dude-</span>how<span>-are</span></i><span>-you?</span>
*/

</script>
</body>
</html>
```

### Notes

The _innerHTML_ property will convert html elements found in the string to actual DOM nodes while the _textContent_ can only be used to construct text nodes. If you pass _textContent_ a string containing html elements it will simply spit it out as text.

_document.write()_ can also be used to simultaneously create and add nodes to the DOM. However its typically not used anymore unless its usage is required to accomplish 3rd party scripting tasks. Basically the _write()_ method will output the values passed to it into the page during page loading/parsing. You should be aware that using the _write()_ method will stall/block the parsing of the html document being loaded.

_innerHTML_ invokes a heavy & expensive HTML parser where as text node generation is trivial thus use the innerHTML & friends sparingly

The _insertAdjacentHTML_ options "beforebegin" and "afterend" will only work if the node is in the DOM tree and has a parent element.

Support for _outerHTML_ was not available natively in Firefox until version 11\. A [polyfill](https://gist.github.com/1044128) is avaliable.

_textContent_ gets the content of all elements, including _<script>_ and _<style>_ elements, _innerText_ does not

_innerText_ is aware of style and will not return the text of hidden elements, whereas _textContent_ will

Avaliable to all modern browser except Firefox is _insertAdjacentElement()_ and _insertAdjacentText()_

## 1.9 Extracting parts of the DOM tree as JavaScript strings

The same exact properties (_innerHTML_, _outerHTML_, _textContent_) that we use to create and add nodes to the DOM can also be used to extract parts of the DOM (or really the entire DOM) as a JavaScript string. In the code example below I use these properties to return a string value containing text and html values from the HTML document.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div id="A"><i>Hi</i></div>
<div id="B">Dude<strong> !</strong></div>

<script>

console.log(document.getElementById('A').innerHTML); //logs '<i>Hi</i>'console.log(document.getElementById('A').outerHTML); //logs <div id="A">Hi</div>

//notice that all text is returned even if its in child element nodes (i.e. <strong> !</strong>) 
console.log(document.getElementById('B').textContent); //logs 'Dude !'

//NON standard extensions below i.e. innerText & outerText

console.log(document.getElementById('B').innerText); //logs 'Dude !'console.log(document.getElementById('B').outerText); //logs 'Dude !'​​

</script>
</body>
</html>
```

### Notes

The _textContent_, _innerText_, _outerText_ property when being read will return all of the text nodes contained within the selected node. So for example (not a good idea in practice), _document.body.textContent_ will get all the text nodes contained in the body element not just the first text node.

## 1.10 Adding node objects to the DOM using _appendChild()_& _insertBefore()_

The _appendChild()_ and _insertBefore()_ _Node_ methods allow us to insert JavaScript node objects into the DOM tree. The _appendChild()_ method will append a node(s) to the end of the child node(s) of the node the method is called on. If there are no child node(s) then the node being appended is appended as the first child. For example in the code below we are creating a element node (_<strong>_) and text node (_Dude_). Then the _<p>_ is selected from the DOM and our _<strong>_ element is appended using _appendChild()_. Notice that the _<strong>_ element is encapsulated inside of the _<p>_ element and added as the last child node. Next the _<strong>_ element is selected and the text _'Dude'_ is appended to the _<strong>_ element.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p>Hi</p>

<script>

//create a blink element node and text node
var elementNode = document.createElement('strong');
var textNode = document.createTextNode(' Dude');

//append these nodes to the DOM
document.querySelector('p').appendChild(elementNode);
document.querySelector('strong').appendChild(textNode);

//log's <p>Hi<strong> Dude</strong></p>
console.log(document.body.innerHTML);

</script>
</body>
</html>
```

When it becomes necessary to control the location of insertion beyond appending nodes to the end of a child list of nodes we can use _insertBefore()_. In the code below I am inserting the _<li>_ element before the first child node of the _<ul>_ element.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
    <li>2</li>
    <li>3</li>
</ul>

<script>

//create a text node and li element node and append the text to the li
var text1 = document.createTextNode('1');
var li = document.createElement('li');
li.appendChild(text1);

//select the ul in the document
var ul = document.querySelector('ul');

/* 
add the li element we created above to the DOM, notice I call on <ul> and pass reference to <li>2</li> using ul.firstChild 
*/
ul.insertBefore(li,ul.firstChild);

console.log(document.body.innerHTML);
/*logs
<ul>
<li>1</li>
<li>2</li>
<li>3</li>
</ul>
*/

</script>
</body>
</html>
```

The _insertBefore()_ requires two parameters, the node to be inserted and the reference node in the document you would like the node inserted before.

### Notes

If you do not pass the _insertBefore()_ method a second parameter then it functions just like _appendChild()_.

We have [more methods](http://www.w3.org/TR/dom/#mutation-methods) (e.g. _prepend()_, _append()_, _before()_, _after()_) to look forward too in DOM 4.

## 1.11 Removing and replacing nodes using _removeChild()_ and _replaceChild()_

Removing a node from the DOM is a bit of a multi-step process. First you have to select the node you want to remove. Then you need to gain access to its parent element typically using the _parentNode_ property. Its on the parent node that you invoke the _removeChild()_ method passing it the reference to the node to be removed. Below I demonstrate its use on an element node and text node.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div id="A">Hi</div>
<div id="B">Dude</div>

<script>

//remove element node
var divA = document.getElementById('A');
divA.parentNode.removeChild(divA);

//remove text node
var divB = document.getElementById('B').firstChild;
divB.parentNode.removeChild(divB);

//log the new DOM updates, which should only show the remaining empty div#B
console.log(document.body.innerHTML);

</script>
</body>
</html>
```

Replacing an element or text node is not unlike removing one. In the code below I use the same html structure used in the previous code example except this time I use _replaceChild()_ to update the nodes instead of removing them.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div id="A">Hi</div>
<div id="B">Dude</div>

<script>

//replace element node
var divA = document.getElementById('A');
var newSpan = document.createElement('span');
newSpan.textContent = 'Howdy';
divA.parentNode.replaceChild(newSpan,divA);

//replace text node
var divB = document.getElementById('B').firstChild;
var newText = document.createTextNode('buddy');
divB.parentNode.replaceChild(newText, divB);

//log the new DOM updates, 
console.log(document.body.innerHTML);

</script>
</body>
</html>
```

### Notes

Depending upon what you are removing or replacing simply providing the _innerHTML_, _outerHTML_, and _textContent_ properties with an empty string might be easier and faster. [Careful memory leaks in brwoser might get you however](http://javascript.crockford.com/memory/leak.html).

Both _replaceChild()_ and _removeChild()_ return the replaced or remove node. Basically its not gone just because you replace it or remove. All this does is takes it out of the current live document. You still have a reference to it in memory.

We have [more methods](http://www.w3.org/TR/dom/#mutation-methods) (e.g._replace()_, _remove()_) to look forward too in DOM 4.

## 1.12 Cloning nodes using _cloneNode()_

Using the _cloneNode()_ method its possible to duplicate a single node or a node and all its children nodes.

In the code below I clone only the _<ul>_ (i.e. _HTMLUListElement)_ which once cloned can be treated like any node reference.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
 <li>Hi</li>
 <li>there</li>
</ul>

<script>

var cloneUL = document.querySelector('ul').cloneNode();

console.log(cloneUL.constructor); //logs HTMLUListElement()
console.log(cloneUL.innerHTML); //logs (an empty string) as only the ul was cloned

</script>
</body>
</html>
```

To clone a node and all of its children nodes you pass the _cloneNode()_ method a parameter of of _true_. Below I use the _cloneNode()_ method again but this time we clone all of the child nodes as well.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
 <li>Hi</li>
 <li>there</li>
</ul>

<script>

var cloneUL = document.querySelector('ul').cloneNode(true);

console.log(cloneUL.constructor); //logs HTMLUListElement()
console.log(cloneUL.innerHTML); //logs <li>Hi</li><li>there</li>

</script>
</body>
</html>
```

### Notes

When cloning an _Element_ node all attributes and values are also cloned. In fact, only attributes are copied! Everything else you can add (e.g. event handlers) to a DOM node is lost when cloning.

You might think that cloning a node and its children using _cloneNode(true)_ would return a _NodeList_ but it in fact does not.

_cloneNode()_ may lead to duplicate element IDs in a document

## 1.13 Grokking node collections (i.e. _Nodelist_ & _HTMLcollection_)

When selecting groups of nodes from a tree (cover in chaper 3) or accessing pre-defined sets of nodes, the nodes are either placed in a [_NodeList_](http://www.w3.org/TR/dom/#nodelist) (e.g. _document.querySelectorAll('\_')_) or [_HTMLCollection_](<http://www.w3.org/TR/dom/#htmlcollection>) (e.g._ document.scripts_). These array like (i.e. not a real_ Array*) object collections that have the following characteristics.

- A collection can either be live or static. Meaning that the nodes contained in the collection are either literally part of the live document or a snapshot of the live document.
- By default nodes are sorted inside of the collection by tree order. Meaning the order matches the liner path from tree trunk to branches.
- The collections have a _length_ property that reflects the number of elements in the list

## 1.14 Gettting a list/collection of all immediate child nodes

Using the _childNodes_ property produces an array like list (i.e. [NodeList](https://developer.mozilla.org/En/DOM/NodeList)) of the immediate child nodes. Below I select the _<ul>_ element which I then use to create a list of all of the immediate child nodes contained inside of the _<ul>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
 <li>Hi</li>
 <li>there</li>
</ul>

<script>

var ulElementChildNodes = document.querySelector('ul').childNodes;

console.log(ulElementChildNodes); //logs an array like list of all nodes inside of the ul

/*Call forEach as if its a method of NodeLists so we can loop over the NodeList. Done because NodeLists are array like, but do not directly inherit from Array*/
Array.prototype.forEach.call(ulElementChildNodes,function(item){ 
   console.log(item); //logs each item in the array
}); 

</script>
</body>
</html>
```

### Notes

The _NodeList_ returned by _childNodes_ only contains immediate child nodes

Be aware _childNodes_ contains not only _Element_ nodes but also all other node types (e.g. _Text_ and _Comment_ nodes)

_[].forEach_ was implemented in ECMAScript 5th edtion

## 1.15 Convert a _NodeList_ or _HTMLCollection_ to JavaScript _Array_

Node lists and html collections are array like but not a true JavaScript array which inherits array methods. In the code below we programtically confirm this using _isArray()_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#"></a>

<script>

console.log(Array.isArray(document.links)); //returns false, its an HTMLCollection not an Array
console.log(Array.isArray(document.querySelectorAll('a'))); //returns false, its an NodeList not an Array

</script>
</body>
</html>
```

### Notes

_Array.isArray_ was implemented in ECMAScript 5th edtion or ES5

Converting a node list and html collection list to a true JavaScript array can provide a good deal of advantages. For one it gives us the ability to create a snapshot of the list that is not tied to the live DOM considering that _NodeList_ and _HTMLCollection_ are [live](http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/core.html#td-live) lists. Secondly, converting a list to a JavaScript array gives access to the methods provided by the _Array_ object (e.g. _forEach_, _pop_, _map_, _reduce_ etc...).

To convert an array like list to a true JavaScript array pass the array-like list to _call()_ or _apply()_, in which the _call()_ or _apply()_ is calling a method that returns an un-altered true JavaScript array. In the code below I use the _.slice()_ method, which doesn't really slice anything I am just using it to convert the list to a JavaScript _Array_ due to the fact the _slice()_ returns an array.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#"></a>

<script>

console.log(Array.isArray(Array.prototype.slice.call(document.links))); //returns true
console.log(Array.isArray(Array.prototype.slice.call(document.querySelectorAll('a')))); //returns true

</script>
</body>
</html>
```

### Notes

In ECMAScript 6th edtion we have _Array.from_ to look forward to which converts a single argument that is an array-like object or list (eg. _arguments_, _NodeList_, _DOMTokenList_ (used by _classList_), _NamedNodeMap_ (used by _attributes_ property)) into a _new Array()_ and returns it

## 1.16 Traversing nodes in the DOM

From a node reference (i.e. _document.querySelector('ul')_) its possible to get a different node reference by traversing the DOM using the following properties:

- _parentNode_
- _firstChild_
- _lastChild_
- _nextSibling_
- _previousSibling_

In the code example below we examine the _Node_ properties providing DOM traversal functionality.

```
<!DOCTYPE html>
<html lang="en">
<body><ul><!-- comment -->
<li id="A"></li>
<li id="B"></li>
<!-- comment -->
</ul>

<script>

//cache selection of the ul
var ul = document.querySelector('ul');

//What is the parentNode of the ul?
console.log(ul.parentNode.nodeName); //logs body

//What is the first child of the ul?
console.log(ul.firstChild.nodeName); //logs comment

//What is the last child of the ul?
console.log(ul.lastChild.nodeName); //logs text not comment, because there is a line break

//What is the nextSibling of the first li?
console.log(ul.querySelector('#A').nextSibling.nodeName); //logs text

//What is the previousSibling of the last li?
console.log(ul.querySelector('#B').previousSibling.nodeName); //logs text

</script>
</body>
</html>
```

If you have been around the DOM much then it should be no surprise that traversing the DOM includes not just traversing element nodes but also text and comment nodes. I believe the last code example makes this clear, and this is not exactly ideal. Using the following properties we can traverse the DOM ignoring text and comment nodes.

- _firstElementChild_
- _lastElementChild_
- _nextElementChild_
- _previousElementChild_
- _children_

### Notes

The _childElementCount_ is not mentioned but you should be aware of its avaliablity for calculating the number of child elements a node contains.

Examine our code example again using only element traversing methods.

```
<!DOCTYPE html>
<html lang="en">
<body><ul><!-- comment -->
<li id="A"></li>
<li id="B"></li>
<!-- comment -->
</ul>

<script>

//cache selection of the ul
var ul = document.querySelector('ul');

//What is the first child of the ul?
console.log(ul.firstElementChild.nodeName); //logs li

//What is the last child of the ul?
console.log(ul.lastElementChild.nodeName); //logs li

//What is the nextSibling of the first li?
console.log(ul.querySelector('#A').nextElementSibling.nodeName); //logs li

//What is the previousSibling of the last li?
console.log(ul.querySelector('#B').previousElementSibling.nodeName); //logs li

//What are the element only child nodes of the ul? 
console.log(ul.children); //HTMLCollection, all child nodes including text nodes

</script>
</body>
</html>
```

## 1.17 Verify a node position in the DOM tree with _contains()_ & _compareDocumentPosition()_

Its possible to know if a node is contained inside of another node by using the _contains()_ _Node_ method. In the code below I ask if _<body>_ is contained inside of _<html lang="en">_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<script>

// is <body> inside <html lang="en"> ?
var inside = document.querySelector('html').contains(document.querySelector('body'));

console.log(inside); //logs true

</script>
</body>
</html>
```

If you need more robust information about the position of a node in the DOM tree in regards to the nodes around it you can use the _compareDocumentPosition()_ _Node_ method. Basically this method gives you the ability to request information about a selected node relative to the node passed in. The information that you get back is a number that corresponds to the following information.

number code returned from _compareDocumentPosition()_: | number code info:
------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------
0                                                      | Elements are identical.
1                                                      | DOCUMENT_POSITION_DISCONNECTED<br>
Set when selected node and passed in node are not in the same document.
2                                                      | DOCUMENT_POSITION_PRECEDING<br>
Set when passed in node is preceding selected node.
3                                                      | DOCUMENT_POSITION_FOLLOWING<br>
Set when passed in node is following selected node.
8                                                      | DOCUMENT_POSITION_CONTAINS<br>
Set when passed in node is an ancestor of selected node.
16, 10                                                 | DOCUMENT_POSITION_CONTAINED_BY (16, 10 in hexadecimal)<br>
Set when passed in node is a descendant of selected node.

### Notes

_contains()_ will return _true_ if the node selected and node passed in are identical.

_compareDocumentPosition()_ can be rather confusing because its possible for a node to have more than one type of relationship with another node. For example when a node both contains (16) and precedes (4) the returned value from _compareDocumentPosition()_ will be 20.

## 1.18 How to determine if two nodes are identical

[According to the DOM 3 specification](http://www.w3.org/TR/DOM-Level-3-Core/core.html#Node3-isEqualNode) two nodes are equal if and only if the following conditions are satisfied:

- The two nodes are of the same type.
- The following string attributes are equal: _nodeName_, _localName_, _namespaceURI_, _prefix_, _nodeValue_. That is: they are both null, or they have the same length and are character for character identical.
- The _attributes_ _NamedNodeMaps_ are equal. That is: they are both _null_, or they have the same length and for each node that exists in one map there is a node that exists in the other map and is equal, although not necessarily at the same index.
- The _childNodes_ _NodeLists_ are equal. That is: they are both _null_, or they have the same length and contain equal nodes at the same index. Note that normalization can affect equality; to avoid this, nodes should be normalized before being compared.

Calling the _.isEqualNode()_ method on a node in the DOM will ask if that node is equal to the node that you pass it as a parameter. Below I exhibt a case of an two equal nodes and two non-identical nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>

<input type="text">
<input type="text">

<textarea>foo</textarea>
<textarea>bar</textarea>

<script>

//logs true, because they are exactly idential
var input = document.querySelectorAll('input');
console.log(input[0].isEqualNode(input[1]));

//logs false, because the child text node is not the same
var textarea = document.querySelectorAll('textarea');
console.log(textarea[0].isEqualNode(textarea[1]));

</script>
</body>
</html>
```

### Notes

If you don't care about two nodes being exactly equal but instead want to know if two node references refer to the same node you can simply check it using the _===_ opertor (i.e. _document.body === document.body_). This will tell us if they are identical but no equal.

## Chapter 2 - Document Nodes

## 2.1_document_node overview

The _HTMLDocument_ constructor (which inherits from _document_) when instantiated represents specifically a _DOCUMENT_NODE_ (i.e. _window.document_) in the DOM. To verify this we can simply ask which constructor was used in the creation of the _document_ node object.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

console.log(window.document.constructor); //logs function HTMLDocument() { [native code] }
console.log(window.document.nodeType); //logs 9, which is a numeric key mapping to DOCUMENT_NODE

</script>
</body>
</html>
```

The code above concludes that the _HTMLDocument_ constructor function constructs the _window.document_ node object and that this node is a _DOCUMENT_NODE_ object.

### Notes

Both _Document_ and _HTMLDocument_ constructors are typically instantiated by the browser when you load an HTML document. However, using _document.implementation.createHTMLDocument()_ its possible to create your own HTML document outside of the one currently loaded into the browser. In addtion to _createHTMLDocument()_ its also possible to create a document object which has yet to be setup as an HTML document using _createDocument()_. Typically the use of theses methods are associated with programatically providing an HTML document to an iframe.

## 2.2 _HTMLDocument_ properties and methods (including inherited)

To get accurate information pertaining to the available properties and methods on an _HTMLDocument_ node its best to ignore the specification and to ask the browser what is available. Examine the arrays created in the code below detailing the properties and methods available from an _HTMLDocument_ node (a.k.a. _window.document_) object.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

//document own properties
console.log(Object.keys(document).sort());

//document own properties & inherited properties
var documentPropertiesIncludeInherited = [];
for(var p in document){
    documentPropertiesIncludeInherited.push(p);
}
console.log(documentPropertiesIncludeInherited.sort());

//documment inherited properties only
var documentPropertiesOnlyInherited = [];
for(var p in document){
    if(
        !document.hasOwnProperty(p)){documentPropertiesOnlyInherited.push(p);
    }
}
console.log(documentPropertiesOnlyInherited.sort());

</script>
</body>
</html>
```

The available properties are many even if the inherited properties were not considered. Below I've hand pick a list of noteworthy properties and methods for the context of this chapter.

- _doctype_
- _documentElement_
- _implementation_.*
- _activeElement_
- _body_
- _head_
- _title_
- _lastModified_
- _referrer_
- _URL_
- _defaultview_
- _compatMode_
- _ownerDocument_
- _hasFocus()_

### Notes

The _HTMLDocument_ node object is used to access (typically inherit) a great deal of the methods and properties available for working with the DOM (i.e. _document.querySelectorAll()_). You will be seeing many of these properties not discussed in this chapter discussed in the appropriate chapter's following this chapter.

## 2.3 Getting general HTML document information (title, url, referrer, lastModified, compatMode)

The _document_ object provides access to some general information about the HTML document/DOM being loaded. In the code below I use the _document.title_, _document.URL_, _document.referrer_, _document.lastModified_, and _document.compatMode_ properties to gain some general information about the _document_. Based on the property name the returned values should be obvious.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

var d = document;
console.log('title = ' +d.title);
console.log('url = ' +d.URL);
console.log('referrer = ' +d.referrer);
console.log('lastModified = ' +d.lastModified);

//logs either BackCompat (Quirks Mode) or CSS1Compat (Strict Mode)
console.log('compatibility mode = ' +d.compatMode);

</script>
</body>
</html>
```

## 2.4 _document_ child nodes

_Document_ nodes can contain one _DocumentType_ node object and one _Element_ node object. This should not be a surprise since HTML documents typically contain only one doctype (e.g. _<!DOCTYPE html>_) and one element (e.g. _<html lang="en">_). Thus if you ask for the children (e.g. _document.childNodes_) of the _Document_ object you will get an array containing at the very least the documents doctype/DTD and _<html lang="en">_ element. The code below showcases that _window.document_ is a type of node object (i.e _Document_) with child nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

//This is the doctype/DTD
console.log(document.childNodes[0].nodeType); //logs 10, which is a numeric key mapping to DOCUMENT_TYPE_NODE

//This is the <html> element
console.log(document.childNodes[1].nodeType); //logs 1, which is a numeric key mapping to ELEMENT_TYPE_NODE

</script>
</body>
</html>
```

### Notes

Don't confuse the _window.document_ object created from _HTMLDocument_ constructor with the _Document_ object. Just remember _window.document_ is the starting point for the DOM interface. That is why _document.childNodes_ contains child nodes.

If a comment node (not discussed in this book) is made outside of the _<html lang="en">_ element then it will become a child node of the _window.document_. However having comment nodes outside of the <html> element can cause some buggy results in IE and also is a violation of the DOM specification.

## 2.5 _document_ provides shortcuts to _<!DOCTYPE>_, _<html lang="en">_, _<head>_, and _<body>_

Using the properties listed below we can get a shortcut reference to the following nodes:

- _document.doctype_ refers to _<!DOCTYPE>_ 
- _document.documentElement_ refers to _<html lang="en">_
- _document.head_ refers to _<head>_
- _document.body_ refers to _<body>_

This is demonstrated in the code below.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

console.log(document.doctype); // logs DocumentType {nodeType=10, ownerDocument=document, ...}

console.log(document.documentElement); // logs <html lang="en">

console.log(document.head); // logs <head>

console.log(document.body); // logs <body>

</script>

</body>
</html>
```

### Notes

The doctype or DTD is a _nodeType_ of 10 or _DOCUMENT_TYPE_NODE_ and should not be confused with the _DOCUMENT_NODE_ (aka _window.document_ constructed from _HTMLDocument()_). The doctype is constructed from the _DocumentType()_ constructor.

In Safari, Chrome, and Opera the _document.doctype_ does not appear in the _document.childNodes_ list.

## 2.6 Detecting DOM specifications/features using _document.implementation.hasFeature()_

Its possible using _document.implementation.hasFeature()_ to ask (boolean) the current document what feature and level the browser has implemented/supports. For example we can ask if the browser has implemented the core DOM level 3 specification by passing the name o
f the feature and the version to the _hasFeature()_ method. In the code below I ask if the browser has implemented the Core 2.0 & 3.0 specification.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

console.log(document.implementation.hasFeature('Core','2.0'));
console.log(document.implementation.hasFeature('Core','3.0')); 

</script>
</body>
</html>
```

The following table defines the features ([spec calls these modules](http://www.w3.org/TR/DOM-Level-2-Core/introduction.html#ID-Conformance)) and versions that you can pass the _hasFeature()_ method.

Feature                                                                 | Supported Versions
----------------------------------------------------------------------- | ------------------
Core                                                                    | 1.0, 2.0, 3.0
XML                                                                     | 1.0, 2.0, 3.0
HTML                                                                    | 1.0, 2.0
Views                                                                   | 2.0
StyleSheets                                                             | 2.0
CSS                                                                     | 2.0
CSS2                                                                    | 2.0
Events                                                                  | 2.0, 3.0
UIEvents                                                                | 2.0, 3.0
MouseEvents                                                             | 2.0, 3.0
MutationEvents                                                          | 2.0, 3.0
HTMLEvents                                                              | 2.0
Range                                                                   | 2.0
Traversal                                                               | 2.0
LS (Loading and saving between files and DOM trees synchronously)       | 3.0
LS-Asnc (Loading and saving between files and DOM trees asynchronously) | 3.0
Validation                                                              | 3.0

# Notes

Don't trust _hasFeature()_ alone you should also use [capability detection](http://dev.opera.com/articles/view/using-capability-detection/) in addition to _hasFeature()_.

Using the _isSupported_ method implementation information can be gathered for a specific/selected node only (i.e. _element.isSupported(feature,version_).

You can determince online what a user agent supports by visiting <http://www.w3.org/2003/02/06-dom-support.html>. Here you will find a table indicating what the browser loading the url claims to implement.

## 2.7 Get a reference to the focus/active node in the _document_

Using the _document.activeElement_ we can quickly get a reference to the node in the document that is focused/active. In the code below, on page load, I am setting the focus of the document to the _<textarea>_ node and then gaining a reference to that node using the _activeElement_ property.

```
<!DOCTYPE html>
<html lang="en">
<body>
<textarea></textarea>

<script>

//set focus to <textarea>
document.querySelector('textarea').focus();

​//get reference to element that is focused/active in the document
console.log(document.activeElement); //logs <textarea>

</script>
</body>
</html>
```

### Notes

The focused/active element returns elements that have the ability to be focused. If you visit a web page in a browser and start hitting the tab key you will see focus shifting from element to element in that page that can get focused. Don't confuse the selection of nodes (highlight sections of the HTML page with mouse) with elements that get focus for the purpose of inputting something with keystrokes, spacebar, or mouse.

## 2.8 Determing if the _document_ or any node inside of the _document_ has focus

Using the document.hasFocus() method its possible to know if the user currently is focused on the window that has the HTML document loaded. In the code below you see that if we execute the code and then focus another window, tabe, or application all together the _getFocus()_ will return false.

```
<!DOCTYPE html>
<html lang="en">
<body>

<script>

//If you keep focus on the window/tab that has the document loaded its true. If not it's false.
setTimeout(function(){console.log(document.hasFocus())},5000);

</script>
</body>
</html>
```

## 2.9 _document.defaultview_ is a shortcut to the head/global object

You should be aware that the _defaultView_ property is a shortcut to the JavaScript head object or what some refer to as the global object. The head object in a web browser is the _window_ object and _defaultView_ will point to this object in a JavaScript browser enviroment. The code below demonstrates the value of _defaultView_ in a browser.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

console.log(document.defaultView) //reference, head JS object. Would be window object in a browser.

</script>
</body>
</html>
```

If you are dealing with a DOM that is headless or an JavaScript enviroment that is not running in a web browser (i.e. [node.js](http://nodejs.org/)) this property can get you access to the head object scope.

## 2.9 Getting a reference to the _Document_ from an _element_ using _ownerDocument_

The _ownerDocument_ property when called on a node returns a reference to the _Document_ the node is contained within. In the code below I get a reference to the _Document_ of the _<body>_ in the HTML document and the _Document_ node for the _<body>_ element contained inside of the iframe.

live code: N/A

```
<!DOCTYPE html>
<html lang="en">
<body>

<iframe src="http://someFileServedFromServerOnSameDomain.html"></iframe>

<script>

//get the window.document that the <body> is contained within
console.log(document.body.ownerElement);

//get the window.document the <body> inside of the iframe is contained within
console.log(window.frames[0].document.body.ownerElement);

</script>
</body>
</html>
```

If _ownerDocument_ is called on the _Document_ node the value returned is _null_.

## Chapter 3 - Element Nodes

## 3.1 _HTML\_Element* object overview

Elements in an html document all have a unique nature and as such they all have a unique [JavaScript constructor](http://www.w3.org/TR/2003/REC-DOM-Level-2-HTML-20030109/html.html) that instantiates the element as a node object in a DOM tree. For example an _<a>_ element is created as a DOM node from the _HTMLAnchorElement()_ constructor. Below we verify that an anchor element is created from _HTMLAnchorElement()_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a></a>

<script>

// grab <a> element node from DOM and ask for the name of the constructor that constructed it
console.log(document.querySelector('a').constructor);
//logs function HTMLAnchorElement() { [native code] }

</script>
</body>
</html>
```

The point I am trying to express in the previous code example is that each element in the DOM is constructed from a unique JavaScript intefaces/constructor. The list below (not a [complete list](http://www.whatwg.org/specs/web-apps/current-work/multipage/section-index.html#elements-1)) should give you a good sense of the interfaces/constructors used to create HTML elements.

- _HTMLHtmlElement_
- _HTMLHeadElement_
- _HTMLLinkElement_
- _HTMLTitleElement_
- _HTMLMetaElement_
- _HTMLBaseElement_
- _HTMLIsIndexElement_
- _HTMLStyleElement_
- _HTMLBodyElement_
- _HTMLFormElement_
- _HTMLSelectElement_
- _HTMLOptGroupElement_
- _HTMLOptionElement_
- _HTMLInputElement_
- _HTMLTextAreaElement_
- _HTMLButtonElement_
- _HTMLLabelElement_
- _HTMLFieldSetElement_
- _HTMLLegendElement_
- _HTMLUListElement_
- _HTMLOListElement_
- _HTMLDListElement_
- _HTMLDirectoryElement_
- _HTMLMenuElement_
- _HTMLLIElement_
- _HTMLDivElement_
- _HTMLParagraphElement_
- _HTMLHeadingElement_
- _HTMLQuoteElement_
- _HTMLPreElement_
- _HTMLBRElement_
- _HTMLBaseFontElement_
- _HTMLFontElement_
- _HTMLHRElement_
- _HTMLModElement_
- _HTMLAnchorElement_
- _HTMLImageElement_
- _HTMLObjectElement_
- _HTMLParamElement_
- _HTMLAppletElement_
- _HTMLMapElement_
- _HTMLAreaElement_
- _HTMLScriptElement_
- _HTMLTableElement_
- _HTMLTableCaptionElement_
- _HTMLTableColElement_
- _HTMLTableSectionElement_
- _HTMLTableRowElement_
- _HTMLTableCellElement_
- _HTMLFrameSetElement_
- _HTMLFrameElement_
- _HTMLIFrameElement_

Keep in mind each _HTML\_Element _above inherits properties and methods from_ HTMLElement_,_ Element_,_ Node_, and_ Object*.

## 3.2 _HTML\_Element* object properties and methods (including inherited)

To get accurate information pertaining to the available properties and methods on an _HTML\_Element* node its best to ignore the specification and to ask the browser what is available. Examine the arrays created in the code below detailing the properties and methods available from HTMLelement nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#">Hi</a>

<script>

var anchor = document.querySelector('a');

//element own properties
console.log(Object.keys(anchor).sort());

//element own properties & inherited properties
var documentPropertiesIncludeInherited = [];
for(var p in document){
    documentPropertiesIncludeInherited.push(p);
}
console.log(documentPropertiesIncludeInherited.sort());

//element inherited properties only
var documentPropertiesOnlyInherited = [];
for(var p in document){
    if(!document.hasOwnProperty(p)){
        documentPropertiesOnlyInherited.push(p);
    }
}
console.log(documentPropertiesOnlyInherited.sort());

</script>
</body>
</html>
```

The available properties are many even if the inherited properties were not considered. Below I've hand pick a list of note worthy properties and methods (inherited as well) for the context of this chapter.

- _createElement()_
- _tagName_
- _children_
- _getAttribute()_
- _setAttribute()_
- _hasAttribute()_
- _removeAttribute()_
- _classList()_
- _dataset_
- _attributes_

For a complete list check out the MDN documentation which covers the [general properties and methods](https://developer.mozilla.org/en/DOM/element) available to most HTML elements.

## 3.3 Creating Elements

_Element_ nodes are instantiated for us when a browser interputs an HTML document and a corresponding DOM is built based on the contents of the document. After this fact, its also possible to programaticlly create _Element_ nodes using _createElement()_. In the code below I create a _<textarea>_ element node and then inject that node into the live DOM tree.

```
<!DOCTYPE html>
<html lang="en">
<body>
<script>

var elementNode = document.createElement('textarea'); //HTMLTextAreaElement() constructs <textarea>
document.body.appendChild(elementNode);

console.log(document.querySelector('textarea')); //verify it's now in the DOM

</script>
</body>
</html>
```

The value passed to the _createElement()_ method is a string that specifices the type of element (aka _[tagName](http://www.w3.org/TR/2000/REC-DOM-Level-2-Core-20001113/core.html#ID-104682815)_) to be created.

### Notes

This value passed to createElement is changed to a lower-case string before the element is created.

## 3.4 Get the tag name of an element

Using the _tagName_ property we can access the name of an element. The _tagName_ property returns the same value that using _nodeName_ would return. Both return the value in uppercase regardless of the case in the source HTML document.

Below we get the name of an _<a>_ element in the DOM.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="#">Hi</a>

<script>

console.log(document.querySelector('a').tagName); //logs A

//the nodeName property returns the same value
console.log(document.querySelector('a').nodeName); //logs A

</script>
</body>
</html>
```

## 3.5 Getting a list/collection of element attributes and values

Using the _attributes_ property (inherited by element nodes from _Node_) we can get a collection of the _[Attr](http://www.w3.org/TR/DOM-Level-3-Core/core.html#ID-637646024)_ nodes that an element currently has defined. The list returned is a _[NameNodeMap](https://developer.mozilla.org/en/DOM/NamedNodeMap)_. Below I loop over the attributes collection exposing each _Attr_ node object contained in the collection.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href='#' title="title" data-foo="dataFoo" class="yes" style="margin:0;" foo="boo"></a>

<script>

var atts = document.querySelector('a').attributes;

for(var i=0; i< atts.length; i++){
    console.log(atts[i].nodeName +'='+ atts[i].nodeValue);
}

</script>
</body>
</html>
```

### Notes

The array returned from accessing the attributes property should be consider live. Meaning that its contents can be changed at anytime.

The array that is returned inherits from the _NameNodeMap_ which provides methods to operate on the array such as _getNamtedItem()_, _setNamedItem()_, and _removeNamedItem()_. Operating on _attributes_ with these methods should be secondary to using _getAttribute()_, _setAttribute()_, _hasAttribute()_, _removeAttribute()_. Its this authors opinion that dealing with [Attr](http://www.w3.org/TR/DOM-Level-3-Core/core.html#ID-637646024) nodes is messy. The only merit in using the _attributes_ is found only in its funcitonaly for returning a list of live attributes.

The _attributes_ property is an array like collection and has a read only _length_ property.

Boolean attributres (e.g. _<option selected>foo</option>_) show up in the _attributes_ list but of course have no value unless you provide one (e.g. _<option selected="selected">foo</option>_).

## 3.6 Getting, Setting, & Removing an element's attribute value

The most consistent way to get, set, or remove an elements [attribute](http://www.whatwg.org/specs/web-apps/current-work/#attributes-1) value is to use the _getAttribute(), setAttribute(),_ and _removeAttribute()_ method. In the code below I demonstrate each of these methods for managing element attributes.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href='#' title="title" data-foo="dataFoo" style="margin:0;" class="yes" foo="boo" hidden="hidden">#link</a>

<script>

var atts = document.querySelector('a');

//remove attributes
atts.removeAttribute('href');
atts.removeAttribute('title');
atts.removeAttribute('style');
atts.removeAttribute('data-foo');
atts.removeAttribute('class');
atts.removeAttribute('foo'); //custom attribute
atts.removeAttribute('hidden'); //boolean attribute

//set (really re-set) attributes
atts.setAttribute('href','#');
atts.setAttribute('title','title');
atts.setAttribute('style','margin:0;');
atts.setAttribute('data-foo','dataFoo');
atts.setAttribute('class','yes');
atts.setAttribute('foo','boo');
atts.setAttribute('hidden','hidden'); //boolean attribute requires sending the attribute as the value too

//get attributes
console.log(atts.getAttribute('href'));
console.log(atts.getAttribute('title'));
console.log(atts.getAttribute('style'));
console.log(atts.getAttribute('data-foo'));
console.log(atts.getAttribute('class'));
console.log(atts.getAttribute('foo'));
console.log(atts.getAttribute('hidden'));

</script>
</body>
</html>
```

### Notes

Use _removeAttribute()_ instead of setting the attribute value to _null_ or _''_ using _setAttribute()_

Some element attributes are available from element nodes as object properties (i.e. _document.body.id_ or _document.body.className_). This author recommends avoiding these properties and using the remove, set, and get attribute methods.

## 3.7 Verifying an element has a specific attribute

The best way to determine (i.e. boolean) if an element has an attribute is to use the _hasAttribute()_ method. Below I verify if the _<a>_ has a _href_, _title_, _style_, _data-foo_, _class_, and _foo_ attribute.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href='#' title="title" data-foo="dataFoo" style="margin:0;" class="yes" goo></a>

<script>

var atts = document.querySelector('a');

console.log(
    atts.hasAttribute('href'),
    atts.hasAttribute('title'),
    atts.hasAttribute('style'),
    atts.hasAttribute('data-foo'),
    atts.hasAttribute('class'),
    atts.hasAttribute('goo') //Notice this is true regardless if a value is defined 
)

</script>
</body>
</html>
```

This method will return _true_ if the element contains the attribute even if the attribute has no value. For example using _hasAttribute()_ we can get a boolean response for [boolean attributes](http://www.w3.org/TR/html4/intro/sgmltut.html#h-3.3.4.2). In the code example below we check to see if a checkbox is checked.

```
<!DOCTYPE html>
<html lang="en">
<body>

<input type="checkbox" checked></input>

<script>

var atts = document.querySelector('input');

console.log(atts.hasAttribute('checked')); //logs true

</script>
</body>
</html>
```

## 3.8 Getting a list of class attribute values

Using the _classList_ property available on element nodes we can access a list (i.e. _[DOMTokenList](http://www.w3.org/TR/dom/#interface-domtokenlist)_) of class attribute values that is much easier to work with than a space-delimited string value returned from the _className_ property. In the code below I contrast the use of _classList_ with _className_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div class="big brown bear"></div>

<script>

var elm = document.querySelector('div');

console.log(elm.classList); //big brown bear {0="big", 1="brown", 2="bear", length=3, ...}
console.log(elm.className); //logs 'big brown bear'

</script>
</body>
</html>
```

### Notes

Given the _classList_ is an array like collection it has a read only _length_ property.

_classList_ is read-only but can be modifyied using the _add()_, _remove()_, _contains()_, and _toggle()_ methods

IE9 does not support _classList_. Support will land in [IE10](http://blogs.msdn.com/b/ie/archive/2012/05/31/windows-release-preview-the-sixth-ie10-platform-preview.aspx). [Several](https://github.com/eligrey/classList.js) [polyfills](https://gist.github.com/1381839) are avaliable.

## 3.9 Adding & removing sub-values to a class attribute

Using the _classList.add()_ and _classList.remove()_ methods its extremely simple to edit the value of a class attribute. In the code below I demonstrated adding and removing class values.

```
<!DOCTYPE html>
<html lang="en">
<body>
<div class="dog"></div>​

<script>

var elm = document.querySelector('div');

elm.classList.add('cat');
elm.classList.remove('dog');
console.log(elm.className); //'cat'

</script>
</body>
</html>
```

## 3.10 Toggling a class attribute value

Using the _classList.toggle()_ method we can toggle a sub-value of the class attribute. This allows us to add a value if its missing or remove a value if its already added. In the code below I toggle the _'visible'_ value and the _'grow'_ value. Which essentially means I remove _'visible'_ and add _'grow'_ to the class attribute value.

```
<!DOCTYPE html>
<html lang="en">
<body>
<div class="visible"></div>​

<script>

var elm = document.querySelector('div');

elm.classList.toggle('visible');
elm.classList.toggle('grow');
console.log(elm.className); //'grow'

</script>
</body>
</html>
```

## 3.11 Determining if a class attribute value contains a specific value

Using the _classList.contains()_ method its possible to determine (boolean) if a class attribute value contains a specific sub-value. In the code below we test weather the _<div>_ class attribute contains a sub-value of _brown_.

```
<!DOCTYPE html>
<html lang="en">
<body>
<div class="big brown bear"></div>​

<script>

var elm = document.querySelector('div');

console.log(elm.classList.contains('brown')); //logs true

</script>
</body>
</html>
```

## 3.12 Getting & Setting data-* attributes

The _dataset_ property of a element node provides an object containing all of the attributes of an element that starts with data-*. Because its a simply a JavaScript object we can manipulate _dataset_ and have the element in the DOM reflect those changes

```
<!DOCTYPE html>
<html lang="en">
<body>

<div data-foo-foo="foo" data-bar-bar="bar"></div>​

<script>

var elm = document.querySelector('div');

//get
console.log(elm.dataset.fooFoo); //logs 'foo'
console.log(elm.dataset.barBar); //logs 'bar'

//set
elm.dataset.gooGoo = 'goo';
console.log(elm.dataset); //logs DOMStringMap {fooFoo="foo", barBar="bar", gooGoo="goo"}

//what the element looks like in the DOM 
console.log(elm); //logs <div data-foo-foo="foo" data-bar-bar="bar" data-goo-goo="goo">

</script>
</body>
</html>
```

### Notes

_dataset_ contains camel case versions of data attributes. Meaning _data-foo-foo_ will be listed as the property _fooFoo_ in the dataset _DOMStringMap_ object. The_-_ is replaced by camel casing.

Removing a data-* attribute from the DOM is as simple using the _delete_ operator on a property of the _datset_ (e.g. _delete dataset.fooFoo_)

_dataset_ is not supported in IE9\. A [polyfill](https://github.com/remy/polyfills/blob/master/dataset.js) is avaliable. However, you can always just use getAttribute('data-foo'), removeAttribute('data-foo'), setAttribute('data-foo'), hasAttribute('data-foo').

## Chapter 4 - Element Node Selecting

## 4.1 Selecting a specific element node

The most common methods for getting a reference to a single element node are:

- _querySelector()_
- _getElementById()_

In the code below I leverage both of these methods to select an element node from the HTML document.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
<li>Hello</li>
<li>big</li>
<li>bad</li>
<li id="last">world</li>
</ul>

<script>

console.log(document.querySelector('li').textContent); //logs Hello
console.log(document.getElementById('last').textContent); //logs world

</script>
</body>
</html>
```

The _getElementById()_ method is pretty simple compared to the more robust _querySelector()_ method. The _querySelector()_ method permits a parameter in the form of a [CSS selector syntax](http://www.w3.org/TR/css3-selectors/#selectors). Basically you can pass this method a CSS 3 selector (e.g. _'#score>tbody>tr>td:nth-of-type(2)'_) which it will use to select a single element in the DOM.

### Notes

_querySelector()_ will return the first node element found in the document based on the selector. For example, in the code example above we pass a selector that would select all the li's in CSS, but only the first one is returned.

_querySelector()_ is also defined on element nodes. This allows for the method to limit (allows for context querying) its results to a specific vein of the DOM tree

## 4.2 Selecting/creating a list (aka _NodeList_) of element nodes

The most common methods for selecting/creating a list of nodes in an HTML document are:

- _querySelectorAll()_
- _getElementsByTagName()_
- _getElementsByClassName()_

Below we use all three of these methods to create a list of the _<li>_ elements in the document.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
<li class="liClass">Hello</li>
<li class="liClass">big</li>
<li class="liClass">bad</li>
<li class="liClass">world</li>
</ul>

<script>

//all of the methods below create/select the same list of <li> elements from the DOM
console.log(document.querySelectorAll('li'));
console.log(document.getElementsByTagName('li'));
console.log(document.getElementsByClassName('liClass'));

</script>
</body>
</html>
```

If its not clear the methods used in the code example above do not select a specific element, but instead creates a list (aka [_NodeLists_](https://developer.mozilla.org/En/DOM/NodeList)) of elements that you can select from.

### Notes

_NodeLists_ created from _getElementsByTagName()_ and _getElementsByClassName()_ are considered live are will always reflect the state of the document even if the document is updated after the list is created/selected.

The _querySelectorAll()_ method does not return a live list of elements. Meaning that the list created from _querySelectorAll()_ is a snap shot of the document at the time it was created and is not reflective of the document as it changes. The list is static not live.

_querySelectorAll()_, _getElementsByTagName()_, and _getElementsByClassName_ are also defined on element nodes. This allows for the method to limit its results to specific vein(s) of the DOM tree (e.g. _document.getElementById('header').getElementsByClassName('a')_).

I did not mention the _getElementsByName()_ method as it not commonly leverage over other solutions but you should be aware of its existence for selecting form, img, frame, embed, and object elements from a document that all have the same name attribute value.

Passing either _querySelectorAll()_ or _getElementsByTagName()_ the string _'\_'*, which generally means all, will return a list of all elements in the document.

Keep in mind that _childNodes_ will also return a _NodeList_ just like _querySelectorAll()_, _getElementsByTagName()_, and _getElementsByClassName_

The _NodeLists_ are array like (but does not inherit array methods) lists/collections and have a read only _length_ property

## 4.3 Selecting all immediate child element nodes

Using the _children_ property from an element node we can get a list (aka [_HTMLCollection_](https://developer.mozilla.org/en/DOM/HTMLCollection)) of all the immediate children nodes that are element nodes. In the code below I use _children_ to create a selection/list of all of the _<li>_'s contained wiithin the _<ul>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
<li><strong>Hi</strong></li>
<li>there</li>
</ul>

<script>

var ulElement = document.querySelector('ul').children;

//logs a list/array of all immediate child element nodes
console.log(ulElement); //logs [<li>, <li>]

</script>
</body>
</html>
```

Notice that using _children_ only gives us the immediate element nodes excluding any nodes (e.g. text nodes) that are not elements. If the element has no children then _children_ will return an empty array-like-list.

### Notes

_HTMLCollection_'s contain elements in document order, that is they are placed in the array in the order the elements appear in the DOM

_HTMLCollection_'s are live, which means any change to the document will be reflected dynamically in the collection

## 4.4 Contextual element selecting

The methods _querySelector()_, _querySelectorAll()_, _getElementsByTagName()_, and _getElementsByClassName_ typically accessed from the _document_ object are also defined on element nodes. This allows for these methods to limit its results to specific vein(s) of the DOM tree. Or said another, you can select a specific context in which you would like the methods to search for element nodes by invoking these methods on element node objects.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>
<ul>
<li class="liClass">Hello</li>
<li class="liClass">big</li>
<li class="liClass">bad</li>
<li class="liClass">world</li>
</ul>
</div>

<ul>
<li class="liClass">Hello</li>
</ul>

<script>

//select a div as the context to run the selecting methods only on the contents of the div
var div = document.querySelector('div');

console.log(div.querySelector('ul'));
console.log(div.querySelectorAll('li'));
console.log(div.getElementsByTagName('li'));
console.log(div.getElementsByClassName('liClass'));

</script>
</body>
</html>
```

These methods not only operate on the live dom but programatic DOM structures that are created in code as well.

```
<!DOCTYPE html>
<html lang="en">
<body>

<script>

//create DOM structure
var divElm = document.createElement('div');
var ulElm = document.createElement('ul');
var liElm = document.createElement('li');
liElm.setAttribute('class','liClass');
ulElm.appendChild(liElm);
divElm.appendChild(ulElm);

//use selecting methods on DOM structure
console.log(divElm.querySelector('ul'));
console.log(divElm.querySelectorAll('li'));
console.log(divElm.getElementsByTagName('li'));
console.log(divElm.getElementsByClassName('liClass'));

</body>
</html>
```

## 4.5 Pre-configured selections/lists of element nodes

You should be aware that there are some legacy, pre-configured arrays-like-lists, containing element nodes from an HTML document. Below I list a couple of these (not the complete list) that might be handy to be aware of.

- _document.all_ - all elements in HTML document
- _document.forms_ - all _<form>_ elements in HTML document
- _document.images_ - all _<img>_ elements in HTML document
- _document.links_ - all _<a>_ elements in HTML document
- _document.scripts_ - all _<script>_ elements in HTML document
- _document.styleSheets_ - all _<link>_ or _<style>_ objects in HTML document

### Notes

These pre-configured arrays are constucted from the [HTMLCollection](https://developer.mozilla.org/en/DOM/HTMLCollection) interface/object, except _document.styleSheets_ it uses _StyleSheetList_

_[HTMLCollection](https://developer.mozilla.org/en-US/docs/DOM/HTMLCollection)_'s are live just like _[NodeList](https://developer.mozilla.org/En/DOM/NodeList)_'s.

Oddly _document.all_ is constucted from a _HTMLAllCollection_ not an _HTMLCollection_ and is not supported in Firefox

## 4.6 Verify an element will be selected using _matchesSelector()_

Using the _matchesSelector()_ method we can determine if an element will match a selector string. For example say we want to determine if an _<li>_ is the first child element of a _<ul>_. In the code example below I select the first _<li>_ inside of the _<ul>_ and then ask if that element matches the selector, _li:first-child_. Because it in fact does, the _matchesSelector()_ method returns _true_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
<li>Hello</li>
<li>world</li>
</ul>

<script>

//fails in modern browser must use browser prefix moz, webkit, o, and ms
console.log(document.querySelector('li').matchesSelector('li:first-child')); //logs false

//prefix moz
//console.log(document.querySelector('li').mozMatchesSelector('li:first-child'));

//prefix webkit
//console.log(document.querySelector('li').webkitMatchesSelector('li:first-child'));

//prefix o
//console.log(document.querySelector('li').oMatchesSelector('li:first-child'));

//prefix ms
//console.log(document.querySelector('li').msMatchesSelector('li:first-child'));

</script>
</body>
</html>
```

### Notes

matchesSelector has not seen much love from the browsers as its usage is behind browser prefixes _mozMatchesSelector()_, _webkitMatchesSelector()_, _oMatchesSelector()_, _msMatchesSelector()_

In the future _matchesSelector()_ will be renamed to _matches()_

## Chapter 5 - Element Node Geometry & Scrolling Geometry

## 5.1 Element node size, offsets, and scrolling overview

DOM nodes are parsed and [painted](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/#Painting) into visual shapes when viewing html documents in a web browser. Nodes, mostly element nodes, have a corresponding visual representation made viewable/visual by browsers. To inspect and in some cases manipulate the visual representation and gemometry of nodes programatically a set of API's exists and are specified in the [CSSOM View Module](http://www.w3.org/TR/cssom-view/). A subset of methods and properties found in this specification provide an API to determine the geometry (i.e. size & position using offset) of element nodes as well as hooks for manipulating scrollable nodes and getting values of scrolled nodes. This chapter breaks down these methods and properties.

### Notes

Most of the properties (excluding _scrollLeft_ & _scrollTop_) from the CSSOM View Module specification are read only and calculated each time they are accessed. In other words, the values are live

## 5.2 Getting an elements _offsetTop_ and _offsetLeft_ values relative to the _offsetParent_

Using the properties _offsetTop_ and _offsetLeft_ we can get the offset pixel value of an element node from the _offsetParent_. These element node properties give us the distance in pixels from an elements outside top and left border to the inside top and left border of the _offsetParent_. The value of the _offsetParent_ is determined by searching the nearest ancestor elements for an element that has a CSS position value not equal to static. If none are found then the _<body>_ element or what some refer to as the "document" (as opposed to the browser viewport) is the _offsetParent_ value. If during the ancestral search a _<td>_, _<th>_, or _<table>_ element with a CSS position value of static is found then this becomes the value of _offsetParent_.

Lets verify that _offsetTop_ and _offsetLeft_ provide the values one might expect. The properties _offsetLeft_ and _offsetTop_ in the code below tell us that the _<div>_ with an _id_ of _red_ is 60px's from the top and left of the _offsetParent_ (i.e. the _<body>_ element in this example).

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
body{margin:0;}
#blue{height:100px;width:100px;background-color:blue;border:10px solid gray; padding:25px;margin:25px;}
#red{height:50px;width:50px;background-color:red;border:10px solid gray;}
</style>
</head>
<body>

<div id="blue"><div id="red"></div></div>

<script>

var div = document.querySelector('#red'); console.log(div.offsetLeft); //logs 60
console.log(div.offsetTop); //logs 60
console.log(div.offsetParent); //logs <body>

</script>
</body>
</html>
```

Examine the following image showing what the code visually show in browser to help aid your understanding of how the _offsetLeft_ and _offsetTop_ values are deteremined. The red _<div>_ shown in the image is exactly 60 pixels from the _offsetParent_.

![](http://domenlightenment.com/images/c4sec2.png)

Notice I am measuring from the outside border of the red _<div>_ element to the inside border of the _offsetParent_ (i.e. _<body>_).

As previously mentioned If I was to change the blue _<div>_ in the above code to have a position of absolute this would alter the value of the _offsetParent_. In the code below, absolutely positioning the blue _<div>_ will cause the values returned from _offsetLeft_ and _offsetTop_ to report an offset (i.e. 25px's). This is because the offset parent is now the blue _<div>_ and not the _<body>_ .

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
#blue{height:100px;width:100px;background-color:blue;border:10px solid gray; padding:25px;margin:25px;position:absolute;}
#red{height:50px;width:50px;background-color:red;border:10px solid gray;}
</style>
</head>
<body>

<div id="blue"><div id="red"></div></div>

<script>

var div = document.querySelector('#red'); console.log(div.offsetLeft); //logs 25
console.log(div.offsetTop); //logs 25
console.log(div.offsetParent); //logs <div id="blue">

</script>
</body>
</html>
```

The image of the browser view shown below clarifies the new measurements returned from _offsetLeft_ and _offsetTop_ when the _offsetParent_ is the blue _<div>_.

![](http://domenlightenment.com/images/c4sec2b.png)

### Notes

Many of the browsers break the outside border to inside border measurement when the _offsetParent_ is the _<body>_ and the _<body>_ or _<html>_ element has a visible margin, padding, or border value.

The _offsetParent_, _offsetTop_, and _offsetLeft_ are extensions to the _HTMLelement_ object.

## 5.3 Getting an elements top, right, bottom and left border edge offset relative to the viewport using _getBoundingClientRect()_

Using the _getBoundingClientRect()_ method we can get the position of an elements outside border edges as its painted in the browser viewport relative to the top and left edge of the viewport. This means the left and right edge are measured from the outside border edge of an element to the left edge of the viewport. And the top and bottom edges are measured from the outside border edge of an element to the top edge of the viewport.

In the code below I create a 50px X 50px _<div>_ with a 10px border and 100px margin. To get the distance in pixels from each border edge of the _<div>_ I call the _getBoundingClientRect()_ method on the _<div>_ which returns an object containing a _top_, _right_, _bottom_, and _left_ property.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
body{margin:0;}
div{height:50px;width:50px;background-color:red;border:10px solid gray;margin:100px;}
</style>
</head>
<body>

<div></div>

<script>

var divEdges = document.querySelector('div').getBoundingClientRect(); console.log(divEdges.top, divEdges.right, divEdges.bottom, divEdges.left); //logs '100 170 170 100'

</script>
</body>
</html>
```

The image below shows the browser rendered view of the above code with some added measurement indicators to show exactly how _getBoudingClientRect()_ is calculated.

![](http://domenlightenment.com/images/c4sec3.png)

The _top_ outside border edge of the _<div>_ element is 100px from the top edge of the viewport. The _right_ outside border edge of the element _<div>_ is 170px from the left edge of the viewport. The _bottom_ outside border edge of the element _<div>_ is 170px from the top edge of the viewport. And the _left_ outside border edge of the element _<div>_ is 100px from the left edge of the viewport.

## 5.4 Getting an elements size (border + padding + content) in the viewport

The _getBoundingClientRect()_ returns an object with a top, right, bottom, and left property/value but also with a height and width property/value. The _height_ and _width_ properties indicate the size of the element where the total size is derived by adding the content of the div, its padding, and borders together.

In the code below I get the size of the _<div>_ element in the DOM using _getBoundingClientRect()_.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
div{height:25px;width:25px;background-color:red;border:25px solid gray;padding:25px;}
</style>
</head>
<body><div></div><script>var div = document.querySelector('div').getBoundingClientRect(); 

console.log(div.height, div.width); //logs '125 125'
//because 25px border + 25px padding + 25 content + 25 padding + 25 border = 125</script>
</body>
</html>
```

The exact same size values can also be found using from the _offsetHeight_ and _offsetWidth_ properties. In the code below I leverage these properties to get the same exact height and width values provided by _getBoundingClientRect()_.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
div{height:25px;width:25px;background-color:red;border:25px solid gray;padding:25px;}
</style>
</head>
<body><div></div><script>var div = document.querySelector('div'); 

console.log(div.offsetHeight, div.offsetWidth); //logs '125 125'
//because 25px border + 25px padding + 25 content + 25 padding + 25 border = 125</script>
</body>
</html>
```

## 5.5 Getting an elements size (padding + content) in the viewport excluding borders

The _clientWidth_ and _clientHeight_ properties return a total size of an element by adding together the content of the element and its padding excluding the border sizes. In the code below I use these two properties to get the height and width of an element including padding but excluding borders.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
div{height:25px;width:25px;background-color:red;border:25px solid gray;padding:25px;}
</style>
</head>
<body><div></div><script>var div = document.querySelector('div'); 

console.log(div.clientHeight, div.clientWidth); //logs '75 75' because 25px padding + 25 content + 25 padding = 75</script>
</body>
</html>
```

## 5.6 Getting topmost element in viewport at a specific point using _elementFromPoint()_

Using _elementFromPoint()_ it's possible to get a reference to the topmost element in an html document at a specific point in the document. In the code example below I simply ask what is the topmost element 50 pixels from the top and left of the viewport. Since we have two _<div_>'s at that location the topmost (or if there is no z-index set the last one in document order) div is selected and returned.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
div{height:50px;width:50px;background-color:red;position:absolute;top:50px;left:50px;}
</style>
</head>
<body><div id="bottom"></div><div id="top"></div><script>console.log(document.elementFromPoint(50,50)); //logs <div id="top"></script>
</body>
</html>
```

## 5.7 Getting the size of the element being scrolled using _scrollHeight_ and _scrollWidth_

The _scrollHeight_ and _scrollWidth_ properties simply give you the height and width of the node being scrolled. For example, open any HTML document that scrolls in a web browser and access these properties on the _<html>_ (e.g. _document.documentElement.scrollWidth_) or _<body>_ (e.g. _document.body.scrollWidth_) and you will get the total size of the HTML document being scrolled. Since we can apply, using CSS (i.e overflow:scroll), to elements lets look at a simpler code example. In the code below I make a _<div>_ scroll a _<p>_ element that is 1000px's x 1000px's. Accessing the _scrollHeight_ and _scrollWidth_ properties on the _<div>_ will tell us that the element being scroll is 1000px's x 1000px's.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
*{margin:0;padding:0;}
div{height:100px;width:100px; overflow:auto;}
p{height:1000px;width:1000px;background-color:red;}
</style>
</head>
<body><div><p></p></div><script>var div = document.querySelector('div'); console.log(div.scrollHeight, div.scrollWidth); //logs '1000 1000'</script>
</body>
</html>
```

### Notes

If you need to know the height and width of the node inside a scrollable area when the node is smaller than the viewport of the scrollable area don't use _scrollHeight_ and _scrollWidth_ as this will give you the size of the viewport. If the node being scrolled is smaller than the scroll area then use _clientHeight_ and _clientWidth_ to determine the size of the node contained in the scrollable area.

## 5.8 Getting & Setting pixels scrolled from the top and left using _scrollTop_ and _scrollLeft_

The _scrollTop_ and _scrollLeft_ properties are read-write properties that return the pixels to the left or top that are not currently viewable in the scrollable viewport due to scrolling. In the code below I setup a _<div>_ that scrolls a _<p>_ element.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
div{height:100px;width:100px;overflow:auto;}
p{height:1000px;width:1000px;background-color:red;}
</style>
</head>
<body><div><p></p></div><script>var div = document.querySelector('div'); div.scrollTop = 750;
div.scrollLeft = 750;console.log(div.scrollTop,div.scrollLeft); //logs '750 750' </script>
</body>
</html>
```

I programatically scroll the _<div>_ by setting the _scrollTop_ and _scrollLeft_ to 750\. Then I get the current value of _scrollTop_ and _scrollLeft_, which of course since we just set the value to 750 will return a value of 750\. The 750 reports the number of pixels scroll and indicates 750 px's to the left and top are not viewable in the viewport. If it helps just think of these properties as the pixel measurements of the content that is not shown in the viewport to the left or top.

## 5.9 Scrolling an element into view using _scrollIntoView()_

By selecting a node contained inside a node that is scrollable we can tell the selected node to scroll into view using the _scrollIntoView()_ method. In the code below I select the fifth _<p>_ element contained in the scrolling _<div>_ and call _scrollIntoView()_ on it.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
div{height:30px;width:30px; overflow:auto;}
p{background-color:red;}
</style>
</head>
<body><div>
<content>
<p>1</p>
<p>2</p>
<p>3</p>
<p>4</p>
<p>5</p>
<p>6</p>
<p>7</p>
<p>8</p>
<p>9</p>
<p>10</p>            
</content>        
</div><script>

//select <p>5</p> and scroll that element into view, I pass children '4' because its a zero index array-like structure
document.querySelector('content').children[4].scrollIntoView(true);

</script>
</body>
</html>
```

By passing the _scrollIntoView()_ method a parameter of _true_ I am telling the method to scroll to the top of the element being scrolled too. The _true_ parameter is however not needed as this is the default action performed by the method. If you want to scroll align to the bottom of the element pass a parameter of _false_ to the _scrollIntoView()_ method.

## Chapter 6 - Element Node Inline Styles

## 6.1 Style Attribute (aka element inline CSS properties) Overview

Every HTML element has a style attribute that can be used to inline CSS properties specific to the element. In the code below I am accessing the _style_ attribute of a _<div>_ that contains several inline CSS properties.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div style="background-color:red;border:1px solid black;height:100px;width:100px;"></div>

<script>

var divStyle = document.querySelector('div').style; 

//logs CSSStyleDeclaration {0="background-color", ...}
console.log(divStyle);

 </script>
</body>
</html>
```

Notice in the code above that what is returned from the _style_ property is a _CSSStyleDeclaration_ object and not a string. Additionally note that only the elements inline styles (i.e. excluding the computed styles, computed styles being any styles that have cascaded from style sheets) are included in the _CSSStyleDeclartion_ object.

## 6.2 Getting, setting, & removing individual inline CSS properties

Inline CSS styles are individually represented as a property (i.e. object property) of the _style_ object avaliabe on element node objects. This provides the interface for us to get, set, or remove individual CSS properties on an element by simply setting an objects property value. In the code below we set, get, and remove styles on a _<div>_ by manipulating the properties of the _style_ object.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div></div>

<script>

var divStyle = document.querySelector('div').style;

//set
divStyle.backgroundColor = 'red';
divStyle.border = '1px solid black';
divStyle.width = '100px';
divStyle.height = '100px';

//get
console.log(divStyle.backgroundColor);
console.log(divStyle.border);
console.log(divStyle.width);
console.log(divStyle.height);

/*remove
divStyle.backgroundColor = '';
divStyle.border = '';
divStyle.width = '';
divStyle.height = '';
*/

</script>
</body>
</html>
```

### Notes

The property names contained in the style object do not contain the normal hyphen that is used in CSS property names. The translation is pretty simple. Remove the hyphen and use camel case. (e.g. font-size = _fontSize_ or background-image = _backgroundImage_). In the case where a css property name is a JavaScript keyword the javascript css property name is prefixed with "css" (e.g. float = _cssFloat_).

Short hand properties are available as properties as well. So you can not only set _margin_, but also _marginTop_.

Remember to include for any css property value that requires a unit of measure the appropriate unit (e.g. _style.width = '300px';_ _not style.width = '300';_). When a document is rendered in standards mode the unit of measure is require or it will be ignored. In quirksmode assumptions are made if not unit of measure is included.

CSS Property                  | JavaScript Property
----------------------------- | -------------------------
background                    | background
background-attachment         | backgroundAttachment
background-color              | backgroundColor
background-image              | backgroundImage
background-position           | backgroundPosition
background-repeat             | backgroundRepeat
border                        | border
border-bottom                 | borderBottom
border-bottom-color           | borderBottomColor
border-bottom-style           | borderBottomStyle
border-bottom-width           | borderBottomWidth
border-color                  | borderColor
border-left                   | borderLeft
border-left-color             | borderLeftColor
border-left-style             | borderLeftStyle
border-left-width             | borderLeftWidth
border-right                  | borderRight
border-right-color            | borderRightColor
border-right-style            | borderRightStyle
border-right-width            | borderRightWidth
border-style                  | borderStyle
border-top                    | borderTop
border-top-color              | borderTopColor
border-top-style              | borderTopStyle
border-top-width              | borderTopWidth
border-width                  | borderWidth
clear                         | clear
clip                          | clip
color                         | color
cursor                        | cursor
display                       | display
filter                        | filter
font                          | font
font-family                   | fontFamily
font-size                     | fontSize
font-variant                  | fontVariant
font-weight                   | fontWeight
height                        | height
left                          | left
letter-spacing                | letterSpacing
line-height                   | lineHeight
list-style                    | listStyle
list-style-image              | listStyleImage
list-style-position           | listStylePosition
list-style-type               | listStyleType
margin                        | margin
margin-bottom                 | marginBottom
margin-left                   | marginLeft
margin-right                  | marginRight
margin-top                    | marginTop
overflow                      | overflow
padding                       | padding
padding-bottom                | paddingBottom
padding-left                  | paddingLeft
padding-right                 | paddingRight
padding-top                   | paddingTop
page-break-after              | pageBreakAfter
page-break-before             | pageBreakBefore
position                      | position
float                         | styleFloat
text-align                    | textAlign
text-decoration               | textDecoration
text-decoration: blink        | textDecorationBlink
text-decoration: line-through | textDecorationLineThrough
text-decoration: none         | textDecorationNone
text-decoration: overline     | textDecorationOverline
text-decoration: underline    | textDecorationUnderline
text-indent                   | textIndent
text-transform                | textTransform
top                           | top
vertical-align                | verticalAlign
visibility                    | visibility
width                         | width
z-index                       | zIndex

The style object is a _CSSStyleDeclaration_ object and it provides not only access to inidividual CSS properties, but also the _setPropertyValue(propertyName)_, _getPropertyValue(propertyName,value)_, and _removeProperty()_ methods used to manipulate individual CSS properties on a element node. In the code below we set, get, and remove individual CSS properties on a _<div>_ using these methods.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
</style>
</head>

<body>

<div style="background-color:green;border:1px solid purple;"></div>

<script>

var divStyle = document.querySelector('div').style;

//set
divStyle.setProperty('background-color','red');
divStyle.setProperty('border','1px solid black');
divStyle.setProperty('width','100px');
divStyle.setProperty('height','100px');

//get
console.log(divStyle.getPropertyValue('background-color'));
console.log(divStyle.getPropertyValue('border','1px solid black'));
console.log(divStyle.getPropertyValue('width','100px'));
console.log(divStyle.getPropertyValue('height','100px'));

/*remove
divStyle.removeProperty('background-color');
divStyle.removeProperty('border');
divStyle.removeProperty('width');
divStyle.removeProperty('height');
*/

</script>
</body>
</html>
```

### Notes

Take notice that the property name is passed to the _setProperty()_ and _getPropertyValue()_ method using the css property name including a hyphen (e.g. _background-color_ not _backgroundColor_).

For more detailed information about the _setProperty()_, _getPropertyValue()_, and _removeProperty()_ as well as additional properties and methods have a look the [documentation](https://developer.mozilla.org/en/DOM/CSSStyleDeclaration) provided by Mozilla.

## 6.3 Getting, setting, & removing all inline CSS properties

Its possible using the _cssText_ property of the _CSSStyleDeclaration_ object as well as the _getAttribute()_ and _setAttribute()_ method to get, set, and remove the entire (i.e. all inline CSS properties) value of the style attribute using a JavaScript string. In the code below we get, set, and remove all inline CSS (as opposed to individually changing CSS proeprties) on a _<div>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div></div>

<script>

var div = document.querySelector('div');
var divStyle = div.style;

//set using cssText
divStyle.cssText = 'background-color:red;border:1px solid black;height:100px;width:100px;';
//get using cssText
console.log(divStyle.cssText);
//remove
divStyle.cssText = '';

//exactly that same outcome using setAttribute() and getAttribute()

//set using setAttribute
div.setAttribute('style','background-color:red;border:1px solid black;height:100px;width:100px;');
//get using getAttribute
console.log(div.getAttribute('style'));
//remove
div.removeAttribute('style');

</script>
</body>
</html>
```

### Notes

If its not obvious you should note that replacing the _style_ attribute value with a new string is the fastest way to make multiple changes to an elements style.

## 6.4 Getting an elements computed styles (i.e. actual styles including any from the cascade) using _getComputedStyle()_

The _style_ property only contains the css that is defined via the style attribute. To get an elements css from the cascade (i.e. cascading from inline style sheets, external style sheets, browser style sheets) as well as its inline styles you can use _getComputedStyle()_. This method provides a read-only _CSSStyleDeclaration_ object similar to _style_. In the code example below I demonstrate the reading of cascading styles, not just element inline styles.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
​div{
 background-color:red;
 border:1px solid black;
 height:100px;
 width:100px;
}
</style>
</head>

<body>

<div style="background-color:green;border:1px solid purple;"></div>

<script>

var div = document.querySelector('div');

//logs rgb(0, 128, 0) or green, this is an inline element style
console.log(window.getComputedStyle(div).backgroundColor);

//logs 1px solid rgb(128, 0, 128) or 1px solid purple, this is an inline element style
console.log(window.getComputedStyle(div).border);

//logs 100px, note this is not an inline element style
console.log(window.getComputedStyle(div).height);

//logs 100px, note this is not an inline element style
console.log(window.getComputedStyle(div).width);

</script>
</body>
</html>
```

Make sure you note that _getComputedStyle()_ method honors the [CSS specificity hierarchy](http://css-tricks.com/specifics-on-css-specificity/). For example in the code just shown the _backgroundColor_ of the _<div>_ is reported as green not red because inline styles are at the top of the specificity hierarchy thus its the inline _backgroundColor_ value that is applied to the element by the browser and consider its final computed style.

### Notes

No values can by set on a _CSSStyleDeclaration_ object returned from _getComputedStyles()_ its read only.

The _getComputedStyles()_ method returns color values in the _rgb(#,#,#)_ format regardless of how they were originally authored.

[Shorthand](http://www.w3.org/TR/CSS21/about.html#shorthand) properties are not computed for the _CSSStyleDeclaration_ object you will have to use non-shorthand property names for property access (e.g. marginTop not margin)_._

## 6.5 Apply & remove css properties on an element using _class_ & _id_ attributes

Style rules defined in a inline style sheet or external style sheet can be added or removed from an element using the _class_ and _id_ attribute. This is a the most common pattern for manipulating element styles. In the code below, leveraging _setAttribute()_ and _classList.add(),_ inline style rules are applied to a _<div>_ by setting the _class_ and _id_ attribute value. Using _removeAttribute()_ and _classList.remove()_ these CSS rules can be removed as well.

```
<!DOCTYPE html>
<html lang="en">
<head>
<style>
.foo{
  background-color:red;
  padding:10px;
}
#bar{
  border:10px solid #000;
  margin:10px;
}
</style>
</head>
<body>

<div></div>

<script>

var div = document.querySelector('div');

//set
div.setAttribute('id','bar');
div.classList.add('foo');

/*remove
div.removeAttribute('id');
div.classList.remove('foo');
*/

</script>
</body>
</html>
```

## Chapter 7 - Text Nodes

## 7.1 _Text_ object overview

Text in an HTML document is represented by instances of the _Text()_ constructor function, which produces text nodes. When an HTML document is parsed the text mixed in among the elements of an HTML page are converted to text nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p>hi</p>

<script>

//select 'hi' text node
var textHi = document.querySelector('p').firstChild

console.log(textHi.constructor); //logs Text()

//logs Text {textContent="hi", length=2, wholeText="hi", ...}
console.log(textHi);

</script>
</body>
</html>
```

The code above concludes that the _Text()_ constructor function constructs the text node but keep in mind that 

_<span>Text</span>_

 inherits from _CharacterData_, _Node_, and _Object_.

## 7.2 _Text_ object & properties

To get accurate information pertaining to the available properties and methods on an _Text_ node its best to ignore the specification and to ask the browser what is available. Examine the arrays created in the code below detailing the properties and methods available from a text node.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p>hi</p>

<script>
var text = document.querySelector('p').firstChild;

//text own properties
console.log(Object.keys(text).sort());

//text own properties & inherited properties
var textPropertiesIncludeInherited = [];
for(var p in text){
    textPropertiesIncludeInherited.push(p);
}
console.log(textPropertiesIncludeInherited.sort());

//text inherited properties only
var textPropertiesOnlyInherited = [];
for(var p in text){
    if(!text.hasOwnProperty(p)){
        textPropertiesOnlyInherited.push(p);
    }
}
console.log(textPropertiesOnlyInherited.sort());

</script>
</body>
</html>
```

The available properties are many even if the inherited properties were not considered. Below I've hand pick a list of note worthy properties and methods for the context of this chapter.

- _textContent_
-
_splitText()_

- _appendData()_
- _deleteData()_
- _insertData()_
- _replaceData()_
- _subStringData()_
- _normalize()_
- _data_
- _document.createTextNode()_ (not a property or inherited property of text nodes but discussed in this chapter)

# 7.3 White space creates _Text_ nodes

When a DOM is contstructed either by the browser or by programmatic means text nodes are created from white space as well as from text characters. After all, whitespace is a character. In the code below the second paragraph, conaining an empty space, has a child _Text_ node while the first paragraph does not.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p id="p1"></p>
<p id="p2"> </p>

<script>

console.log(document.querySelector('#p1').firstChild) //logs null
console.log(document.querySelector('#p2').firstChild.nodeName) //logs #text

</script>
</body>
</html>
```

Don't forget that white space and text characters in the DOM are typically represented by a text node. This of course means that carriage returns are considered text nodes. In the code below we log a carriage return highlighting the fact that this type of character is in fact a text node.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p id="p1"></p> //yes there is a carriage return text node before this comment, even this comment is a node
<p id="p2"></p>

<script>

console.log(document.querySelector('#p1').nextSibling) //logs Text

</script>
</body>
</html>
```

The reality is if you can input the character or whitespace into an html document using a keyboard then it can potentially be interputed as a text node. If you think about it, unless you minimze/compress the html document the average html page contains a great deal of whitespace and carriage return text nodes.

# 7.4 Creating & Injecting _Text_ Nodes

_Text_ nodes are created automatically for us when a browser interputs an HTML document and a corresponding DOM is built based on the contents of the document. After this fact, its also possible to programatically create _Text_ nodes using _createTextNode()_. In the code below I create a text node and then inject that node into the live DOM tree.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div></div>

<script>

var textNode = document.createTextNode('Hi');
document.querySelector('div').appendChild(textNode);

console.log(document.querySelector('div').innerText); // logs Hi

</script>
</body>
</html>
```

Keep in mind that we can also inject text nodes into programmatically created DOM structures as well. In the code below I place a text node inside of an _<p>_ element before I inject it into the live DOM.

```
<!DOCTYPE html>
<html lang="en">

<div></div>

<body>

<script>

var elementNode = document.createElement('p');
var textNode = document.createTextNode('Hi');
elementNode.appendChild(textNode);
document.querySelector('div').appendChild(elementNode);

console.log(document.querySelector('div').innerHTML); //logs <div>Hi</div>

</script>
</body>
</html>
```

# 7.5 Getting a _Text_ node value with _.data_ or _nodeValue_

The text value/data represented by a _Text_ node can be extracted from the node by using the _.data_ or _nodeValue_ property. Both of these return the text contained in a _Text_ node. Below I demostrate both of these to retrive the value contained in the _<div>_.

```
<!DOCTYPE html>
<html lang="en">

<p>Hi, <strong>cody</strong></p><body>

<script>

console.log(document.querySelector('p').firstChild.data); //logs 'Hi,'
console.log(document.querySelector('p').firstChild.nodeValue); //logs 'Hi,'

</script>
</body>
</html>
```

Notice that the _<p>_ contains two _Text_ node and _Element_ (i.e. _<strong>_)node. And that we are only getting the value of the first child node contained in the _<p>_.

## Notes

Getting the length of the characters contained in a text node is as simple as accessing the length proerty of the node itself or the actual text value/data of the node (i.e. _document.querySelector('p').firstChild.length_ or _document.querySelector('p').firstChild.data.length_ or _document.querySelector('p').firstChild.nodeValue.length_)

# 7.6 Maniputlating _Text_ nodes with _appendData()_, _deleteData()_, _insertData()_, _replaceData()_, _subStringData()_

The _CharacterData_ object that _Text_ nodes inherits methods from provides the following methods for manipulating and extracting sub values from _Text_ node values.

- _appendData()_
- _deleteData()_
- _insertData()_
- _replaceData()_
- _subStringData()_

Each of these are leverage in the code example below.

```
<!DOCTYPE html>
<html lang="en">

<p>Go big Blue Blue<body>

<script>

var pElementText = document.querySelector('p').firstChild;//add !
pElementText.appendData('!');
console.log(pElementText.data);//remove first 'Blue'
pElementText.deleteData(7,5);
console.log(pElementText.data);//insert it back 'Blue'
pElementText.insertData(7,'Blue ');
console.log(pElementText.data);//replace first 'Blue' with 'Bunny'
pElementText.replaceData(7,5,'Bunny ');
console.log(pElementText.data);//extract substring 'Blue Bunny'
console.log(pElementText.substringData(7,10));

</script>
</body>
</html>
```

## Notes

These same manipulation and sub extraction methods can be leverage by _Comment_ nodes

# 7.7 When mulitple sibling _Text_ nodes occur

Typically, immediate sibling _Text_ nodes do not occur because DOM trees created by browsers intelligently combines text nodes, however two cases exist that make sibling text nodes possible. The first case is rather obvious. If a text node contains an _Element_ node (e.g. _<p>Hi, <strong>cody</strong> welcome!</p>_) than the text will be split into the proper node groupings. Its best to look at a code example as this might sound more complicted than it really is. In the code below the contents of the _<p>_ element is not a single _Text_ node it is in fact 3 nodes, a _Text_ node, _Element_ node, and another _Text_ node.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p>Hi, <strong>cody</strong> welcome!</p>

<script>

var pElement = document.querySelector('p');

console.log(pElement.childNodes.length); //logs 3

console.log(pElement.firstChild.data); // is text node or 'Hi, '
console.log(pElement.firstChild.nextSibling); // is Element node or <strong>
console.log(pElement.lastChild.data); ​// is text node or ' welcome!'

</script>
</body>
</html>
```

The next case occurs when we are programatically add _Text_ nodes to an element we created in our code. In the code below I create a _<p>_ element and then append two _Text_ nodes to this element. Which results in sibling _Text_ nodes.

```
<!DOCTYPE html>
<html lang="en">
<body>

<script>

var pElementNode = document.createElement('p');
var textNodeHi = document.createTextNode('Hi ');
var textNodeCody = document.createTextNode('Cody');pElementNode.appendChild(textNodeHi);
pElementNode.appendChild(textNodeCody);document.querySelector('div').appendChild(pElementNode);console.log(document.querySelector('div p').childNodes.length); //logs 2​​​​​​​​​​​​​​​​​​

</script>
</body>
</html>
```

# 7.8 Remove markup and return all child _Text_ nodes using _textContent_

The _textContent_ property can be used to get all child text nodes, as well as to set the contents of a node to a specific _Text_ node. When its used on a node to get the textual content of the node it will returned a concatenataed string of all text nodes contained with the node you call the method on. This functionality would make it very easy to extract all text nodes from an HTML document. Below I extract all of the text contained withing the _<body>_ element. Notice that _textContent_ gathers not just immediate child text nodes but all child text nodes no matter the depth of encapsulation inside of the node the method is called.

live code: N/A

```
<!DOCTYPE html>
<html lang="en">
<body>
<h1> Dude</h2>
<p>you <strong>rock!</strong></p>
<script>

console.log(document.body.textContent); //logs 'Dude you rock!' with some added white space

</script>
</body>
</html>
```

When _textContent_ is used to set the text contained within a node it will remove all child nodes first, replacing them with a single _Text_ node. In the code below I replace all the nodes inside of the _<div>_ element with a single _Text_ node.

```
<!DOCTYPE html>
<html lang="en">
<body>
<div>
<h1> Dude</h2>
<p>you <strong>rock!</strong></p>
</div>
<script>

document.body.textContent = 'You don\'t rock!'
console.log(document.querySelector('div').textContent); //logs 'You don't rock!'

</script>
</body>
</html>
```

## Notes

_textContent_ returns _null_ if used on the a document or doctype node.

_textContent_ returns the contents from _<script>_ and _<style>_ elements

# 7.9 The difference between _textContent_ & _innerText_

Most of the modern bowser, except Firefox, support a seeminly similiar property to _textContent_ named _innerText_. However these properties are not the same. You should be aware of the following differences between _textContent_ & _innerText_.

- _innerText_ is aware of CSS. So if you have hidden text _innerText_ ignores this text, whereas _textContent_ will not
- Because _innerText_ cares about CSS it will trigger a reflow, whereas _textContent_ will not
- _innerText_ ignores the _Text_ nodes contained in _<script>_ and _<style>_ elements
- _innerText_, unlike _textContent_ will normalize the text that is returned. Just think of _textContent_ as returning exactly what is in the document with the markup removed. This will include white space, line breaks, and carriage returns
- _innerText_ is considered to be non-standard and browser specific while _textContent_ is implemented from the DOM specifications

If you you intend to use _innerText_ you'll have to create a work around for Firefox.

# 7.10 Combine sibling _Text_ nodes into one text node using _normalize()_

Sibling _Text_ nodes are typically only encountered when text is programaticly added to the DOM. To eliminate sibling _Text_ nodes that contain no _Element_ nodes we can use _normalize()_. This will concatenate sibling text nodes in the DOM into a single _Text_ node. In the code below I create sibling text, append it to the DOM, then normalize it.

```
<!DOCTYPE html>
<html lang="en">
<body>
<div></div>
<script>

var pElementNode = document.createElement('p');
var textNodeHi = document.createTextNode('Hi');
var textNodeCody = document.createTextNode('Cody');

pElementNode.appendChild(textNodeHi);
pElementNode.appendChild(textNodeCody);

document.querySelector('div').appendChild(pElementNode);

console.log(document.querySelector('p').childNodes.length); //logs 2

document.querySelector('div').normalize(); //combine our sibling text nodes

console.log(document.querySelector('p').childNodes.length); //logs 1

</script>
</body>
</html>
```

# 7.11 Splitting a text node using _splitText()_

When _splitText()_ is called on a _Text_ node it will alter the text node its being called on (leaving the text up to the offset) and return a new _Text_ node that contains the text split off from the orginal text based on the offset. In the code below the text node _Hey Yo!_ is split after _Hey_ and _Hey_ is left in the DOM while _Yo!_ is turned into a new text node are returned by the _splitText()_ method.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p>Hey Yo!</p>

<script>

//returns a new text node, taken from the DOM
console.log(document.querySelector('p').firstChild.splitText(4).data); //logs Yo!

//What remains in the DOM...
console.log(document.querySelector('p').firstChild.textContent); //logs Hey

</script>
</body>
</html>
```

# Chapter 8 - DocumentFragment Nodes

# 8.1 _DocumentFragment_ object overview

The creation and use of a _DocumentFragment_ node provides a light weight document DOM that is external to the live DOM tree. Think of a _DocumentFragment_ as an empty document template that acts just like the live DOM tree, but only lives in memory, and its child nodes can easily be manipulated in memory and then appended to the live DOM.

# 8.2 Creating _DocumentFragment_'s using _createDocumentFragment()_

In the code below a _DocumentFragment_ is created using _createDocumentFragment()_ and _<li>_'s are appended to the fragment.

```
<!DOCTYPE html>
<html lang="en">
<body>

<script>

var docFrag = document.createDocumentFragment();["blue", "green", "red", "blue", "pink"].forEach(function(e) {
    var li = document.createElement("li");
    li.textContent = e;
    docFrag.appendChild(li);
});console.log(docFrag.textContent); //logs bluegreenredbluepink

</script>
</body>
</html>
```

Using a _documentFragment_ to create node structures in memory is extrememly efficent when it comes time to inject the _documentFragment_ into live node structures.

You might wonder what is the advantage to using a _documentFragment_ over simply creating (via _createElement()_) a _<div>_ in memory and working within this _<div>_ to create a DOM structure. The follow are the differences.

- A document fragment may contain any kind of node (except _<body_> or _<html>_) where as an element may not
- The document fragment itself, is not added to the DOM when you append a fragment. The contents of the node are. As opposed to appending an element node in which the element itself is part of the appending.
- When a document fragment is appended to the DOM it transfers from the document fragment to the place its appended. Its no longer in memory in the place you created it. This is not true for element nodes that are temperately used to contained nodes briefly and then are moved to the live DOM.

# 8.3 Adding a _DocumentFragment_ to the live DOM

By passing the _appendChild()_ and _insertBefore()_ node methods a _documentFragment_ argument the child nodes of the _documentFragment_ are transported as children nodes to the DOM node the methods are called on. Below we create a _documentfragment_, add some _<li>_'s to it, then append these new element nodes to the live DOM tree using _appendChild()_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul></ul>

<script>

var ulElm = document.queryselector('ul');
var docFrag = document.createDocumentFragment();["blue", "green", "red", "blue", "pink"].forEach(function(e) {
    var li = document.createElement("li");
    li.textContent = e;
    docFrag.appendChild(li);
});

ulElm.appendChild(docFrag);

//logs <ul><li>blue</li><li>green</li><li>red</li><li>blue</li><li>pink</li></ul>
console.log(document.body.innerHTML);

</script>
</body>
</html>
```

## Notes

Document fragments passed as arguments to inserting node methods will insert the entire child node structure ignoring the documentFragment node itself.

# 8.4 Using _innerHTML_ on a _documentFragment_

Creating a DOM structure in memory using node methods can be verbose and laboring. One way around this would be to created a _documentFragment_, append a _<div>_ to this fragment because _innerHTML_ does not work on document fragments, and then use the _innerHTML_ property to update the fragment with a string of HTML. By doing this a DOM structure is crafted from the HTML string. In the code below I construct a DOM structure that I can then treat as a tree of nodes and not just a JavaScript string.

```
<!DOCTYPE html>
<html lang="en">
<body>

<script>

//create a <div> and document fragment
var divElm = document.createElement('div');
var docFrag = document.createDocumentFragment();

//append div to document fragment
docFrag.appendChild(divElm);

//create a DOM structure from a string
docFrag.querySelector('div').innerHTML = '<ul><li>foo</li><li>bar</li></ul>';

//the string becomes a DOM structure I can call methods on like querySelectorAll()
//Just don't forget the DOM structure is wrapped in a <div>
console.log(docFrag.querySelectorAll('li').length); //logs 2

</script>
</body>
</html>
```

When it comes time to append a DOM structure created using a _documentFragment_ and _<div>_ you'll want to append the structure skipping the injection of the _<div>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div></div>

<script>

//create a <div> and document fragment
var divElm = document.createElement('div');
var docFrag = document.createDocumentFragment();

//append div to document fragment
docFrag.appendChild(divElm);

//create a DOM structure from a string
docFrag.querySelector('div').innerHTML = '<ul><li>foo</li><li>bar</li></ul>';

//append, starting with the first child node contained inside of the <div>
document.querySelector('div').appendChild(docFrag.querySelector('div').firstChild);

//logs <ul><li>foo</li><li>bar</li></ul>
console.log(document.querySelector('div').innerHTML);

</script>
</body>
</html>
```

## Notes

In addtion to _DocumentFragment_ we also have _[DOMParser](http://html5.org/specs/dom-parsing.html#domparser)_ to look forward too. _DOMParser_ can parse HTML stored in a string into a DOM [Document](https://developer.mozilla.org/en/DOM/document "document"). It's only supported in Opera & Firefox as of today, but a [polyfill](https://gist.github.com/1129031) is avaliable. Of course, if you need a stand alone HTML to DOM script try [domify](https://github.com/component/domify).

# 8.5 Leaving a fragments containing nodes in memory by cloning

When appending a _documentFragment_ the nodes contained in the Fragment are moved from the Fragment to the structure you are appending too. To leave the contents of a fragment in memory, so the nodes remain after appending, simply clone using _cloneNode()_ the _documentFragment_ when appending. In the code below instead of tranporting the _<li>_'s from the document fragment I clone the<br>
_<li>_'s, which keeps the _<li>_'s being clonded in memory inside of the _documentFragment_ node.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul></ul>

<script>

//create ul element and document fragment
var ulElm = document.querySelector('ul');
var docFrag = document.createDocumentFragment();

//append li's to document fragment
["blue", "green", "red", "blue", "pink"].forEach(function(e) {
    var li = document.createElement("li");
    li.textContent = e;
    docFrag.appendChild(li);
});

//append cloned document fragment to ul in live DOM
ulElm.appendChild(docFrag.cloneNode(true));

//logs <li>blue</li><li>green</li><li>red</li><li>blue</li><li>pink</li>
console.log(document.querySelector('ul').innerHTML);

//logs [li,li,li,li,li] 
console.log(docFrag.childNodes);

</script>
</body>
</html>
```

# Chapter 9 - CSS Style Sheets & CSS rules

# 9.1 CSS Style sheet overview

A style sheet is added to an HTML document by either using the _HTMLLinkElement_ node (i.e. _<link href="stylesheet.css" rel="stylesheet" type="text/css">_) to include an external style sheet or the _HTMLStyleElement_ node (i.e. _<style></style>_) to define a style sheet inline. In the HTML document below both of these _Element_ node's are in the DOM and I verify which constructor, constructs these nodes.

```
<!DOCTYPE html>
<html lang="en">
<head>

<link id="linkElement" href="http://yui.yahooapis.com/3.3.0/build/cssreset/reset-min.css" rel="stylesheet" type="text/css">

<style id="styleElement">
body{background-color:#fff;}
</style>

</head>
<body>

<script>

//logs function HTMLLinkElement() { [native code] }
console.log(document.querySelector('#linkElement').constructor);

//logs function HTMLStyleElement() { [native code] }
console.log(document.querySelector('#styleElement').constructor);

</script>
</body>
</html>
```

Once a style sheet is added to an HTML document its represented by the _CSSStylesheet_ object. Each CSS rule (e.g. _body{background-color:red;}_) inside of a style sheet is represent by a _CSSStyleRule_ object. In the code below I verify which constructor constructed the style sheet and each CSS rule (selector & its css properties and values) in the style sheet.

```
<!DOCTYPE html>
<html lang="en">
<head>

<style id="styleElement">
body{background-color:#fff;}
</style>

</head>
<body>

<script>

//logs function CSSStyleSheet() { [native code] } because this object is the stylesheet itself
console.log(document.querySelector('#styleElement').sheet.constructor);

//logs function CSSStyleRule() { [native code] } because this object is the rule inside of the style sheet
console.log(document.querySelector('#styleElement').sheet.cssRules[0].constructor);

</script>
</body>
</html>
```

Keep in mind that selecting the element that includes the style sheet (i.e. _<link>_ or _<style_>) is not the same as accessing the actual object (_CSSStyleSheet_) that represents the style sheet itself.

# 9.2 Accessing all style sheets (i.e. _CSSStylesheet_ objects) in the DOM

_document.styleSheets_ gives access to a list of all style sheet objects (aka _CSSStylesheet_) explicitly linked (i.e. _<link>_) or embedded (i.e. _<style>_) in an HTML document. In the code below _styleSheets_ is leverage to gain access to all of the style sheets contained in the document.

live code: N/A

```
<!DOCTYPE html>
<html lang="en">
<head>

<link href="http://yui.yahooapis.com/3.3.0/build/cssreset/reset-min.css" rel="stylesheet" type="text/css">

<style>
body{background-color:red;}
</style>

</head>
<body>

<script>

console.log(document.styleSheets.length); //logs 2
console.log(document.styleSheets[0]); // the <link>
console.log(document.styleSheets[1]); // the <style>

</script>
</body>
</html>
```

## Notes

_styleSheet_ is live just like other node lists

The _length_ property returns the number of stylesheets contained in the list starting at 0 index (i.e. _document.styleSheets.length_)

The style sheets included in a _styleSheets_ list typically includes any style sheets created using the _<style>_ element or using a _<link>_ element where _rel_ is set to _"stylesheet"_

In addtion to using _styleSheets_ to access a documents styles sheets its also possible to access a style sheet in an HTML document by first selecting the element in the DOM (_<style>_ or _<link>_) and using the _.sheet_ property to gain access to the _CSSStyleSheet_ object. In the code below I access the style sheets in the HTML docment by first selecting the element used to include the style sheet and then leveraging the _sheet_ property.

```
<!DOCTYPE html>
<html lang="en">
<head>

<link id="linkElement" href="http://yui.yahooapis.com/3.3.0/build/cssreset/reset-min.css" rel="stylesheet" type="text/css">

<style id="styleElement">
body{background-color:#fff;}
</style>

</head>
<body>

<script>

//get CSSStylesheeet object for <link>
console.log(document.querySelector('#linkElement').sheet); //same as document.styleSheets[0] 

//get CSSSstylesheet object for <style>
console.log(document.querySelector('#styleElement').sheet); //same as document.styleSheets[1]

</script>
</body>
</html>
```

# 9.3 _CSSStyleSheet_ properties and methods

To get accurate information pertaining to the available properties and methods on an _CSSStyleSheet_ node its best to ignore the specification and to ask the browser what is available. Examine the arrays created in the code below detailing the properties and methods available from a _CSSStyleSheet_ node.

```
<!DOCTYPE html>
<html lang="en">
<head><style id="styleElement">
body{background-color:#fff;}
</style></head>
<body>

<script>

var styleSheet = document.querySelector('#styleElement').sheet;

//text own properties
console.log(Object.keys(styleSheet).sort());

//text own properties & inherited properties
var styleSheetPropertiesIncludeInherited = [];
for(var p in styleSheet){
    styleSheetPropertiesIncludeInherited.push(p);
}
console.log(styleSheetPropertiesIncludeInherited.sort());

//text inherited properties only
var styleSheetPropertiesOnlyInherited = [];
for(var p in styleSheet){
    if(!styleSheet.hasOwnProperty(p)){
        styleSheetPropertiesOnlyInherited.push(p);
    }
}
console.log(styleSheetPropertiesOnlyInherited.sort());

</script>
</body>
</html>
```

A _CSSStyleSheet_ object accessed from a _styleSheets_ list or via the _.sheet_ property has the following properties and methods:

- _disabled_
- _href_
- _media_
- _ownerNode_
- _parentStylesheet_
- _title_
- _type_
- _cssRules_
- _ownerRule_
- _deleteRule_
- _inserRule_

## Notes

_href_, _media_, _ownerNode_, _parentStylesheet_, _title_, and _type_ are read only properties, you can't set its value using these properteis

# 9.4 _CSSStyleRule_ overview

A _CSSStyleRule_ object represents each CSS rule contained in a style sheet. Basicly a _CSSStyleRule_ is the interface to the CSS properties and values attached to a selector. In the code below we programaticlly access the details of each rule contained in the inline style sheet by accessing the _CSSStyleRule_ object that represents the CSS rule in the style sheet.

```
<!DOCTYPE html>
<html lang="en">
<head>

<style id="styleElement">
body{background-color:#fff;margin:20px;} /*this is a css rule*/
p{line-height:1.4em; color:blue;} /*this is a css rule*/
</style>

</head>
<body>

<script>

var sSheet = document.querySelector('#styleElement');

console.log(sSheet.cssRules[0].cssText); //logs "body { background-color: red; margin: 20px; }"
console.log(sSheet.cssRules[1].cssText); //logs "p { line-height: 1.4em; color: blue; }"

</script>
</body>
</html>
```

# 9.5 _CSSStyleRule_ properties and methods

To get accurate information pertaining to the available properties and methods on an _CSSStyleRule_ node its best to ignore the specification and to ask the browser what is available. Examine the arrays created in the code below detailing the properties and methods available from a _CSSStyleRule_node.

```
<!DOCTYPE html>
<html lang="en">
<head><style id="styleElement">
body{background-color:#fff;}
</style></head>
<body>

<script>

var styleSheetRule = document.querySelector('#styleElement').sheet.cssRule;

//text own properties
console.log(Object.keys(styleSheetRule).sort());

//text own properties & inherited properties
var styleSheetPropertiesIncludeInherited = [];
for(var p in styleSheetRule){
    styleSheetRulePropertiesIncludeInherited.push(p);
}
console.log(styleSheetRulePropertiesIncludeInherited.sort());

//text inherited properties only
var styleSheetRulePropertiesOnlyInherited = [];
for(var p in styleSheetRule){
    if(!styleSheetRule.hasOwnProperty(p)){
        styleSheetRulePropertiesOnlyInherited.push(p);
    }
}
console.log(styleSheetRulePropertiesOnlyInherited.sort());

</script>
</body>
</html>
```

Scripting the rules (e.g. _body{background-color:red;}_) contained inside of a style sheet is made possible by the _CSSrule_ object. This object provides the following properties:

- _cssText_
- _parentRule_
- _parentStylesSheet_
- _selectorText_
- _style_
- _type_

# 9.6 Getting a list of CSS Rules in a style sheet using _CSSRules_

As previously discussed the _styleSheets_ list provides a list of style sheets contained in a document. The _CSSRules_ list provides a list (aka _CSSRulesList_) of all the CSS rules (i.e. _CSSStyleRule_ objects) in a specific style sheet. The code below logs a _CSSRules_ list to the console.

```
<!DOCTYPE html>
<html lang="en">
<head>

<style id="styleElement">
body{background-color:#fff;margin:20px;}
p{line-height:1.4em; color:blue;}
</style>

</head>
<body>

<script>

var sSheet = document.querySelector('#styleElement').sheet;

//array like list containing all of the CSSrule objects repreesenting each CSS rule in the style sheet
console.log(sSheet.cssRules);

console.log(sSheet.cssRules.length); //logs 2

//rules are index in a CSSRules list starting at a 0 index
console.log(sSheet.cssRules[0]); //logs first rule
console.log(sSheet.cssRules[1]); //logs second rule

</script>
</body>
</html>
```

# 9.7 Inserting & deleting CSS rules in a style sheet using _.insertRule()_ and _.deleteRule()_

The _insertRule()_ and _deleteRule()_ methods provided the ability to programatically manipulate the CSS rules in a style sheet. In the code below I use _insertRule()_ to add the css rule _p{color:red}_ to the inline style sheet at index 1\. Remeber the css rules in a style sheet are numerical index starting at 0\. So by inserting a new rule at index 1 the current rule at index 1 (i.e. _p{font-size:50px;}_) is push to index 2.

```
<!DOCTYPE html>
<html lang="en">
<head>

<style id="styleElement">
p{line-height:1.4em; color:blue;} /*index 0*/
p{font-size:50px;} /*index 1*/
</style>

</head>
<body>

<p>Hi</p>

<script>

//add a new CSS rule at index 1 in the inline style sheet
document.querySelector('#styleElement').sheet.insertRule('p{color:red}',1);

//verify it was added
console.log(document.querySelector('#styleElement').sheet.cssRules[1].cssText);

//Delete what we just added
document.querySelector('#styleElement').sheet.deleteRule(1);

//verify it was removed
console.log(document.querySelector('#styleElement').sheet.cssRules[1].cssText);

</script>
</body>
</html>
```

Deleting or removing a rule is as simple as calling _deleteRule()_ method on a style sheet and passing it the index of the rule in the style sheet to be deleted.

## Notes

Inserting and deleting rules is not a common practice given the difficulty around managing the cascaade and using a numeric indexing system to update a style sheet (i.e. determining at what index a style is located without previewing the contents of the style sheet itself.). Its much simpler working with CSS rules in CSS and HTML files before they are served to a client than programaticlly altering them in the client after the fact.

# 9.8 Editing the value of a _CSSStyleRule_ using the _.style_ property

Just like the _.style_ property that facilitates the manipulation of inline styles on element nodes there is a also _.style_ property for _CSSStyleRule_ objects that orchestrates the same manipulation of styles in style sheets. In the code below I levereage the _.style_ property to set and get the value of css rules contained in the inline style sheet.

```
<!DOCTYPE html>
<html lang="en">
<head>

<style id="styleElement">
p{color:blue;}
strong{color:green;}
</style>

</head>
<body>

<p>Hey <strong>Dude!</strong></p>

<script>

var styleSheet = document.querySelector('#styleElement').sheet;

//Set css rules in stylesheet
styleSheet.cssRules[0].style.color = 'red';
styleSheet.cssRules[1].style.color = 'purple';

//Get css rules
console.log(styleSheet.cssRules[0].style.color); //logs 'red'
console.log(styleSheet.cssRules[1].style.color); //logs 'purple'

</script>
</body>
</html>
```

# 9.9 Creating a new inline CSS style sheets

To craft a new style sheet on the fly after an html page is loaded one only has to create a new _<style>_ node, add CSS rules using _innerHTML_ to this node, then append the _<style>_ node to the HTML document. In the code below I programatily craft a style sheet and add the _body{color:red}_ CSS rule to the style sheet, then append the stylesheet to the DOM.

```
<!DOCTYPE html>
<html lang="en">
<head></head>
<body>

<p>Hey <strong>Dude!</strong></p>

<script>

var styleElm = document.createElement('style');
styleElm.innerHTML = 'body{color:red}';

//notice markup in the document changed to red from our new inline stylesheet
document.querySelector('head').appendChild(styleElm);

</script>
</body>
</html>
```

# 9.10 Programatically adding external style sheets to an HTML document

To add a CSS file to an HTML document programatically a _<link>_ element node is created with the appropriate attributes and then the _<link>_ element node is appended to the DOM. In the code below I programatically include an external style sheet by crafting a new _<link>_ element and appending it to the DOM.

```
<!DOCTYPE html>
<html lang="en">
<head></head>
<body>

<script>

//create & add attributes to <link>
var linkElm = document.createElement('link');
linkElm.setAttribute('rel', 'stylesheet');
linkElm.setAttribute('type', 'text/css');
linkElm.setAttribute('id', 'linkElement');
linkElm.setAttribute('href', 'http://yui.yahooapis.com/3.3.0/build/cssreset/reset-min.css');

//Append to the DOM
document.head.appendChild(linkElm);

//confrim its addition to the DOM
console.log(document.querySelector('#linkElement'));

</script>
</body>
</html>
```

# 9.11 Disabling/Enabling style sheets using _disabled_ property

Using the _.disabled_ property of a _CSSStyleSheet_ object its possible to enable or disabled a style sheet. In the code below we access the current disabled value of each style sheet in the document then proceed to disabled each style sheet leveraging the _.disabled_ property.

```
<!DOCTYPE html>
<html lang="en">
<head>

<link id="linkElement" href="http://yui.yahooapis.com/3.3.0/build/cssreset/reset-min.css" rel="stylesheet" type="text/css">

<style id="styleElement">
body{color:red;}
</style>

</head>
<body>

<script>

//Get current boolean disabled value
console.log(document.querySelector('#linkElement').disabled); //log 'false'
console.log(document.querySelector('#styleElement').disabled); //log 'false'

//Set disabled value, which of courese disabled all styles for this document
document.document.querySelector('#linkElement').disabled = true;
document.document.querySelector('#styleElement').disabled = true;

</script>
</body>
</html>
```

## Notes

Disabled is not an avaliable attributre of a <link> or <style> element according to the specification. Trying to add this as an attribute in the HTML document itself will fail (and likley cause parsing errors where styles are ignored) in the majority of modern browsers in use today.

# Chapter 10 - JavaScript in the DOM

# 10.1 Inserting & executing JavaScript overview

JavaScript can be inserted in to an HTML document in a modern way by including external JavaScript files or writing page level inline JavaScript, which is basically the contents of an external JavaScript file literally embed in the HTML page as a text node. Don't confuse element inline JavaScript contained in attribute event handlers (i.e. _<div onclick="alert('yo')"></div>_) with page inline JavaScript (i.e. _<script>alert('hi')</script>_).

Both methods of inserting JavaScript into an HTML document require the use of a _[<script>](http://www.whatwg.org/specs/web-apps/current-work/multipage/scripting-1.html#the-script-element)_[element node](http://www.whatwg.org/specs/web-apps/current-work/multipage/scripting-1.html#the-script-element). The _<script>_ element can contain JavaScript code or can be used to link to external JavaScript files using the _src_ attribute. Both methods are explored in the code example below.

```
<!DOCTYPE html>
<html lang="en">
<body>

<!-- external, cross domain JavaScript include -->
<script src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js"></script>

<!-- page inline JavaScript -->
<script>
console.log('hi');
</script>

</body>
</html>
```

## Notes

Its possible to insert and execute JavaScript in the DOM by placing JavaScript in an element attribute event handler (i.e. _<div onclick="alert('yo')"></div>_) and using the _javascript:_ protocal (e.g. _<a href="javascript:alert('yo')"></a>_) but this is no longer considered a modern practice.

Trying to include an external JavaScript file and writing page inline JavaScript using the same _<script>_ element will result in the page inline JavaScript being ignored and the exterenal JavaScript file being downloaded and exectued

Self-closing scripts tags (i.e. _<script src="" />_ ) should be avoid unless you are rocking some old school XHTML

The _<script>_ element does not have any required attributes but offers the follow optional attribures: _async_, _charset_, _defer_, _src_, and _type_

Page inline JavaScript produces a text node. Which permits the usage of _innerHTML_ and _textContent_ to retrieve the contents of a line _<script>_. However, appending a new text node made up of JavaScript code to the DOM after the browser has already parsed the DOM will not execute the new JavaScript code. It simply replaces the text.

If JavaScript code contains the string _'</script>'_ you will have to escape the closing _'/'_ with _'<\/script>'_ so that the parser does not think this is the real closing _</script>_ element

# 10.2 JavaScript is parsed synchronously by default

By default when the DOM is being parsed and it encounters a _<script>_ element it will stop parsing the document, block any further rendering & downloading, and exectue the JavaScript. Because this behavior is blocking and does not permit parallel parsing of the DOM or exection of JavaScriopt its consider to be synchronous. If the JavaScript is external to the html document the blocking is exacerbated because the JavaScript must first be downloaed before it can be parsed. In the code example below I comment what is occuring during browser rendering when the browser encoutners several _<script>_ elements in the DOM.

```
<!DOCTYPE html>
<html lang="en">
<body>

<!-- stop document parsing, block document parsing, load js, exectue js, then resume document parsing... -->
<script src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js"></script>

<!-- stop document parsing, block document parsing, exectue js, then resume document parsing... -->
<script>console.log('hi');</script>


</body>
</html>
```

You should make note of the differences between an inline script's and external scripts as it pertains to the loading phase.

## Notes

The default blocking nature of a _<script>_ element can have a significant effect on the perfomrance & percived performance of the visual rendering of a HTML web page. If you have a couple of script elements at the start of an html page nothing else is happening (e.g. DOM parsing & resource loading) until each one is downloaed and executed sequentially.

# 10.3 Defering the downloading & exectuion of external JavaScript using _defer_

The _<script>_ element has an attribute called _defer_ that will defer the blocking, downloading, and executing of an external JavaScript file until the browser has parsed the closing _<html>_ node. Using this attribute simply defers what normally occurs when a web browser encoutners a _<script>_ node. In the code below I defer each external JavaScript file until the final _<html>_ is encountered.

```
<!DOCTYPE html>
<html lang="en">
<body>

<!-- defer, don't block just ignore this until the <html> element node is parsed -->
<script defer src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js"></script>

<!-- defer, don't block just ignore this until the <html> element node is parsed -->
<script defer src="http://cdnjs.cloudflare.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>

<!-- defer, don't block just ignore this until the <html> element node is parsed -->
<script defer src="http://cdnjs.cloudflare.com/ajax/libs/jquery-mousewheel/3.0.6/jquery.mousewheel.min.js"></script>

<script>
//We know that jQuery is not avaliable because this occurs before the closing <html> element
console.log(window['jQuery'] === undefined); //logs true

//Only after everything is loaded can we safley conclude that jQuery was loaded and parsed
document.body.onload = function(){console.log(jQuery().jquery)}; //logs function
</script> 

</body>
</html>
```

## Notes

According to the specification defered scripts are suppose to be exectued in document order and before the _DOMContentLoaded_ event. However, adherence to this specification among modern browsers is inconsistent.

_defer_ is a boolan attribute it does not have a value

Some browers support defered inline scripts but this is not common among modern browsers

By using _defer_ the assummption is that _document.write()_ is not being used in the JavaScript that will be defered

# 10.4 Asynchronously downloading & executing external JavaScript files using _async_

The _<script>_ element has an attribute called _async_ that will override the sequential blocking nature of _<script>_ elements when the DOM is being constructed by a web browser. By using this attribute, we are telling the browser not to block the construction (i.e. DOM parsing, including downloading other assets e.g. images, style sheets, etc...) of the html page and forgo the the sequential loading as well.

What happens by using the _async_ attribute is the files are loaded in parallel and parsed in order of download once they are fully downloaded. In the code below I comment what is happening when the HTML document is being parsed and render by the web browser.

```
<!DOCTYPE html>
<html lang="en">
<body>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script async src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js"></script>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script async src="http://cdnjs.cloudflare.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script async src="http://cdnjs.cloudflare.com/ajax/libs/jquery-mousewheel/3.0.6/jquery.mousewheel.min.js"></script>


<script>
// we have no idea if jQuery has been loaded yet likley not yet...
console.log(window['jQuery'] === undefined);//logs true

//Only after everything is loaded can we safley conclude that jQuery was loaded and parsed
document.body.onload = function(){console.log(jQuery().jquery)};
</script> 

</body>
</html>
```

## Notes

IE 10 has support for _async_, but IE 9 does not

A major drawback to using the _async_ attribute is JavaScript files potentially get parsed out of the order they are included in the DOM. This raises a dependency management issue.

_async_ is a boolan attribute it does not have a value

By using _async_ the assummption is that _document.write()_ is not being used in the JavaScript that will be defered

The _async_ attribute will trump the _defer_ if both are used on a _<script>_ element

# 10.5 Forcing asynchronous downloading & parsing of external JavaScript using dynamic _<script>_

A known hack for forcing a web browser into asynchronous JavaScript downloading and parsing without using the _async_ attribure is to programatically create _<script>_ elements that include external JavaScript files and insert them in the DOM. In the code below I programatically create the _<script>_ element node and then append it to the _<body>_ element which forces the browser to treat the _<script>_ element asynchronously.

```
<!DOCTYPE html>
<html lang="en">
<body>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script>
var underscoreScript = document.createElement("script"); 
underscoreScript.src = "http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js"; 
document.body.appendChild(underscoreScript);
</script>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script>
var jqueryScript = document.createElement("script");
jqueryScript.src = "http://cdnjs.cloudflare.com/ajax/libs/jquery/1.7.2/jquery.min.js"; 
document.body.appendChild(jqueryScript);
</script>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script>
var mouseWheelScript = document.createElement("script");
mouseWheelScript.src = "http://cdnjs.cloudflare.com/ajax/libs/jquery-mousewheel/3.0.6/jquery.mousewheel.min.js"; 
document.body.appendChild(mouseWheelScript);
</script>

<script>
//Only after everything is loaded can we safley conclude that jQuery was loaded and parsed
document.body.onload = function(){console.log(jQuery().jquery)};
</script>

</body>
</html>
```

## Notes

A major drawback to using dynamic _<script>_ elements is JavaScript files potentially get parsed out of the order they are included in the DOM. This raises a dependency management issue.

# 10.6 Using the _onload_ call back for asynchronous _<script>_'s so we know when its loaded

The _<script>_ element [supports a load event](http://pieisgood.org/test/script-link-events/) handler (ie. _onload_) that will execute once an external JavaScript file has been loaded and executed. In the code below I leverage the _onload_ event to create a callback programatically notifying us when the JavaScript file has been downloaded and exectued.

```
<!DOCTYPE html>
<html lang="en">
<body>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script>
var underscoreScript = document.createElement("script"); 
underscoreScript.src = "http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js";
underscoreScript.onload = function(){console.log('underscsore is loaded and exectuted');};
document.body.appendChild(underscoreScript);
</script>

<!-- Don't block, just start downloading and then parse the file when it's done downloading -->
<script async src="http://cdnjs.cloudflare.com/ajax/libs/jquery/1.7.2/jquery.min.js" onload="console.log('jQuery is loaded and exectuted');"></script>

</body>
</html>
```

# 10.7 Be mindful of _<script>_ 's placement in HTML for DOM manipulation

Given a _<script>_ elements synchronous nature, placing one in the _<head>_ element of an HTML document presents a timing problem if the JavaScript execution is dependant upon any of the DOM that proceeds the _<script>_. In a nut shell, if JavaScript is executed at the begining of a document that manipulates the DOM, that proceeds it, you are going to get a JavaScript error. Proven by the following code example:

live code: N/A

```
<!DOCTYPE html>
<html lang="en">
<head>
<!-- stop parsing, block parsing, exectue js then resume... -->
<script>
//we can't script the body element yet, its null, not even been parsed by the browser, its not in the DOM yet 
console.log(document.body.innerHTML); //logs Uncaught TypeError: Cannot read property 'innerHTML' of null 
</script>
</head>
<body>
<strong>Hi</strong>
</body>
</html>
```

Many developers, myself being one of them, for this reason will attempt to place all _<script>_ elements before the closing _</body>_ element. By doing this you can rest assured the DOM in front of the _<script>_'s has been parsed and is ready for scripting. As well, this strategy will remove a dependancy on DOM ready events that can liter a code base.

# 10.8 Getting a list of _<script>_'s in the DOM

The _document.scripts_ property avaliable from the document object provides a list (i.e. an _HTMLCollection_) of all of the scripts currently in the DOM. In the code below I leverage this property to gain access to each of the _<script>_ elements _src_ attributes.

live code: N/A

```
<!DOCTYPE html>
<html lang="en">
<body>
<script src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.3/underscore-min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/jquery-mousewheel/3.0.6/jquery.mousewheel.min.js"></script>

<script>​
Array.prototype.slice.call(document.scripts).forEach(function(elm){
    console.log(elm); 
});//will log each script element in the document
</script> 

</body>
</html>
```

# Chapter 11 - DOM Events

# 11.1 DOM events overview

An event, in terms of the DOM, is either a pre-defined or custom moment in time that occurs in relationship with an element in the DOM, the _document_ object, or the _window_ object. These moments are typically predetermined and programaticlly accounted for by associating functionality (i.e. handlers/callbacks) to occur when these moments in time come to pass. These moments can be initiated by that state of the UI (e.g. input is focused or something has been dragged), the state of the enviroment that is running the JavaScript program (e.g. page is loaded or XHR request has finished), or the state of the program itself (e.g. start monitor users ui interaction for 30 seconds after the page has loaded).

Setting up events can be accomplished using inline attribute event handlers, property event handlers, or the _addEventListener()_ method. In the code below I'm demonstrating these three patterns for setting up an event. All three patterns add a _click_ event that is invoked whenever the _<div>_ in the html document is clicked by the mouse.

```
<!DOCTYPE html>
<html lang="en">

<!-- inline attribure event handler pattern -->
<body onclick="console.log('fire/trigger attribure event handler')">

<div>click me</div>

<script>
var elementDiv = document.querySelector('div');

// property event handler pattern
elementDiv.onclick = function(){console.log('fire/trigger property event handler')};

//addEventListener method pattern
elementDiv.addEventListener('click',function(){console.log('fire/trigger addEventListener')}, false);
</script> 
</body>
</html>
```

Notice that one of the events is attached to the _<body>_ element. If you find it odd that the attribute event handler on the _<body>_ fires by clicking the _<div>_ element consider that when the _<div>_ is clicked, are you not also clicking on the _<body>_ element. Click anywhere but on the _<div>_ and you still see the attribute handler fire on the _<body>_ element alone.

While all three of these patterns for attaching an event to the DOM programatically schedule the event, only the _addEventListener()_ provides a robust and organized solution. The inline attribute event handler mixes together JavaScript and HTML and best practices advise keeping these things seperate.

The downside to using a property event handler is that only one value can be assigned to the event property at a time. Meaning, you can't add more than one propety event handler to a DOM node when assigning events as property values. The code below shows an example of this by assigning a value to the _onclick_ property twice, the last value set is used when the event is invoked.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>
var elementDiv = document.querySelector('div');

// property event handler 
elementDiv.onclick = function(){console.log('I\'m first, but I get overidden/replace')};

//overrides/replaces the prior value
elementDiv.onclick = function(){console.log('I win')};

</script> 
</body>
</html>
```

Additionaly, using event handlers inline or property event handlers can suffer from scoping nuances as one attempts to leverage the scope chain from the function that is invoked by the event. The _addEventListener()_ smooths out all of these issues, and will be used throughout this chapter.

## Notes

_Element_ nodes typically support inline event handlers (e.g. _<div onclick=""></div>_), property event handlers (e.g. _document.querySelector('div').onclick = function(){}_), and the use of the _addEventListener()_ method.

The _Document_ node supports property event handlers (e.g. _document.onclick = funciton()_) and the use of the _addEventListener()_ method.

The _window_ object supports inline event handler's via the _<body>_ or _<frameset>_ element (e.g. _<body onload=""></body>_), property event handlers (e.g. _window.load = function(){}_), and the use of the _addEventListener()_ method.

A property event handler historically has been refered to as a "DOM level 0 event". And the _addEventListener()_ is often refered to as a "DOM level 2 event". Which is rather confusing considering there is no level 0 event or level 1 event. Addintioanlly, inline event handlers are known to be called, "HTML event handlers".

# 11.2 DOM event types

In the tables below I detail the most common pre-defined events that can be attached to _Element_ nodes, the _document_ object, and the _window_ object. Of course not all events are directly applicable to the node or object it can be attached too. That is, just because you can attach the event without error, and most likley invoke the event (i.e. bubbling events like _onchange_ to _window_), does not mean that adding something like _window.onchange_ is logical given that this event, by design was not meant for the _window_ object.

**User interface events**

Event Type    | Event Interface    | Description                                                                                                                                                                                       | Event Targets                                                             | Bubbles | Cancelable
------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ------- | ----------
_load_        | _Event_, _UIEvent_ | fires when an asset (HTML page, image, CSS, frameset, _<object>_, or JS file) is loaded.                                                                                                          | _Element_, _Document_, _window_, _XMLHttpRequest_, _XMLHttpRequestUpload_ | No      | No
_unload_      | _UIEvent_          | fires when user agent removes the resource (document, element, defaultView) or any depending resources (images, CSS file, etc.)                                                                   | _window_, _<body>_, _<frameset>_                                          | No      | No
_abort_       | _Event_, _UIEvent_ | Fires when an resource (object/image) is stopped from loading before completely loaded                                                                                                            | _Element_, _XMLHttpRequest_, _XMLHttpRequestUpload_                       | Yes     | No
_error_       | _Event_, _UIEvent_ | Fires when a resource failed to load, or has been loaded but cannot be interpreted according to its semantics, such as an invalid image, a script execution error, or non-well-formed XML         | _Element_, _XMLHttpRequest_, _XMLHttpRequestUpload_                       | Yes     | No
_resize_      | _UIEvent_          | Fires when a document view has been resized. This event type is dispatched after all effects for that occurrence of resizing of that particular event target have been executed by the user agent | _window_, _<body>_, _<frameset>_                                          | Yes     | No
_scroll_      | _UIEvent_          | Fires when a user scrolls a document or an element.                                                                                                                                               | _Element_, _Document_, _window_                                           | Yes     | No
_contextmenu_ | _MouseEvent_       | fires by right clicking an element                                                                                                                                                                | _Element_                                                                 | Yes     | Yes

**Focus events**

Event Type | Event Interface | Description                                                                                                                          | Events Targets                                             | Bubbles | Cancelable
---------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- | ------- | ----------
_blur_     | _FocusEvent_    | Fires when an element loses focus either via the mouse or tabbing                                                                    | _Element_ (except _<body>_ and _<frameseet>_ ), _Document_ | No      | No
_focus_    | _FocusEvent_    | Fires when an element receives focus                                                                                                 | _Element_ (except _<body>_ and _<frameseet>_ ), _Document_ | No      | No
_focusin_  | _FocusEvent_    | Fires when an event target is about to receive focus but before the focus is shifted. This event occurs right before the focus event | _Element_                                                  | Yes     | No
_focusout_ | _FocusEvent_    | Fires when an event target is about to lose focus but before the focus is shifted. This event occurs right before the blur event     | _Element_                                                  | Yes     | No

**Form events**

Event Type | Event Interface        | Description                                                                                    | Event Targets | Bubbles | Cancelable
---------- | ---------------------- | ---------------------------------------------------------------------------------------------- | ------------- | ------- | ----------
_change_   | specific to HTML forms | Fires when a control loses the input focus and its value has been modified since gaining focus | _Element_     | Yes     | No
_reset_    | specific to HTML forms | Fires when a form is reset                                                                     | _Element_     | Yes     | No
_submit_   | specific to HTML forms | Fires when a form is submitted                                                                 | _Element_     | Yes     | Yes
_select_   | specific to HTML forms | Fires when a user selects some text in a text field, including input and textarea              | _Element_     | Yes     | No

**Mouse events**



































































































































































































































Event Type   | Event Interface | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Event Targets                   | Bubbles | Cancelable
------------ | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- | ------- | ----------
_click_      | _MouseEvent_    | Fires when mouse pointer is clicked (or user presses enter key) over an element. A click is defined as a mousedown and mouseup over the same screen location. The sequence of these events is _mousedown_>_mouseup_>_click_. Depending upon the environment configuration, the click event may be dispatched if one or more of the event types mouseover, mousemove, and mouseout occur between the press and release of the pointing device button. The click event may also be followed by the dblclick event | _Element_, _Document_, _window_ | Yes     | Yes
_dblclick_   | _MouseEvent_    | Fires when a mouse pointer is clicked twice over an element. The definition of a double click depends on the environment configuration, except that the event target must be the same between _mousedown_, _mouseup_, and _dblclick_. This event type must be dispatched after the event typeclick if a click and double click occur simultaneously, and after the event type _mouseup_ otherwise                                                                                                               | _Element_, _Document_, _window_ | Yes     | Yes
_mousedown_  | _MouseEvent_    | Fires when mouse pointer is pressed over an element                                                                                                                                                                                                                                                                                                                                                                                                                                                             | _Element_, _Document_, _window_ | Yes     | Yes
_mouseenter_ | _MouseEvent_    | Fires when mouse pointer is moved onto the boundaries of an element or one of its descendent elements. This event type is similar to mouseover, but differs in that it does not bubble, and must not be dispatched when the pointer device moves from an element onto the boundaries of one of its descendent elements                                                                                                                                                                                          | _Element_, _Document_, _window_ | No      | No
_mouseleave_ | _MouseEvent_    | Fires when mouse pointer is moved off of the boundaries of an element and all of its descendent elements. This event type is similar to mouseout, but differs in that does not bubble, and that it must not be dispatched until the pointing device has left the boundaries of the element and the boundaries of all of its children                                                                                                                                                                            | _Element_, _Document_, _window_ | No      | No
_mousemove_  | _MouseEvent_    | Fires when mouse pointer is moved while it is over an element. The frequency rate of events while the pointing device is moved is implementation-, device-, and platform-specific, but multiple consecutive mousemove events should be fired for sustained pointer-device movement, rather than a single event for each instance of mouse movement. Implementations are encouraged to determine the optimal frequency rate to balance responsiveness with performance                                           | _Element_, _Document_, _window_ | Yes     | No
_mouseout_   | _MouseEvent_    | Fires when mouse pointer is moved off of the boundaries of an element. This event type is similar to _mouseleave_, but differs in that does bubble, and that it must be dispatched when the pointer device moves from an element onto the boundaries of one of its descendent elements                                                                                                                                                                                                                          | _El_
ement, _Document_, _window_

Yes

Yes



_mouseup_

_MouseEvent_

Fires when mouse pointer button is released over an element

_Element_, _Document_, _window_

Yes

Yes



_mouseover_

_MouseEvent_

Fires when mouse pointer is moved over an element

_Element_, _Document_, _window_

Yes

Yes

**Wheel events**

Event Type                                                             | Event Interface | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | Event Targets                   | Bubbles | Cancelable
---------------------------------------------------------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- | ------- | ----------
_wheel_ (browsers use _mousewheel_ but the specification uses _wheel_) | _WheelEvent_    | Fires when a mouse wheel has been rotated around any axis, or when an equivalent input device (such as a mouse-ball, certain tablets or touchpads, etc.) has emulated such an action. Depending on the platform and input device, diagonal wheel deltas may be delivered either as a singlewheel event with multiple non-zero axes or as separate wheel events for each non-zero axis. Some helpful details about browser support can be found [here](http://www.quirksmode.org/dom/events/scroll.html). | _Element_, _Document_, _Window_ | Yes     | Yes

**Keyboard events**

Event Type | Event Interface | Description                                                                                                                                                                                                               | Event Targets         | Bubbles | Cancelable
---------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- | ------- | ----------
_keydown_  | _KeyboardEvent_ | Fires when a key is initially pressed. This is sent after any key mapping is performed, but before any input method editors receive the keypress. This is sent for any key, even if it doesn't generate a character code. | _Element_, _Document_ | Yes     | Yes
_keypress_ | _KeyboardEvent_ | Fires when a key is initially pressed, but only if that key normally produces a character value. This is sent after any key mapping is performed, but before any input method editors receive the keypress.               | _Element_, _Document_ | Yes     | Yes
_keyup_    | _KeyboardEvent_ | Fires when a key is released. This is sent after any key mapping is performed, and always follows thecorresponding _keydown_ and _keypress_ events.                                                                       | _Element_, _Document_ | Yes     | Yes

**Touch events**

Event Type    | Event Interface | Description                                                                                                                                                                                                                                                                                                    | Event Targets                   | Bubbles | Cancelable
------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- | ------- | ----------
_touchstart_  | _TouchEvent_    | Fires event to indicate when the user places a touch point on the touch surface                                                                                                                                                                                                                                | _Element_, _Document_, _window_ | Yes     | Yes
_touchend_    | _TouchEvent_    | Fires event to indicate when the user removes a touch poin[t](http://www.w3.org/TR/2011/WD-touch-events-20110505/#dfn-touch-point) from the touch surface, also including cases where the touch point physically leaves the touch surface, such as being dragged off of the screen                             | _Element_, _Document_, _window_ | Yes     | Yes
_touchmove_   | _TouchEvent_    | Fires event to indicate when the user moves a touch point along the touch surface                                                                                                                                                                                                                              | _Element_, _Document_, _window_ | Yes     | Yes
_touchenter_  | _TouchEvent_    | Fires event to indicate when a touch point moves onto the interactive area defined by a DOM element                                                                                                                                                                                                            | _Element_, _Document_, _window_ | No      | ?
_toucheleave_ | _TouchEvent_    | Fires event to indicate when a touch point moves off the interactive area defined by a DOM element                                                                                                                                                                                                             | _Element_, _Document_, _window_ | No      | ?
_touchcancel_ | _TouchEvent_    | Fires event to indicate when a touch point has been disrupted in an implementation-specific manner, such as a synchronous event or action originating from the UA canceling the touch, or the touch point leaving the document window into a non-document area which is capable of handling user interactions. | _Element_, _Document_, _window_ | Yes     | No

# Notes

Touch events are typically only supported iOS, Andorid, and Blackberry browsers or browsers (e.g. chrome) that can switch on touch modes

**Window, _<body>_, and frame specific events**

Event Type     | Event Interface       | Description                                                                                                | Event Targets                    | Bubbles | Cancelable
-------------- | --------------------- | ---------------------------------------------------------------------------------------------------------- | -------------------------------- | ------- | ----------
_afterprint_   | ?                     | Fires on the object immediately after its associated document prints or previews for printing              | _window_, _<body>_, _<frameset>_ | No      | No
_beforeprint_  | ?                     | Fires on the object before its associated document prints or previews for printing                         | _window_, _<body>_, _<frameset>_ | No      | No
_beforeunload_ | ?                     | Fires prior to a document being unloaded                                                                   | _window_, _<body>_, _<frameset>_ | No      | Yes
_hashchange_   | _HashChangeEvent_     | Fires when there are changes to the portion of a URL that follows the number sign (#)                      | _window_, _<body>_, _<frameset>_ | No      | No
_messsage_     | ?                     | Fires when the user sends a cross-document message or a message is sent from a _Worker_ with _postMessage_ | _window_, _<body>_, _<frameset>_ | No      | No
_offline_      | _NavigatorOnLine_     | Fires when browser is working offline                                                                      | _window_, _<body>_, _<frameset>_ | No      | No
_online_       | _NavigatorOnLine_     | Fires when browser is working online                                                                       | _window_, _<body>_, _<frameset>_ | No      | No
_pagehide_     | _PageTransitionEvent_ | Fires when traversing from a session history entry                                                         | _window_, _<body>_, _<frameset>_ | No      | No
_pageshow_     | _PageTransitionEvent_ | The pagehide event is fired when traversing from a session history entry                                   | _window_, _<body>_, _<frameset>_ | No      | No

**Document specific events**

Event Type         | Event Interface | Description                                                                               | Event Targets                | Bubbles | Cancelable
------------------ | --------------- | ----------------------------------------------------------------------------------------- | ---------------------------- | ------- | ----------
_readystatechange_ | _Event_         | Fires event when _readyState_ is changed                                                  | _Document_, _XMLHttpRequest_ | No      | No
_DOMContentLoaded_ | _Event_         | Fires when a webpage has been parsed, but before all resources have been fully downloaded | _Document_                   | Yes     | No

**Drag events**

Event Type  | Event Interface | Description                                                                                                                                                                                                           | Event Targets                   | Bubbles | Cancelable
----------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- | ------- | ----------
_drag_      | _DragEvent_     | Fires on the source object continuously during a drag operation.                                                                                                                                                      | _Element_, _Document_, _window_ | Yes     | Yes
_dragstart_ | _DragEvent_     | Fires on the source object when the user starts to drag a text selection or selected object. The ondragstart event is the first to fire when the user starts to drag the mouse.                                       | _Element_, _Document_, _window_ | Yes     | Yes
_dragend_   | _DragEvent_     | Fires on the source object when the user releases the mouse at the close of a drag operation. The ondragend event is the final drag event to fire, following the ondragleave event, which fires on the target object. | _Element_, _Document_, _window_ | Yes     | No
_dragenter_ | _DragEvent_     | Fires on the target element when the user drags the object to a valid drop target.                                                                                                                                    | _Element_, _Document_, _window_ | Yes     | Yes
_dragleave_ | _DragEvent_     | Fires on the target object when the user moves the mouse out of a valid drop target during a drag operation.                                                                                                          | _Element_, _Document_, _window_ | Yes     | No
_dragover_  | _DragEvent_     | Fires on the target element continuously while the user drags the object over a valid drop target. The ondragover event fires on the target object after the ondragenter event has fired.                             | _Element_, _Document_, _window_ | Yes     | Yes
_drop_      | _DragEvent_     | Fires on the target object when the mouse button is released during a drag-and-drop operation. The ondrop event fires before the ondragleave and ondragend events.                                                    | _Element_, _Document_, _window_ | Yes     | Yes

# Notes

The tables below were crafted from the following three resources [Document Object Model (DOM) Level 3 Events Specification 5 User Event Module](http://www.w3.org/TR/DOM-Level-3-Events/#events-module), [DOM event reference](https://developer.mozilla.org/en/DOM/DOM_event_reference), [HTML Living Standard 7.1.6 Event handlers on elements, Document objects, and Window objects,](http://www.whatwg.org/specs/web-apps/current-work/multipage/webappapis.html#events), and [Event compatibility tables](http://www.quirksmode.org/dom/events/).

I've only mentioned here in this section the most common event types. Keep in mind there are numerous HTML5 api's that I've excluded from the this section (e.g. [media events](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#event-definitions) for _<video>_ and _<audio>_ elements or all state change events for the [XMLHttpRequest Level 2](http://www.w3.org/TR/XMLHttpRequest/#event-handlers)).

The _copy_, _cut_, and _textinput_ event are not defined by DOM 3 events or HTML5

Use mouseenter and mouseleave instead of mouseover and mouseout. Unfortunately Firefox, Chrome, and Safari still haven't added these events!

## 11.3 The event flow

When an event is invoked the [event flows or propagates through the DOM](http://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture), firing the same event on other nodes and JavaScript objects. The event flow can be programmed to occur as a capture phase (i.e. DOM tree trunk to branch) or bubbling phase (i.e. DOM tree branches to trunk), or both.

In the code below I set up 10 event listeners that can all be invoked, due to the event flow, by clicking once on the _<div>_ element in the HTML document. When the _<div>_ is clicked the capture phase begins at the _window_ object and propagates down the DOM tree firing the _click_ event for each object (i.e. _window_ **>** _document_ **>** _<html>_ **>** _<body>_ **>** event target) until it hits the event target. Once the capture phase ends the target phase starts, firing the _click_ event on the target element itself. Next the propagation phase propagates up from the event target firing the _click_ event until it reaches the _window_ object (i.e. event target **>** _<body>_ **>** _<html>_ **>** _document_ **>** _window_). With this knowledge it should be obvious why clicking the _<div>_ in the code example logs to the console 1,2,3,4,5,6,7,8,9,11.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me to start event flow</div>

<script>

/*notice that I am passing the addEventListener() a boolean parameter of true so capture events fire, not just bubbling events*/

//1 capture phase
window.addEventListener('click',function(){console.log(1);},true);

//2 capture phase
document.addEventListener('click',function(){console.log(2);},true);

//3 capture phase
document.documentElement.addEventListener('click',function(){console.log(3);},true);

//4 capture phase
document.body.addEventListener('click',function(){console.log(4);},true);

//5 target phase occurs during capture phase
document.querySelector('div').addEventListener('click',function(){console.log(5);},true);

//6 target phase occurs during bubbling phase
document.querySelector('div').addEventListener('click',function(){console.log(6);},false);

//7 bubbling phase
document.body.addEventListener('click',function(){console.log(7);},false);

//8 bubbling phase
document.documentElement.addEventListener('click',function(){console.log(8);},false);

//9 bubbling phase
document.addEventListener('click',function(){console.log(9);},false);

//10 bubbling phase
window.addEventListener('click',function(){console.log(10)},false);

</script> 
</body>
</html>
```

After the _<div>_ is clicked, the event flow proceeds in this order:

1. capture phase invokes click events on window that are set to fire on capture
2. capture phase invokes click events on document that are set to fire on capture
3. capture phase invokes click events on html element that are set to fire on capture
4. capture phase invokes click events on body element that are set to fire on capture
5. target phase invokes click events on div element that are set to fire on capture
6. target phase invokes click events on div element that are set to fire on bubble
7. bubbling phase invokes click events on body element are set to fire on bubble
8. bubbling phase invokes click events on html element are set to fire on bubble
9. bubbling phase invokes click events on document are set to fire on bubble
10. bubbling phase invokes click events on window are set to fire on bubble

The use of the capture phase is not all that common due to a lack of browser support for this phase. Typically events are assumed to be inovked during the bubbling phase. In the code below I remove the capture phase from the previous code example and demostrate typically what is occuring during an event invocation.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me to start event flow</div>

<script>

//1 target phase occurs during bubbling phase
document.querySelector('div').addEventListener('click',function(){console.log(1);},false);

//2 bubbling phase
document.body.addEventListener('click',function(){console.log(2);},false);

//3 bubbling phase
document.documentElement.addEventListener('click',function(){console.log(3);},false);

//4 bubbling phase
document.addEventListener('click',function(){console.log(4);},false);

//5 bubbling phase
window.addEventListener('click',function(){console.log(5)},false);

</script> 
</body>
</html>
```

Notice in the last code example that if the click event is initiated (click anywhere except on the _<div>_) on the _<body>_ element the click event attached to the _<div>_ is not invoked and bubbling invocation starts on the _<body>_. This is due to the fact the the event target is no longer the _<div>_ but instead the _<body>_ element.

### Notes

Modern browsers do support the use of the capture phase so what was once considered unreliable might just server some value today. For example, one could intercept an event before it occurs on the event target.

Keep this knowledge of event capturing and bubbling at the forefront of your thoughts when you read the event delegation section of this chapter.

The event object passed to event listener functions contains a _eventPhase_ property containing a number which indicates which phase an event is inoked in. A value of 1 indicates the capture phase. A value of 2 indicates the target phase. And a value of 3 indicates bubbling phase.

## 11.4 Adding event listeners to _Element_ nodes, _window_ object, and _Document_ object

The _addEventListener()_ method is avaliabe on all _Element_ nodes, the _window_ object, and the _document_ object providing the ability to added event listeners to parts of an HTML document as well as JavaScript objects relating to the DOM and [BOM](https://developer.mozilla.org/en-US/docs/DOM/window) (browser object model). In the code below I leverage this method to add a _mousemove_ event to a _<div>_ element, the _document_ object, and the _window_ object. Notice, due to the event flow, that mouse movement specifically over the _<div>_ will invoke all three listeners each to time a movement occurs.

```
<!DOCTYPE html>
<html lang="en">

<body>

<div>mouse over me</div>

<script>

//add a mousemove event to the window object, invoking the event during the bubbling phase
window.addEventListener('mousemove',function(){console.log('moving over window');},false);

//add a mousemove event to the document object, invoking the event during the bubbling phase
document.addEventListener('mousemove',function(){console.log('moving over document');},false);

//add a mousemove event to a <div> element object, invoking the event during the bubbling phase
document.querySelector('div').addEventListener('mousemove',function(){console.log('moving over div');},false);

</script> 
</body>
</html>
```

The _addEventListener()_ method used in the above code example takes three arguments. The first argument is the type of event to listen for. Notice that the event type string does not contain the "on" prefix (i.e. _onmousemove_) that event handlers require. The second argument is the function to be invoked when the event occurs. The third parameter is a boolean indicating if the event should be fired during the capture phase or bubbling phase of the event flow.

### Notes

I've purposfully avoided dicussing inline event handlers & property event handlers in favor of promoting the use of _addEventListener()_

Typically a developer wants events to fire during the bubbling phase so that object eventing handles the event before bubbling the event up the DOM. Because of this you almost always provide a _false_ value as the last argument to the _addEventListener()_. In modern browsers if the 3rd parameter is not specified it will default to false.

You should be aware that the _addEventListener()_ method can be used on the _XMLHttpRequest_ object

## 11.5 Removing event listeners

The _removeEventListener()_ method can be used to remove events listeners, if the orginal listener was not added using an anonymous function. In the code below I add two events listeners to the HTML document and attempt to remove both of them. However, only the listener that was attached using a function reference is removed.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click to say hi</div>

<script>

var sayHi = function(){console.log('hi')};

//adding event listener using anonymous function
document.body.addEventListener('click',function(){console.log('dude');},false);

//adding event listener using function reference
document.querySelector('div').addEventListener('click',sayHi,false);

//attempt to remove both event listeners, but only the listener added with a funtions reference is removed
document.querySelector('div').removeEventListener('click',sayHi,false);

//this of course does not work as the function passed to removeEventListener is a new and different function
document.body.removeEventListener('click',function(){console.log('dude');},false);

//clicking the div will still invoke the click event attached to the body element, this event was not removed

</script> 
</body>
</html>
```

Anonymous functions added using _addEventListener()_ method simply cannot be removed.

## 11.6 Getting event properties from the event object

The handler or callback function invoked for events is sent by default a parameter that contains all relevant information about an event itself. In the code below I demostrate access to this event object and log all of its properties and values for a load event as well as a click event. Make sure you click the _<div_> to see the properties assocaited with a click event.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

document.querySelector('div').addEventListener('click',function(event){
Object.keys(event).sort().forEach(function(item){
     console.log(item+' = '+event[item]); //logs event propeties and values
});     
},false);

//assumes 'this' is window
this.addEventListener('load',function(event){
Object.keys(event).sort().forEach(function(item){
     console.log(item+' = '+event[item]); //logs event propeties and values
});     
},false);

</script> 
</body>
</html>
```

Keep in mind that each event will contain slightly different properties based on the event type (e.g. [MouseEvent](https://developer.mozilla.org/en/DOM/MouseEvent), [KeyboardEvent](https://developer.mozilla.org/en/DOM/KeyboardEvent), [WheelEvent](https://developer.mozilla.org/en/DOM/WheelEvent)).

### Notes

The event object also provides the _stopPropagation()_, _stopImediatePropagation()_, and _preventDefault()_ methods.

In this book I use the argument name _event_ to reference the event object. In truth you can use any name you like and its not uncommon to see _e_ or _evt_.

## 11.7 The value of _this_ when using _addEventListener()_

The value of _this_ inside of the event listener function passed to the _addEventListener()_ method will be a reference to the node or object the event is attached too. In the code below I attach an event to a _<div>_ and then using _this_ inside of the event listener gain access to the _<div>_ element the event is attached too.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

document.querySelector('div').addEventListener('click',function(){
// 'this' will be the element or node the event listener is attached too
console.log(this); //logs '<div>' 
},false);

</script> 
</body>
</html>
```

When events are invoked as part of the event flow the _this_ value will remain the value of the node or object that the event listener is attached too. In the code below we add a _click_ event listener to the _<body>_ and regardless of if you click on the _<div>_ or the _<body>_ the value of _this_ always points to _<body>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

//click on the <div> or the <body> the value of this remains the <body> element node
document.body.addEventListener('click',function(){
console.log(this); //log <body>...</body>
},false);

</script> 
</body>
</html>
```

Additionally its possible using the _event.currentTarget_ property to get the same reference, to the node or object invoking the event listener, that the _this_ property provides. In the code below I leverage the _event.currentTarget_ event object property showcasing that it returns the same value as _this_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

document.addEventListener('click',function(event){
console.log(event.currentTarget); //logs '#document'
//same as...
console.log(this);
},false);

document.body.addEventListener('click',function(event){
console.log(event.currentTarget); //logs '<body>'
//same as...
console.log(this);
},false);

document.querySelector('div').addEventListener('click',function(event){
console.log(event.currentTarget); //logs '<div>'
//same as...
console.log(this);
},false);

</script> 
</body>
</html>
```

## 11.8 Referencing the _target_ of an event and not the node or object the event is invoked on

Because of the event flow its possible to click a _<div>_, contained inside of a _<body>_ element and have a _click_ event listener attached to the _<body>_ element get invoked. When this happens, the event object passed to the event listener function attached to the _<body>_ provides a reference (i.e. _event.target_) to the node or object that the event originated on (i.e. the target). In the code below when the _<div>_ is clicked, the _<body>_ element's _click_ event listener is invoked and the _event.target_ property references the orginal _<div>_ that was the target of the click event. The _event.target_ can be extremely useful when an event that fires because of the event flow needs knowledge about the origin of the event.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

document.body.addEventListener('click',function(event){
//when the <div> is clicked logs '<div>' because the <div> was the target in the event flow
console.log(event.target); 
},false);

</script> 
</body>
</html>
```

Consider that in our code example if the _<body>_ element is clicked instead of the _<div>_ then the event _target_ and the element node that the event listener is invoked on are the same. Therefore _event.target_, _this_, and _event.currentTarget_ will all contain a reference to the _<body>_ element.

## 11.9 Cancelling default browser events using _preventDefault()_

Browsers provide several events already wired up when an HTML page is presented to a user. For example, clicking a link has a corresponding event (i.e. you navigate to a url). So does clicking a checkbox (i.e. box is checked) or typing text into a text field (i.e. text is inputed and appears on screen). These browser events can be prevented by calling the _preventDefault()_ method inside of the event handler function associated with a node or object that invokes a browser default event. In the code below I prevent the default event that occurs on a _<a>_, _<input>_, and _<textarea>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<a href="google.com">no go</div>

<input type="checkbox" />

<textarea></textarea>

<script>

document.querySelector('a').addEventListener('click',function(event){
event.preventDefault(); //stop the default event for <a> which would be to load a url
},false);

document.querySelector('input').addEventListener('click',function(event){
event.preventDefault(); //stop default event for checkbox, which would be to toggle checkbox state
},false);

document.querySelector('textarea').addEventListener('keypress',function(event){
event.preventDefault(); //stop default event for textarea, which would be to add characters typed
},false);

/*keep in mind that events still propagate, clicking the link in this html document will stop the default event but not event bubbling*/
document.body.addEventListener('click',function(){
console.log('the event flow still flows!');
},false);

</script> 
</body>
</html>
```

All attempts to click the link, check the box, or type in the text input in the previous code example will fail because I am preventing the default events for these elements from occuring.

### Notes

The _preventDefault()_ methods does not stop events from propagating (i.e. bubbling or capture phases)

Providing a _return false_ at the end of the body of the event listener has the same result as call the _preventDefault()_ method

The event object passed to event listener functions contains a boolean _cancelable_ property which indicates if the event will respond to preveentDefault() method and canceling default behavior

The event object passed to event listener functions contains a _defaultPrevented_ property which indicates true if _preventDefault()_ has been invoked for a bubbling event.

## 11.10 Stoping the event flow using _stopPropagation()_

Calling _stopProgagation()_ from within an event handler/listener will stop the capture and bubble event flow phases, but any events directly attached to the node or object will still be invoked. In the code below the _onclick_ event attached to the _<body>_ is never gets invoked because we are stopping the event from bubbling up the DOM when clicking on the _<div>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

document.querySelector('div').addEventListener('click',function(){
console.log('me too, but nothing from the event flow!');
},false);

document.querySelector('div').addEventListener('click',function(event){
console.log('invoked all click events attached, but cancel capture and bubble event phases');
event.stopPropagation();
},false);

document.querySelector('div').addEventListener('click',function(){
console.log('me too, but nothing from the event flow!');
},false);

/*when the <div> is clicked this event is not invoked because one of the events attached to the <div> stops the capture and bubble flow.*/
document.body.addEventListener('click',function(){
console.log('What, denied from being invoked!');
},false);

</script> 
</body>
</html>
```

Notice that other click events attached to the the _<div>_ still get invoked! Additionally using _stopPropagation()_ does not prevent default events. Had the _<div>_ in our code example been a _<a>_ with an href value calling stopPropagation would not have stopped the browser default events from getting invoked.

## 11.11 Stoping the event flow as well as other like events on the same target using _stopImmediatePropagation()_

Calling the _stopImmediatePropagation()_ from within an event handler/listener will stop the event flow phases (i.e. _stopPropagation()_), as well as any other like events attached to the event target that are attached after the event listener that invokes the _stopImmediatePropagation()_ method. In the code example below If we call _stopImmediatePropagation()_from the second event listener attached to the _<div>_ the click event that follows will not get invoked.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>

<script>

//first event attached
document.querySelector('div').addEventListener('click',function(){
console.log('I get invoked because I was attached first');
},false);

//seond event attached
document.querySelector('div').addEventListener('click',function(event){
console.log('I get invoked, but stop any other click events on this target');
event.stopImmediatePropagation();
},false);

//third event attached, but because stopImmediatePropagation() was called above this event does not get invoked
document.querySelector('div').addEventListener('click',function(){
console.log('I get stopped from the previous click event listener');
},false);

//notice that the event flow is also cancelled as if stopPropagation was called too
document.body.addEventListener('click',function(){
console.log('What, denied from being invoked!');
},false);

</script> 
</body>
</html>
```

### Notes

Using the _stopImmediatePropagation()_ does not prevent default events. Browser default events still get invoked and only calling _preventDefault()_ will stop these events.

## 11.12 Custom events

A developer is not limited to the predefined event types. Its possible to attach and invoke a custom event, using the _addEventListener()_ method like normal in combiniation with _document.createEvent()_, _initCustomEvent()_, and _dispatchEvent()_. In the code below I create a custom event called _goBigBlue_ and invoke that event_._

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>click me</div>​​​​​

<script>

var divElement = document.querySelector('div');//create the custom event
var cheer = document.createEvent('CustomEvent'); //the 'CustomEvent' parameter is required//create an event listener for the custom event
divElement.addEventListener('goBigBlue',function(event){
    console.log(event.detail.goBigBlueIs)
},false);/*Use the initCustomEvent method to setup the details of the custom event.
Parameters for initCustomEvent are: (event, bubble?, cancelable?, pass values to event.detail)*/
cheer.initCustomEvent('goBigBlue',true,false,{goBigBlueIs:'its gone!'});

//invoke the custom event using dispatchEvent
divElement.dispatchEvent(cheer);​

</script> 
</body>
</html>
```

## 11.13 Simulating/Triggering mouse events

Simiulating an event is not unlike creating a custom event. In the case of simulating a mouse event we create a _'MouseEvent'_ using _document.createEvent()_. Then, using _initMouseEvent()_ we setup the mouse event that is going to occur. Next the mouse event is dispatched on the element that we'd like to simulate an event on (i.e the _<div>_ in the html document). In the code below a click event is attached to the _<div>_ in the page. Instead of clicking the _<div>_ to invoke the click event the event is triggered or simulated by programatically setting up a mouse event and dispatching the event to the _<div>_.

```
<!DOCTYPE html>
<html lang="en">
<body>

<div>no need to click, we programatically trigger it</div>​​​​

<script>

var divElement = document.querySelector('div');

//setup click event that will be simulated
divElement.addEventListener('click',function(event){
    console.log(Object.keys(event));
},false);//create simulated mouse event 'click'
var simulateDivClick = document.createEvent('MouseEvents');

/*setup simulated mouse 'click'
initMouseEvent(type,bubbles,cancelable,view,detail,screenx,screeny,clientx,clienty,ctrlKey,altKey,shiftKey,metaKey,button,relatedTarget)*
simulateDivClick.initMouseEvent('click',true,true,document.defaultView,0,0,0,0,0,false,false,false,0,null,null);

//invoke simulated clicked event
divElement.dispatchEvent(simulateDivClick);</script> 
</body>
</html>
```

### Notes

Simulating/triggering mouse events as of this writing works in all modern browsers. Simulating other event types quickly becomes more complicated and leveraging [simulate.js](https://github.com/airportyh/simulate.js) or jQuery (e.g. jQuery _trigger()_ method) becomes neccsary.

## 11.14 Event delegation

Event delegation, stated simply, is the programmatic act of leveraging the event flow and a single event listener to deal with multiple event targets. A side effect of event delegation is that the event targets don't have to be in the DOM when the event is created in order for the targets to respond to the event. This is of course rather handy when dealing with XHR responses that update the DOM. By implementing event delegation new content that is added to the DOM post JavaScript load parsing can immediately start responding to events. Imagine you have a table with an unlimited number of rows and columns. Using event delegation we can add a single event listener to the _<table>_ node which acts as a delegate for the node or object that is the initial target of the event. In the code example below, clicking any of the _<td>_'s (i.e. the target of the event) will delegate its event to the _click_ listener on the _<table>_. Don't forget this is all made possible because of the event flow and in this specific case the bubbling phase.

```
<!DOCTYPE html>
<html lang="en">
<body>

<p>Click a table cell</p>

<table border="1">
    <tbody>
        <tr><td>row 1 column 1</td><td>row 1 column 2</td></tr>
        <tr><td>row 2 column 1</td><td>row 2 column 2</td></tr>
        <tr><td>row 3 column 1</td><td>row 3 column 2</td></tr>
        <tr><td>row 4 column 1</td><td>row 4 column 2</td></tr>
        <tr><td>row 5 column 1</td><td>row 5 column 2</td></tr>
        <tr><td>row 6 column 1</td><td>row 6 column 2</td></tr>
    </tbody>
</table>​​​​​​​​

<script>

document.querySelector('table').addEventListener('click',function(event){
    if(event.target.tagName.toLowerCase() === 'td'){ //make sure we only run code if a td is the target
        console.log(event.target.textContent); //use event.target to gain access to target of the event which is the td 
    }      
},false);

</script> 
</body>
</html>
```

If we were to update the table in the code example with new rows, the new rows would responded to the _click_ event as soon as they were render to the screen because the click event is delegated to the _<table>_ element node.

### Notes

Event delegation is ideally leverage when you are dealing with a _click_, _mousedown_, _mouseup_, _keydown_, _keyup_, and _keypress_ event type.

## Chapter 12 - Creating dom.js - a wishful jQuery inspired DOM Library for modern browers

## 12.1 dom.js overview

I want you to take the information and knowledge from this book and leverage it as I walk you through a foundation for a wishful, modern, jQuery like DOM library called dom.js. Think of dom.js as the foundation to a modern library for selecting DOM nodes and doing something with them. Not unlike jQuery the dom.js code will provide a function for selecting something from the DOM (or creating) and then doing something with it. I show some examples of the _dom()_ function below which shouldn't look all that foreign if you are familiar with jQuery or really any DOM utility for selecting elements.

```
//select in a document all li's in the first ul and get the innerHTML for the first li
dom('li','ul').html();

//create html structure using a document fragment and get the innerHTML of ul
dom('<ul><li>hi</li></ul>').html()
```

For most readers this chapter is simply an exercise in taking the information in this book and applying it to a JavaScript DOM library. For others, this might just shed some light on jQuery itself and any DOM manipulation logic used in JavaScript frameworks today. Ideally, in the end, I hope this exercise inspires readers to craft their own micro DOM abstractions on an as needed bases when the situation is right. With that said, lets begin.

## 12.2 Create a unique scope

To protect our dom.js code from the global scope, I will first create a unique scope to which it can live and operate within without fear of collisions in the global scope. In the code below I setup a pretty standard [Immediately-Invoked Function Expression](http://benalman.com/news/2010/11/immediately-invoked-function-expression/) to create this private scope. When the IIFE is invoked the value of _global_ will be set to the current global scope (i.e. _window_).

```
(function(win){ 

var global = win;
var doc = this.document;

}}(window);
```

Inside of the IIFE we setup a reference to the _window_ and _document_ object (i.e. _doc_) to speed up the access to these objects inside of the IIFE.

## 12.3 Create the _dom()_ and _GetOrMakeDom()_ functions exposing _dom()_ and _GetOrMakeDom.prototype_ to the global scope

Just like jQuery we are going to create a function that will return a chain-able, wrapped set (i.e. custom array like object) of DOM nodes (e.g. _{0:ELEMENT_NODE,1:ELEMENT_NODE,length:2}_) based on the parameters sent into the function. In the code below I setup the _dom()_ function and parameters which get passed on to the _GetOrMakeDOM_ constructor function that when invoked will return the object containing the DOM nodes, that is then returned by from _dom()_.

```
(function(win){ 

var global = win;
var doc = global.document;

var dom = function(params,context){
    return new GetOrMakeDom(params,context);
};

var GetOrMakeDom = function(params,context){

};

})(window);
```

In order for the _dom()_ function to be accessed/called from outside of the private scope setup by the IIFE we have to expose the dom function (i.e. create a reference) to the global scope. This is done by creating a property in the global scope called _dom_ and pointing that property to the local _dom()_ function. When _dom_ is accessed from the global scope it will point to my locally scoped _dom()_ function. In the code below doing, _global.dom = dom;_ does the trick.

```
(function(win){

var global = win;
var doc = global.document;

var dom = function(params,context){
    return new GetOrMakeDom(params,context);
};

var GetOrMakeDom = function(params,context){

};

//expose dom to global scope
global.dom = dom;

})(window);
```

The last thing we need to do is expose the _GetOrMakeDom.prototype_ property to the global scope. Not unlike jQuery (e.g. _jQuery.fn_) we are simply going to provide a shortcut reference from _dom.fn_ to _GetOrMakeDOM.prototype_. This is shown in the code below.

```
(function(win){

var global = win;
var doc = global.document;

var dom = function(params,context){
    return new GetOrMakeDom(params,context);
};

var GetOrMakeDom = function(params,context){

};

//expose dom to global scope
global.dom = dom;

//short cut to prototype
dom.fn = GetOrMakeDom.prototype;

})(window);
```

Now anything attached to the _dom.fn_ is actually a property of the _GetOrMakeDOM.prototype_ object and is inherited during property lookup for any object instance created from the _GetOrMakeDOM_ constructor function.

### Notes

The _getOrMakeDom_ function is invoked with the _new_ operator. Make sure you [understand](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/new) what happens when a function is invoked using the _new_ operator.

## 12.4 Create optional context paramater passed to _dom()_

When _dom()_ is invoked, it also invokes the _GetOrMakeDom_ function passing it the parameters that are sent to _dom()_. When the _GetOrMakeDOM_ constructor is invoked the first thing we need to do is determine context. The context for working with the DOM can be set by passing a selector string used to select a node or a node reference itself. If its not obvious the purpose of passing a context to the _dom()_ function provides the ability to limit the search for element nodes to a specific branch of the DOM tree. This is very similar, almost identical, to the second parameter passed to the _jQuery_ or _$_ function. In the code below I default the context to the current document found in the global scope. If a context parameter is available, I determine what it is (i.e. string or node) and either make the node passed in the context or select a node via _querySelectorAll()_.

```
(function(win){

var global = win;
var doc = global.document;

var dom = function(params,context){
    return new GetOrMakeDom(params,context);
};

var GetOrMakeDom = function(params,context){

    var currentContext = doc;
        if(context){
            if(context.nodeType){//its either a document node or element node
                currentContext = context;
            }else{ //else its a string selector, use it to selector a node
                currentContext = doc.querySelector(context);
        }
    }

};

//expose dom to global scope
global.dom = dom;

//short cut to prototype
dom.fn = GetOrMakeDom.prototype;

})(window);
```

With the _context_ parameter logic setup we can next add the logic required to deal with the _params_ parameter used to actually select or created nodes.

## 12.5 Populate object with DOM node references based on _params_ and return object

The _params_ parameter passed to _dom()_, then on to the _getOrMakeDom()_ varies in the type of parameter that can be passed. Similar to jQuery the type's of value's passed can be any one of the following:

- css selector string (e.g. _dom('body')_)
- html string (e.g. _dom('<p>Hellow</p><p> World!</p>')_)
- _Element_ node (e.g. _dom(document.body)_)
- array of element nodes (e.g. _dom([document.body])_)
- a _NodeList_ (e.g. _dom(document.body.children)_)
- a _HTMLcollection_ (e.g. _dom(document.all)_)
- a _dom()_ object itself. (e.g. _dom(dom())_)

The result of passing _params_ is the construction of a chain-able object containing references to nodes (e.g. _{0:ELEMENT_NODE,1:ELEMENT_NODE,length:2})_ either in the DOM or in a document fragment. Lets examine how each of the above parameters can be used to produce an object containing node references.

The logic to permit such a wide variety of parameter types is shown below in the code and starts with a simple check to verify that _params_ is not _undefined_, an empty string, or a string with empty spaces. If this is the case we add a _length_ property with a value of _0_ to the object constructed from calling _GetOrMakeDOM_ and return the object so the execution of the function ends. If _params_ is not a false ([or false like](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/)) value the execution of the function continues.

Next the _params_ value, if a string, is checked to see if contains HTML. If the string contains HTML then a [document fragmen](https://developer.mozilla.org/en-US/docs/DOM/DocumentFragment)t is created and the string is used as the _innerHTML_ value for a _<div>_ contained in the document fragment so that the string is converted to a DOM structure. With the html string converted to a node tree, the structure is looped over accessing top level nodes, and references to these nodes are passed to the object being created by _GetOrMakeDom_. If the string does not contain HTML execution of the function continues.

The next check simply verifies if _params_ is a reference to a single node and if it is we wrap a reference to it up in an object and return it other wise at we are pretty sure the _params_ value is a [html collection](https://developer.mozilla.org/en-US/docs/DOM/HTMLCollection), [node list](https://developer.mozilla.org/en-US/docs/DOM/NodeList), array, string [selector](http://www.quirksmode.org/css/contents.html), or an object created from _dom()_. If its a string selector, a node list is created by calling the _queryselectorAll()_ method on the _currentContext_. If its not a string selector we loop over the html collection, node list, array, or object extracting the node references and using the references as values contained in the object sent back from calling the _GetOrMakeDom_.

All of this logic inside of _GetOrMakeDom()_ function can be a bit overwhelming just realize that the point of the constructor function is to construct an object containing references to nodes (e.g. _{0:ELEMENT_NODE,1:ELEMENT_NODE,length:2}_) and returns this object to _dom()_.

```
(function(win){

var global = win;
var doc = global.document;

var dom = function(params,context){
    return new GetOrMakeDom(params,context);
};

var regXContainsTag = /^\s*<(\w+|!)[^>]*>/;

var GetOrMakeDom = function(params,context){

    var currentContext = doc;
    if(context){
        if(context.nodeType){
            currentContext = context;
        }else{
            currentContext = doc.querySelector(context);
        }
    }

    //if no params, return empty dom() object
    if(!params || params === '' || typeof params === 'string' && params.trim() === ''){
        this.length = 0;
        return this;
    }

    //if HTML string, construct domfragment, fill object, then return object
    if(typeof params === 'string' && regXContainsTag.test(params)){//yup its forsure html string
        //create div & docfrag, append div to docfrag, then set its div's innerHTML to the string, then get first child
        var divElm = currentContext.createElement('div');
        divElm.className = 'hippo-doc-frag-wrapper';
        var docFrag = currentContext.createDocumentFragment();
        docFrag.appendChild(divElm);
        var queryDiv = docFrag.querySelector('div');
        queryDiv.innerHTML = params;
        var numberOfChildren = queryDiv.children.length;
        //loop over nodelist and fill object, needs to be done because a string of html can be passed with siblings
        for (var z = 0; z //give the object a length value
        this.length = numberOfChildren;
        //return object
        return this; //return e.g. {0:ELEMENT_NODE,1:ELEMENT_NODE,length:2}
    }

    //if a single node reference is passed, fill object, return object
    if(typeof params === 'object' && params.nodeName){
        this.length = 1;
        this[0] = params;
        return this;
    }

    //if its an object but not a node assume nodelist or array, else its a string selector, so create nodelist
    var nodes;
    if(typeof params !== 'string'){//nodelist or array
        nodes = params;
    }else{//ok string
        nodes = currentContext.querySelectorAll(params.trim());
    }
    //loop over array or nodelist created above and fill object
    var nodeLength = nodes.length;
    for (var i = 0; i //give the object a length value
    this.length = nodeLength;
    //return object
    return this; //return e.g. {0:ELEMENT_NODE,1:ELEMENT_NODE,length:2}

};

//expose dom to global scope
global.dom = dom;

//short cut to prototype
dom.fn = GetOrMakeDom.prototype;

})(window);
​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​
```

## 12.6 Create _each()_ method and make it a chainable method

When we invoke _dom()_ we can access anything attached to _dom.fn_ by way of prototypical inheritance. (e.g. _dom().each())_. Not unlike jQuery methods attached to _dom.fn_ operate on the object created from the _GetOrMakeDom_ constructor function. The code below setups the _each()_ method.

```
dom.fn.each = function (callback) {
 var len = this.length; //the specific instance create from getOrMakeDom() and returned by calling dom()
    for(var i = 0; i < len; i++){
        //invoke the callback function setting the value of this to element node and passing it parameters
     callback.call(this[i], i, this[i]);
 }
};​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​
```

As you might expect the _each()_ method takes a callback function as a parameter and invokes the function (setting the _this_ value to the element node object with _call()_) for each node element in the _getOrMakeDom_ object instance. The _this_ value inside of the _each()_ function is a reference to the _getOrMakeDom_ object instance (e.g. _{0:ELEMENT_NODE,1:ELEMENT_NODE,length:2}_).

When a method does not return a value (e.g. _dom().length_ returns a length) its possible to allow method chaning by simply returning the object the method belongs too instead of a specific value. Basically, we are returning the _GetOrMakeDom_ object so another method can be called on this instance of the object. In the code below I would like the _each()_ method to be chainable, meaning more methods can be called after calling _each()_, so I simply return _this_. The _this_ in the code below is the object instance created from calling the _getOrMakeDom_ function.

```
dom.fn.each = function (callback) {
 var len = this.length;
  for(var i = 0; i < len; i++){
        callback.call(this[i], i, this[i]);
 }
    return this; //make it chainable by returning e.g. {0:ELEMENT_NODE,1:ELEMENT_NODE,length:2}
};​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​
```

## 12.7 Create _html()_, _append()_, & _text()_ methods

With the core _each()_ method created and implicit iteration avaliable we can now build out a few _dom()_ methods that act on the nodes we select from an HTML document or create using document fragments. The three methods we are going to create are:

- _html()_ / _html('html string')_
- _text()_ / _text('text string')_
- _append('html | text | dom() | nodelist/HTML collection | node | array')_

The _html()_ and _text()_ methods follow a very similar pattern. If the method is called with a parameter value we loop (using _dom.fn.each()_ for implicit iteration ) over each element node in the _getOrMakeDom_ object instance setting either the _innerHTML_ value or _textContent_ value. If no parameter is sent we simply return the _innerHTML_ or _textContent_ value for the first element node in the _getOrMakeDom_ object instance. Below you will see this logic coded.

```
dom.fn.html = function(htmlString){
    if(htmlString){
        return this.each(function(){ //notice I return this so its chainable if called with param
            this.innerHTML = htmlString;
        });
    }else{
        return this[0].innerHTML;
    }
};

dom.fn.text = function(textString){
    if(textString){
        return this.each(function(){ //notice I return this so its chainable if called with param
            this.textContent = textString;
        });
    }else{
        return this[0].textContent.trim();
    }
};
```

The _append()_ method leveraging _insertAdjacentHTML_ will take a an html string, text string, _dom()_ object, nodelist/HTML collection a single node or array of nodes and appends it to the nodes selected.

```
dom.fn.append = function(stringOrObject){
 return this.each(function(){
        if(typeof stringOrObject === 'string'){
         this.insertAdjacentHTML('beforeend',stringOrObject);
        }else{
          var that = this;
            dom(stringOrObject).each(function(name,value){
              that.insertAdjacentHTML('beforeend',value.outerHTML);
           });
     }
   });
};
```

## 12.8 Taking dom.js for a spin

During the creation of [dom.js I created some very simple qunit tests](https://github.com/codylindley/domjs/tree/master/test) that we are now going to run outside of the testing framework. However, you can also [run the testing framework](https://github.com/codylindley/domjs/blob/master/test/index.html) to see dom.js in action. The follow code demostrates the code create in this chapter.

```
<!DOCTYPE html>
<html lang="en">
<body>

<ul>
<li>1</li>
<li>2</li>
<li>3</li>
</ul>

<script src="https://raw.github.com/codylindley/domjs/master/builds/dom.js"></script>
<script>

//dom()
console.log(dom());
console.log(dom(''));
console.log(dom('body'));
console.log(dom('<p>Hellow</p><p> World!</p>'));
console.log(dom(document.body));
console.log(dom([document.body, document.body]));
console.log(dom(document.body.children));
console.log(dom(dom('body')));

//dom().html()
console.log(dom('ul li:first-child').html('one'));
console.log(dom('ul li:first-child').html() === 'one');

//dom().text()
console.log(dom('ul li:last-child').text('three'));
console.log(dom('ul li:last-child').text() === 'three');

//dom().append()
dom('ul').append('<li>4</li>');
dom('ul').append(document.createElement('li'));
dom('ul').append(dom('li:first-child'));

</script> 
</body>
</html>
```

## 12.9 Summary & continuing on with dom.js

This chapter has been about creating a foundation to a jQuery like DOM library. If you'd like to continue studying the building blocks to a jQuery-like DOM library I would suggest checking out [hippo.js](https://github.com/codylindley/hippojs), which is an exercise in re-creating the jQuery DOM methods for modern browsers. Both [dom.js](https://github.com/codylindley/domjs) and [hippo.js](https://github.com/codylindley/hippojs) make use of [grunt](http://gruntjs.com/), [QUnit](http://qunitjs.com/), and [JS Hint](http://jshint.com/) which I highly recommend looking into if building your own JavaScript libraries is of interest. In addition to the fore mentioned developer tools I highly recommending reading, "[Designing Better JavaScript APIs](http://coding.smashingmagazine.com/2012/10/09/designing-javascript-apis-usability/)". Now go build something for the DOM.
