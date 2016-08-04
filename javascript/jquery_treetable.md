# jQuery treetable

[Original URL](http://ludo.cubicphuse.nl/jquery-treetable/)

> Download the latest release from the jQuery Plugin Registry or grab the source code from Github. Please report issues through Github issues. This plugin is released under both the MIT and the GPLv2...

**[Download](http://plugins.jquery.com/treetable) the latest release from the jQuery Plugin Registry or grab the [source code](https://github.com/ludo/jquery-treetable) from Github.** Please [report issues](https://github.com/ludo/jquery-treetable/issues) through Github issues. This plugin is released under both the MIT and the GPLv2 license by [Ludo van den Boom](http://ludovandenboom.com).

Tree column                                                     | Additional data
--------------------------------------------------------------- | ----------------------------
Node 1: Click on the icon in front of me to expand this branch. | I live in the second column.
Node 1.1: Look, I am a table row _and_ I am part of a tree!     | Interesting.
Node 1.1.1: I am part of the tree too!                          | That's it!
Node 2: I am another root node, but without children            | Hurray!

## Table of Contents

1. [Overview](http://ludo.cubicphuse.nl/jquery-treetable/#overview)
2. [Usage](http://ludo.cubicphuse.nl/jquery-treetable/#usage)
3. [Configuration](http://ludo.cubicphuse.nl/jquery-treetable/#configuration)
4. [Public API](http://ludo.cubicphuse.nl/jquery-treetable/#api)
5. [Examples](http://ludo.cubicphuse.nl/jquery-treetable/#examples)
6. [Test Suite](http://ludo.cubicphuse.nl/jquery-treetable/test.html)
7. [CHANGELOG.txt](http://ludo.cubicphuse.nl/jquery-treetable/CHANGELOG.txt)

## [1\. Overview]()

jQuery treetable is a plugin for [jQuery](http://jquery.com), the 'Write Less, Do More, JavaScript Library'. With this plugin you can display a tree in an HTML table, e.g. a directory structure or a nested list. Why not use a list, you say? Because lists are great for displaying a tree, and tables are not. Oh wait, but this plugin uses tables, doesn't it? Yes. Why do I use a table to display a list? Because I need multiple columns to display additional data besides the tree.

### Unobtrusiveness

One of the goals of jQuery treetable is to be as unobtrusive as possible. Being 'unobtrusive' is very cool nowadays, so that was an important requirement. But it is cool for a reason: it keeps your HTML documents clean and it allows the code to degrade nicely when Javascript is not available.

The treetable plugin only requires that you add specific `data` attributes to every row that is part of your tree. The plugin uses these attributes to determine what your tree looks like. Otherwise, it would have to guess the structure of the tree and it wouldn't be very successful in doing that. See the [Usage](http://ludo.cubicphuse.nl/jquery-treetable/#usage) chapter for more information on how to prepare your tree.

### Features

- It can display a tree of data in a table column.
- It does this as unobtrusively as possible.
- It allows branches to be collapsed and expanded (think of how a directory structure works in most file explorers).
- It allows unlimited tree depth.
- It uses the lightweight [jQuery](http://jquery.com) Javascript libray.

## [2\. Usage]()

**Note:** This chapter assumes that you have already installed jQuery as described on their website.

### [Include the plugin in your document]()

The plugin can either be installed manually or by using the [Bower](http://bower.io) package manager.

#### Installing with Bower

```
bower install jquery-treetable
```

Note that the only listed dependency is jQuery. If you also want to use the drag & drop feature you'll have to install jQuery UI yourself.

#### Installing manually

Copy the files `jquery.treetable.js` and `css/jquery.treetable.css` to your project. Paste the following code right before the closing `body` tag in your HTML document. Make sure these lines are **below** the line where you include jQuery. Change the **red** parts to reflect your situation.

```
<link href="path/to/jquery.treetable.css" rel="stylesheet" type="text/css" />
<script src="path/to/jquery.treetable.js"></script>
<script>
$("#your_table_id").treetable();
</script>
```

### [Styling the tree]()

By default the tree has little styling. Use the file `css/jquery.treetable.theme.default.css` as a template for your own styling, or just use this default theme.

### [Representing your tree in a table]()

When you pasted the above code and adjusted it to reflect your situation, you enabled the _possibility_ of displaying a tree in your table. To make the tree actually display as a tree you have to add `data-tt-id` and `data-tt-parent-id` attributes to your table rows (`tr`).

#### How to do this?

First, you should add a _unique_ `data-tt-id` attribute to each of the rows in your table, for example '`node-x`'. Then you add a `data-tt-parent-id` attribute to each child of a node, give this class a value of '`node-x`'. The _node-x_ part should be the same as the `data-tt-id` of its parent. Do you still follow me? Let me show you an example of a very simple tree: a single parent with a single child. For more examples you should view the source code of this page, where you find several tables for the examples in the [examples](http://ludo.cubicphuse.nl/jquery-treetable/#examples) chapter.

```
<table>
 <tr data-tt-id="1">
 <td>Parent</td>
 </tr>
 <tr data-tt-id="2" data-tt-parent-id="1">
 <td>Child</td>
 </tr>
</table>
```

Please note that the plugin expects the rows in the HTML table to be in the same order in which they should be displayed in the tree. For example, suppose you have three nodes: **A**, **B** (child of node 

<span>A</span>

) and **C** (child of node 

<span>B</span>

). If you create rows for these nodes in your HTML table in the following order 

<span>A</span>

 - 

<span>C</span>

 - 

<span>B</span>

, then the tree will not display correctly. You have to make sure that the rows are in the order 

<span>A</span>

 - 

<span>B</span>

 - 

<span>C</span>

.

## [3\. Configuration]()

There are several settings that let you adjust the behavior of the plugin. Each of these settings is described in this section. Pass these options and callback functions to the `treetable()` function. See the [examples](http://ludo.cubicphuse.nl/jquery-treetable/#examples).

### Settings

Setting              | Type     | Default                          | Description
-------------------- | -------- | -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
`branchAttr`         | `string` | `"ttBranch"`                     | Optional data attribute that can be used to force the expander icon to be rendered on a node. This allows us to define a node as a branch node even though it does not have children yet. This translates to a `data-tt-branch` attribute in your HTML.
`clickableNodeNames` | `bool`   | `false`                          | Set to true to expand branches not only when expander icon is clicked but also when node name is clicked.
`column`             | `int`    | `0`                              | The number of the column in the table that should be displayed as a tree.
`columnElType`       | `string` | `"td"`                           | The types of cells that should be considered for the tree (`td`, `th` or both).
`expandable`         | `bool`   | `false`                          | Should the tree be expandable? An expandable tree contains buttons to make each branch with children collapsible/expandable.
`expanderTemplate`   | `string` | `<a href="#"> </a>`              | The HTML fragment used for the expander.
`indent`             | `int`    | `19`                             | The number of pixels that each branch should be indented with.
`indenterTemplate`   | `string` | `<span class="indenter"></span>` | The HTML fragment used for the indenter.
`initialState`       | `string` | `"collapsed"`                    | Possible values: `"expanded"` or `"collapsed"`.
`nodeIdAttr`         | `string` | `"ttId"`                         | Name of the data attribute used to identify node. Translates to `data-tt-id` in your HTML.
`parentIdAttr`       | `string` | `"ttParentId"`                   | Name of the data attribute used to set parent node. Translates to `data-tt-parent-id` in your HTML.
`stringCollapse`     | `string` | `"Collapse"`                     | For internationalization.
`stringExpand`       | `string` | `"Expand"`                       | For internationalization.

### Events

Setting             | Type       | Default | Description
------------------- | ---------- | ------- | -----------------------------------------------------------
`onInitialized`     | `function` | `null`  | Callback function fired when the tree has been initialized.
`onNodeCollapse`    | `function` | `null`  | Callback function fired when a branch is collapsed.
`onNodeExpand`      | `function` | `null`  | Callback function fired when a branch is expanded.
`onNodeInitialized` | `function` | `null`  | Callback function fired when a node has been initialized.

## [4\. Public API]()

### `treetable()` Plugin Function

The `treetable()` function accepts the following arguments:

`options` (optional)<br>
A Javascript object of configuration settings as described in the chapter on [configuration](http://ludo.cubicphuse.nl/jquery-treetable/#configuration).

`force` (optional)<br>
Pass the boolean `true` to force reinitialization of the tree.

### Additional Functions

Use the following functions to manipulate the tree programmatically. Calling a function should be done through the `treetable()` function. For example, to collapse node '42' use `$("#tree").treetable("collapseNode", "42")`.

`collapseAll()`<br>
Collapse all nodes at once.

`collapseNode(id)`<br>
Collapse a single node, identified by `id`.

`expandAll()`<br>
Expand all nodes at once.

`expandNode(id)`<br>
Expand a single node, identified by `id`.

`loadBranch(node, rows)`<br>
Load additional rows (HTML `<tr>`s) into the tree, with parent `node`. If `node` is `null` rows will be added as root nodes.

`move(nodeId, destinationId)`<br>
Move node `nodeId` to new parent with `destinationId`.

`node(id)`<br>
Select a node from the tree. Returns a `TreeTable.Node` object.

`removeNode(id)`<br>
Remove a node and all its descendants from the tree.

`reveal(id)`<br>
Reveal a node in the tree.

`sortBranch(node)`, `sortBranch(node, columnOrFunction)`<br>
Sort `node`'s children alphabetically. Defaults to sorting on the values in the configured tree column (see settings). Pass an optional column number or sorting function as the second argument `columnOrFunction`. See [the tests](http://ludo.cubicphuse.nl/jquery-treetable/test.html?grep=treetable()%20sortBranch()) for examples of custom sorting functions. Does not recursively sort children of children.

`unloadBranch(node)`<br>
Remove nodes/rows (HTML `<tr>`s) from the tree, with parent `node`. Note that the parent (`node`) will not be removed.

### Classes

The following classes are dynamically added to the tree rows: `expanded`<br>
When the row is expanded

`collapsed`<br>
When the row is collapsed

`branch`<br>
When the row has children or the branchAttr is present

`leaf`<br>
When the row has no children

## [5\. Examples]()

### Basic Static Tree

| app
| -------------------------
| controllers
| application_controller.rb
| helpers
| models
| views

```
$("#example-basic-static").treetable();
```

### Basic Expandable Tree

| app
| -------------------------
| controllers
| application_controller.rb
| helpers
| models
| views

```
$("#example-basic-expandable").treetable({ expandable: true });
```

### Complex Tree With Drag and Drop

This example uses [jQuery UI](http://jqueryui.com)'s Draggable and Droppable components. The tree has 459 nodes.

Examples: `2-1-1`, `3-1-1-2-2` and `3-2-1-2-3-1-2-2-1-1-1-1-2-5`



















































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































 <



Name                                                                              | Kind   | Size
--------------------------------------------------------------------------------- | ------ | -----------
<span class="file">Acknowledgements.rtf</span>                                    | File   | 480.95 KB
<span class="folder">CHUD</span>                                                  | Folder | –
<span class="folder">amber</span>                                                 | Folder | –
<span class="file">AmberTraceFormats.pdf</span>                                   | File   | 124.46 KB
<span class="folder">BigTop</span>                                                | Folder | –
<span class="file">BigTopUserGuide.pdf</span>                                     | File   | 1314.71 KB
<span class="folder">Saturn</span>                                                | Folder | –
<span class="file">SaturnUserGuide.pdf</span>                                     | File   | 694.29 KB
<span class="folder">Shark</span>                                                 | Folder | –
<span class="file">SharkUserGuide.pdf</span>                                      | File   | 12902.51 KB
<span class="folder">simg4</span>                                                 | Folder | –
<span class="folder">simg4_plus</span>                                            | Folder | –
<span class="folder">simg5</span>                                                 | Folder | –
<span class="folder">DocSets</span>                                               | Folder | –
<span class="folder">com.apple.ADC_Reference_Library.CoreReference.docset</span>  | Folder | –
<span class="folder">Contents</span>                                              | Folder | –
<span class="file">Info.plist</span>                                              | File   | 1.23 KB
<span class="folder">Resources</span>                                             | Folder | –
<span class="file">docSet.dsidx</span>                                            | File   | 41504 KB
<span class="file">docSet.skidx</span>                                            | File   | 43072 KB
<span class="folder">Documents</span>                                             | Folder | –
<span class="folder">documentation</span>                                         | Folder | –
<span class="folder">Accessibility</span>                                         | Folder | –
<span class="folder">Reference</span>                                             | Folder | –
<span class="folder">AccessibilityCarbonRef</span>                                | Folder | –
<span class="folder">CarbonAXRefRevisions</span>                                  | Folder | –
<span class="file">CarbonAXRefRevisions.html</span>                               | File   | 7.44 KB
<span class="folder">Index</span>                                                 | Folder | –
<span class="file">index_of_book.html</span>                                      | File   | 174.1 KB
<span class="file">index.html</span>                                              | File   | 1.1 KB
<span class="folder">Reference</span>                                             | Folder | –
<span class="file">reference.html</span>                                          | File   | 196.28 KB
<span class="file">toc.html</span>                                                | File   | 15.92 KB
<span class="folder">AccessibilityLowlevel</span>                                 | Folder | –
<span class="folder">accessibility</span>                                         | Folder | –
<span class="file">CompositePage.html</span>                                      | File   | 5.7 KB
<span class="file">index.html</span>                                              | File   | 1.67 KB
<span class="file">toc.html</span>                                                | File   | 2.87 KB
<span class="file">accessibility-constants.html</span>                            | File   | 26.94 KB
<span class="file">accessibility-datatypes.html</span>                            | File   | 11.02 KB
<span class="file">accessibility-functions.html</span>                            | File   | 15.55 KB
<span class="file">accessibility-mpindex.html</span>                              | File   | 9.87 KB
<span class="folder">AXActionConstants</span>                                     | Folder | –
<span class="file">CompositePage.html</span>                                      | File   | 15.08 KB
<span class="file">index.html</span>                                              | File   | 1.67 KB
<span class="file">toc.html</span>                                                | File   | 4.9 KB
<span class="folder">AXAttributeConstants</span>                                  | Folder | –
<span class="folder">AXError</span>                                               | Folder | –
<span class="folder">AXNotificationConstants</span>                               | Folder | –
<span class="folder">AXRoleConstants</span>                                       | Folder | –
<span class="folder">AXTextAttributedString</span>                                | Folder | –
<span class="folder">AXUIElement</span>                                           | Folder | –
<span class="folder">AXValue</span>                                               | Folder | –
<span class="folder">AXValueConstants</span>                                      | Folder | –
<span class="file">index.html</span>                                              | File   | 10.1 KB
<span class="folder">UniversalAccess</span>                                       | Folder | –
<span class="file">adcstyle.css</span>                                            | File   | 15.86 KB
<span class="folder">AppleApplications</span>                                     | Folder | –
<span class="folder">Reference</span>                                             | Folder | –
<span class="folder">AddressBookC_Collection</span>                               | Folder | –
<span class="folder">Index</span>                                                 | Folder | –
<span class="file">index_of_book.html</span>                                      | File   | 153.98 KB
<span class="file">index.html</span>                                              | File   | 13.62 KB
<span class="folder">Introduction</span>                                          | Folder | –
<span class="file">Introduction.html</span>                                       | File   | 5.41 KB
<span class="file">RevisionHistory.html</span>                                    | File   | 4.66 KB
<span class="folder">AddressBookRefUpdate</span>                                  | Folder | –
<span class="folder">Articles</span>                                              | Folder | –
<span class="file">AddressBook_10.1-10.2_SymbolChanges.html</span>                | File   | 80.84 KB
<span class="file">AddressBook_10.2-10.3_SymbolChanges.html</span>                | File   | 54.2 KB
<span class="file">AddressBook_10.3-10.4_SymbolChanges.html</span>                | File   | 14.78 KB
<span class="file">AddressBook_10.4-10.5_SymbolChanges.html</span>                | File   | 11.03 KB
<span class="file">Introduction.html</span>                                       | File   | 7.3 KB
<span class="file">RevisionHistory.html</span>                                    | File   | 6.19 KB
<span class="file">index.html</span>                                              | File   | 1.08 KB
<span class="file">toc.html</span>                                                | File   | 2.84 KB
<span class="folder">AMWorkflow_class</span>                                      | Folder | –
<span class="folder">AMWorkflowController_class</span>                            | Folder | –
<span class="folder">AMWorkflowView_Class</span>                                  | Folder | –
<span class="folder">AppleApp_Aperture_002</span>                                 | Folder | –
<span class="folder">Automator_constants</span>                                   | Folder | –
<span class="folder">AutomatorFramework</span>                                    | Folder | –
<span class="folder">AutomatorReference</span>                                    | Folder | –
<span class="folder">AutomatorRefUpdate</span>                                    | Folder | –
<span class="folder">CalendarStoreFramework</span>                                | Folder | –
<span class="folder">CalendarStoreReference</span>                                | Folder | –
<span class="folder">CalendarStoreRefUpdate</span>                                | Folder | –
<span class="folder">Dashboard_Ref</span>                                         | Folder | –
<span class="folder">FinalCutPro_XML</span>                                       | Folder | –
<span class="folder">InstantMessageFramework</span>                               | Folder | –
<span class="folder">InstantMessageFrameworkRef</span>                            | Folder | –
<span class="folder">InstantMessageRefUpdate</span>                               | Folder | –
<span class="folder">iSyncJavaScriptRef</span>                                    | Folder | –
<span class="folder">iSyncManualTestSuiteRef</span>                               | Folder | –
<span class="folder">iSyncSyncMLRef</span>                                        | Folder | –
<span class="folder">MessageFrameworkReference</span>                             | Folder | –
<span class="folder">Motion_FXPlug_Ref</span>                                     | Folder | –
<span class="folder">SafariCSSRef</span>                                          | Folder | –
<span class="folder">SafariHTMLRef</span>                                         | Folder | –
<span class="folder">SyncServicesRefUpdate</span>                                 | Folder | –
<span class="folder">SyncServicesSchemaRef</span>                                 | Folder | –
<span class="folder">WebKitDOMRef</span>                                          | Folder | –
<span class="folder">AppleScript</span>                                           | Folder | –
<span class="folder">Reference</span>                                             | Folder | –
<span class="folder">StudioReference</span>                                       | Folder | –
<span class="folder">art</span>                                                   | Folder | –
<span class="file">boxes.gif</span>                                               | File   | 11.52 KB
<span class="file">browser.gif</span>                                             | File   | 26.04 KB
<span class="file">button_in_window.gif</span>                                    | File   | 8.17 KB
<span class="file">cc_app_info_window.gif</span>                                  | File   | 24.36 KB
<span class="file">circular_prog_indicator.gif</span>                             | File   | 0.65 KB
<span class="file">color_panel.jpg</span>                                         | File   | 24.91 KB
<span class="file">color_well.gif</span>                                          | File   | 7.58 KB
<span class="file">combobox.gif</span>                                            | File   | 1.44 KB
<span class="file">comboboxlist.gif</span>                                        | File   | 4.12 KB
<span class="file">display_alert.gif</span>                                       | File   | 28.69 KB
<span class="file">display_dialog.gif</span>                                      | File   | 28.42 KB
<span class="file">doc_exp_groups.gif</span>                                      | File   | 22.52 KB
<span class="file">drawer.gif</span>                                              | File   | 34.4 KB
<span class="file">drawer_content_view.gif</span>                                 | File   | 8.21 KB
<span class="file">drawer_instances_in_nib.gif</span>                             | File   | 20.97 KB
<span class="file">drawers_in_palette.gif</span>                                  | File   | 17.34 KB
<span class="file">files_owner_in_nib.gif</span>                                  | File   | 15.24 KB
<span class="file">font_panel.gif</span>                                          | File   | 17.16 KB
<span class="file">hw_exp_grps_files.gif</span>                                   | File   | 15.35 KB
<span class="file">ib_number_formatter.gif</span>                                 | File   | 1.46 KB
<span class="file">image_tab_mainmenu_nib.gif</span>                              | File   | 12.33 KB
<span class="file">image_view_from_app.gif</span>                                 | File   | 17.18 KB
<span class="file">matrix.gif</span>                                              | File   | 7.36 KB
<span class="file">menu_item.gif</span>                                           | File   | 16.85 KB
<span class="file">menu_showing_file_menu.gif</span>                              | File   | 17 KB
<span class="file">movie_view.gif</span>                                          | File   | 34.76 KB
<span class="file">number_formatter_info.gif</span>                               | File   | 24.8 KB
<span class="file">open_panel.gif</span>                                          | File   | 32.06 KB
<span class="file">outline_view.gif</span>                                        | File   | 18.7 KB
<span class="file">popup_button.gif</span>                                        | File   | 6.76 KB
<span class="file">progindindet.gif</span>                                        | File   | 3.78 KB
<span class="file">save_panel.gif</span>                                          | File   | 39.43 KB
<span class="file">secure_text_field.gif</span>                                   | File   | 9.91 KB
<span class="file">simple_toolbar.gif</span>                                      | File   | 10.4 KB
<span class="file">sliders.gif</span>                                             | File   | 11.01 KB
<span class="file">sounds_in_nib_window.gif</span>                                | File   | 22.26 KB
<span class="file">split_view.gif</span>                                          | File   | 15.73 KB
<span class="file">stepper.gif</span>                                             | File   | 1.08 KB
<span class="file">table_app.gif</span>                                           | File   | 22.72 KB
<span class="file">table_view.gif</span>                                          | File   | 14.55 KB
<span class="file">tabview.gif</span>                                             | File   | 24.86 KB
<span class="file">text_fields.gif</span>                                         | File   | 7.1 KB
<span class="file">text_view.gif</span>                                           | File   | 12.18 KB
<span class="file">to_do_outline.gif</span>                                       | File   | 15.4 KB
<span class="file">window.gif</span>                                              | File   | 6.41 KB
<span class="folder">Index</span>                                                 | Folder | –
<span class="file">index.html</span>                                              | File   | 1.13 KB
<span class="folder">sr10_panel_suite</span>                                      | Folder | –
<span class="folder">sr10_pplugin_suite</span>                                    | Folder | –
<span class="folder">sr11_textview_suite</span>                                   | Folder | –
<span class="folder">sr1_about</span>                                             | Folder | –
<span class="folder">sr2_fundamentals</span>                                      | Folder | –
<span class="folder">sr3_app_suite</span>                                         | Folder | –
<span class="folder">sr4_container_suite</span>                                   | Folder | –
<span class="folder">sr5_control_suite</span>                                     | Folder | –
<span class="folder">sr6_data_suite</span>                                        | Folder | –
<span class="folder">sr7_doc_suite</span>                                         | Folder | –
<span class="folder">sr8_drag_drop_suite</span>                                   | Folder | –
<span class="folder">sr9_menu_suite</span>                                        | Folder | –
<span class="folder">sr_history</span>                                            | Folder | –
<span class="file">toc.html</span>                                                | File   | 132.84 KB
<span class="folder">Carbon</span>                                                | Folder | –
<span class="folder">Cocoa</span>                                                 | Folder | –
<span class="folder">CoreFoundation</span>                                        | Folder | –
<span class="folder">css</span>                                                   | Folder | –
<span class="folder">Darwin</span>                                                | Folder | –
<span class="folder">DeveloperTools</span>                                        | Folder | –
<span class="folder">DeviceDrivers</span>                                         | Folder | –
<span class="folder">Games</span>                                                 | Folder | –
<span class="folder">GraphicsImaging</span>                                       | Folder | –
<span class="folder">Hardware</span>                                              | Folder | –
<span class="folder">HardwareDrivers</span>                                       | Folder | –
<span class="folder">images</span>                                                | Folder | –
<span class="file">index-date.html</span>                                         | File   | 74.06 KB
<span class="file">index-date0.html</span>                                        | File   | 284.1 KB
<span class="file">index-date2.html</span>                                        | File   | 73.94 KB
<span class="file">index-date3.html</span>                                        | File   | 74.4 KB
<span class="file">index-date4.html</span>                                        | File   | 75.11 KB
<span class="file">index-date5.html</span>                                        | File   | 41.11 KB
<span class="file">index-rev-date.html</span>                                     | File   | 49.03 KB
<span class="file">index-rev-revision.html</span>                                 | File   | 49.01 KB
<span class="file">index-rev-title.html</span>                                    | File   | 49.03 KB
<span class="file">index-rev-topic.html</span>                                    | File   | 71.41 KB
<span class="file">index-rev-topic0.html</span>                                   | File   | 93.76 KB
<span class="file">index-rev-topic2.html</span>                                   | File   | 29.61 KB
<span class="file">index-title.html</span>                                        | File   | 73.91 KB
<span class="file">index-title0.html</span>                                       | File   | 284.11 KB
<span class="file">index-title2.html</span>                                       | File   | 74.7 KB
<span class="file">index-title3.html</span>                                       | File   | 73 KB
<span class="file">index-title4.html</span>                                       | File   | 74.15 KB
<span class="file">index-title5.html</span>                                       | File   | 42.91 KB
<span class="file">index-topic.html</span>                                        | File   | 72.46 KB
<span class="file">index-topic0.html</span>                                       | File   | 601.26 KB
<span class="file">index-topic10.html</span>                                      | File   | 74.41 KB
<span class="file">index-topic2.html</span>                                       | File   | 73.6 KB
<span class="file">index-topic3.html</span>                                       | File   | 72.47 KB
<span class="file">index-topic4.html</span>                                       | File   | 71.89 KB
<span class="file">index-topic5.html</span>                                       | File   | 73.89 KB
<span class="file">index-topic6.html</span>                                       | File   | 73.1 KB
<span class="file">index-topic7.html</span>                                       | File   | 70.55 KB
<span class="file">index-topic8.html</span>                                       | File   | 71.25 KB
<span class="file">index-topic9.html</span>                                       | File   | 72.56 KB
<span class="file">index.html</span>                                              | File   | 20.65 KB
<span class="folder">Internationalization</span>                                  | Folder | –
<span class="folder">InternetWeb</span>                                           | Folder | –
<span class="folder">iPhone</span>                                                | Folder | –
<span class="folder">js</span>                                                    | Folder | –
<span class="folder">LegacyTechnologies</span>                                    | Folder | –
<span class="folder">MacOSX</span>                                                | Folder | –
<span class="folder">MacOSXServer</span>                                          | Folder | –
<span class="folder">MusicAudio</span>                                            | Folder | –
<span class="folder">Networking</span>                                            | Folder | –
<span class="folder">OpenSource</span>                                            | Folder | –
<span class="folder">Performance</span>                                           | Folder | –
<span class="folder">Porting</span>                                               | Folder | –
<span class="folder">Printing</span>                                              | Folder | –
<span class="folder">QuickTime</span>                                             | Folder | –
<span class="folder">Resources</span>                                             | Folder | –
<span class="folder">ScriptingAutomation</span>                                   | Folder | –
<span class="folder">Security</span>                                              | Folder | –
<span class="folder">Storage</span>                                               | Folder | –
<span class="folder">TextFonts</span>                                             | Folder | –
<span class="folder">UserExperience</span>                                        | Folder | –
<span class="folder">WebObjects</span>                                            | Folder | –
<span class="folder">referencelibrary</span>                                      | Folder | –
<span class="file">adc.css</span>                                                 | File   | 1.46 KB
<span class="file">base.css</span>                                                | File   | 1.08 KB
<span class="folder">images</span>                                                | Folder | –
<span class="file">body_bg.gif</span>                                             | File   | 0.24 KB
<span class="file">main_bgbottom.gif</span>                                       | File   | 2.35 KB
<span class="file">main_bgtop.gif</span>                                          | File   | 6.88 KB
<span class="file">main_bgtop_stroke.gif</span>                                   | File   | 7.62 KB
<span class="file">UpdateBanner_core.png</span>                                   | File   | 24.25 KB
<span class="file">index.html</span>                                              | File   | 1.15 KB
<span class="file">version.plist</span>                                           | File   | 0.44 KB
<span class="folder">com.apple.ADC_Reference_Library.DeveloperTools.docset</span> | Folder | –
<span class="folder">Contents</span>                                              | Folder | –
<span class="file">Info.plist</span>                                              | File   | 1.33 KB
<span class="folder">Resources</span>                                             | Folder | –
<span class="file">docSet.dsidx</span>                                            | File   | 2752 KB
<span class="file">docSet.skidx</span>                                            | File   | 5664 KB
<span class="folder">Documents</span>                                             | Folder | –
<span class="folder">documentation</span>                                         | Folder | –
<span class="file">adcstyle.css</span>                                            | File   | 15.86 KB
<span class="folder">AppleApplications</span>                                     | Folder | –
<span class="file">AppleApplications.html</span>                                  | File   | 0.22 KB
<span class="folder">Conceptual</span>                                            | Folder | –
<span class="folder">Dashcode_UserGuide</span>                                    | Folder | –
<span class="folder">Contents</span>                                              | Folder | –
<span class="folder">Resources</span>                                             | Folder | –
<span class="folder">de.lproj</span>                                              | Folder | –
<span class="folder">Advanced</span>                                              | Folder | –
<span class="file">chapter_8_section_1.html</span>                                | File   | 6.71 KB
<span class="file">chapter_8_section_2.html</span>                                | File   | 7.93 KB
<span class="file">chapter_8_section_3.html</span>                                | File   | 6.38 KB
<span class="folder">Art</span>                                                   | Folder | –
<span class="file">apple_birthday_widget.jpg</span>                               | File   | 33.5 KB
<span class="file">canvas_inspector.jpg</span>                                    | File   | 71.75 KB
<span class="file">countdown_attributes.jpg</span>                                | File   | 46.69 KB
<span class="file">project_window.jpg</span>                                      | File   | 107.57 KB
<span class="file">source_code_inspector.jpg</span>                               | File   | 76.19 KB
<span class="file">webapp_add_code.jpg</span>                                     | File   | 85.65 KB
<span class="file">webapp_add_part.jpg</span>                                     | File   | 108.16 KB
<span class="file">webapp_first_test.jpg</span>                                   | File   | 86.82 KB
<span class="file">webapp_project_window.jpg</span>                               | File   | 152.27 KB
<span class="file">chapter_999_section_1.html</span>                              | File   | 6.3 KB
<span class="folder">CodeAndDebugging</span>                                      | Folder | –
<span class="file">Dashcode_UserGuide.pdf</span>                                  | File   | 1875.27 KB
<span class="folder">DebuggingSharing</span>                                      | Folder | –
<span class="folder">DesignTools</span>                                           | Folder | –
<span class="file">index.html</span>                                              | File   | 1.11 KB
<span class="folder">Introduction</span>                                          | Folder | –
<span class="folder">MakingaWebApp</span>                                         | Folder | –
<span class="folder">MakingaWidgetwithDashcode</span>                             | Folder | –
<span class="folder">PartsReference</span>                                        | Folder | –
<span class="folder">Templates</span>                                             | Folder | –
<span class="file">toc.html</span>                                                | File   | 38.57 KB
<span class="folder">WidgetProjects</span>                                        | Folder | –
<span class="folder">en.lproj</span>                                              | Folder | –
<span class="folder">Advanced</span>                                              | Folder | –
<span class="file">chapter_8_section_1.html</span>                                | File   | 6.6 KB
<span class="file">chapter_8_section_2.html</span>                                | File   | 7.4 KB
<span class="file">chapter_8_section_3.html</span>                                | File   | 6.24 KB
<span class="folder">Art</span>                                                   | Folder | –
<span class="file">chapter_999_section_1.html</span>                              | File   | 6.2 KB
<span class="folder">CodeAndDebugging</span>                                      | Folder | –
<span class="file">Dashcode_UserGuide.pdf</span>                                  | File   | 1087.36 KB
<span class="folder">DebuggingSharing</span>                                      | Folder | –
<span class="folder">DesignTools</span>                                           | Folder | –
<span class="file">index.html</span>                                              | File   | 1.09 KB
<span class="folder">Introduction</span>                                          | Folder | –
<span class="folder">MakingaWebApp</span>                                         | Folder | –
<span class="folder">MakingaWidgetwithDashcode</span>                             | Folder | –
<span class="folder">PartsReference</span>                                        | Folder | –
<span class="folder">Templates</span>                                             | Folder | –
<span class="file">toc.html</span>                                                | File   | 38.11 KB
<span class="folder">WidgetProjects</span>                                        | Folder | –
<span class="folder">es.lproj</span>                                              | Folder | –
<span class="folder">fr.lproj</span>                                              | Folder | –
<span class="folder">it.lproj</span>                                              | Folder | –
<span class="folder">ja.lproj</span>                                              | Folder | –
<span class="folder">nl.lproj</span>                                              | Folder | –
<span class="folder">zh.lproj</span>                                              | Folder | –
<span class="file">Dashboard-date.html</span>                                     | File   | 10.88 KB
<span class="file">Dashboard-rev-date.html</span>                                 | File   | 8.85 KB
<span class="file">Dashboard-rev-revision.html</span>                             | File   | 8.83 KB
<span class="file">Dashboard-rev-title.html</span>                                | File   | 8.85 KB
<span class="file">Dashboard-title.html</span>                                    | File   | 10.71 KB
<span class="file">index-date.html</span>                                         | File   | 11.79 KB
<span class="file">index-rev-date.html</span>                                     | File   | 9.38 KB
<span class="file">index-rev-revision.html</span>                                 | File   | 9.36 KB
<span class="file">index-rev-title.html</span>                                    | File   | 9.37 KB
<span class="file">index-rev-topic.html</span>                                    | File   | 9.37 KB
<span class="file">index-title.html</span>                                        | File   | 11.78 KB
<span class="file">index-topic.html</span>                                        | File   | 12.39 KB
<span class="file">index.html</span>                                              | File   | 7.24 KB
<span class="file">iSync-date.html</span>                                         | File   | 8.17 KB
<span class="file">iSync-title.html</span>                                        | File   | 8 KB
<span class="folder">Carbon</span>                                                | Folder | –
<span class="file">Carbon.html</span>                                             | File   | 0.21 KB
<span class="file">DesignGuidelines-date.html</span>                              | File   | 9.93 KB
<span class="file">DesignGuidelines-rev-date.html</span>                          | File   | 7.45 KB
<span class="file">DesignGuidelines-rev-revision.html</span>                      | File   | 7.44 KB
<span class="file">DesignGuidelines-rev-title.html</span>                         | File   | 7.45 KB
<span class="file">DesignGuidelines-title.html</span>                             | File   | 9.77 KB
<span class="file">index-date.html</span>                                         | File   | 19.66 KB
<span class="file">index-rev-date.html</span>                                     | File   | 12.35 KB
<span class="file">index-rev-revision.html</span>                                 | File   | 12.33 KB
<span class="file">index-rev-title.html</span>                                    | File   | 12.34 KB
<span class="file">index-rev-topic.html</span>                                    | File   | 12.98 KB
<span class="file">index-title.html</span>                                        | File   | 19.65 KB
<span class="file">index-topic.html</span>                                        | File   | 22.64 KB
<span class="file">index.html</span>                                              | File   | 10.96 KB
<span class="file">IntelBasedMacs-date.html</span>                                | File   | 10.5 KB
<span class="file">IntelBasedMacs-title.html</span>                               | File   | 10.33 KB
<span class="file">Performance-date.html</span>                                   | File   | 9.14 KB
<span class="file">Performance-title.html</span>                                  | File   | 8.98 KB
<span class="file">Porting-date.html</span>                                       | File   | 8.78 KB
<span class="file">Porting-title.html</span>                                      | File   | 8.63 KB
| undefined
td align="left">

<span class="file">Tools-date.html</span>

File

16.03 KB



<span class="file">Tools-rev-date.html</span>

File

10.85 KB



<span class="file">Tools-rev-revision.html</span>

File

10.83 KB



<span class="file">Tools-rev-title.html</span>

File

10.84 KB



<span class="file">Tools-title.html</span>

File

15.88 KB



<span class="file">UserExperience-date.html</span>

File

8.85 KB



<span class="file">UserExperience-title.html</span>

File

8.69 KB



<span class="folder">Cocoa</span>

Folder

–



<span class="folder">CoreFoundation</span>

Folder

–



<span class="folder">css</span>

Folder

–



<span class="folder">Darwin</span>

Folder

–



<span class="folder">DeveloperTools</span>

Folder

–



<span class="folder">GraphicsImaging</span>

Folder

–



<span class="folder">HardwareDrivers</span>

Folder

–



<span class="folder">images</span>

Folder

–



<span class="file">index-date.html</span>

File

38.14 KB



<span class="file">index-rev-date.html</span>

File

20.91 KB



<span class="file">index-rev-revision.html</span>

File

20.89 KB



<span class="file">index-rev-title.html</span>

File

20.9 KB



<span class="file">index-rev-topic.html</span>

File

45.06 KB



<span class="file">index-title.html</span>

File

38.14 KB



<span class="file">index-topic.html</span>

File

77.78 KB



<span class="file">index.html</span>

File

17.08 KB



<span class="folder">Internationalization</span>

Folder

–



<span class="folder">InternetWeb</span>

Folder

–



<span class="folder">Java</span>

Folder

–



<span class="folder">js</span>

Folder

–



<span class="folder">LegacyTechnologies</span>

Folder

–



<span class="folder">MacOSX</span>

Folder

–



<span class="folder">OpenSource</span>

Folder

–



<span class="folder">Performance</span>

Folder

–



<span class="folder">Porting</span>

Folder

–



<span class="folder">Resources</span>

Folder

–



<span class="folder">ScriptingAutomation</span>

Folder

–



<span class="folder">UserExperience</span>

Folder

–



<span class="folder">Xcode</span>

Folder

–



<span class="folder">featuredarticles</span>

Folder

–



<span class="file">adcstyle.css</span>

File

15.86 KB



<span class="folder">AppleApplications</span>

Folder

–



<span class="file">idxDashboard-date.html</span>

File

8.35 KB



<span class="file">idxDashboard-title.html</span>

File

8.24 KB



<span class="file">index-date.html</span>

File

8.52 KB



<span class="file">index-title.html</span>

File

8.51 KB



<span class="file">index-topic.html</span>

File

8.51 KB



<span class="file">index.html</span>

File

6.46 KB



<span class="folder">Carbon</span>

Folder

–



<span class="folder">Cocoa</span>

Folder

–



<span class="folder">css</span>

Folder

–



<span class="folder">DeveloperTools</span>

Folder

–



<span class="folder">Games</span>

Folder

–



<span class="folder">images</span>

Folder

–



<span class="file">index-date.html</span>

File

16.03 KB



<span class="file">index-title.html</span>

File

16.03 KB



<span class="file">index-topic.html</span>

File

19.32 KB



<span class="file">index.html</span>

File

10.98 KB



<span class="folder">js</span>

Folder

–



<span class="folder">LegacyTechnologies</span>

Folder

–



<span class="folder">ScriptingAutomation</span>

Folder

–



<span class="folder">UserExperience</span>

Folder

–



<span class="file">index.html</span>

File

0.23 KB



<span class="folder">qa</span>

Folder

–



<span class="folder">reference</span>

Folder

–



<span class="folder">referencelibrary</span>

Folder

–



<span class="folder">releasenotes</span>

Folder

–



<span class="folder">samplecode</span>

Folder

–



<span class="folder">technicalnotes</span>

Folder

–



<span class="folder">technicalqas</span>

Folder

–



<span class="folder">technotes</span>

Folder

–



<span class="file">version.plist</span>

File

0.44 KB



<span class="file">iPhone SDK License.rtf</span>

File

37.93 KB



<span class="folder">Perl</span>

Folder

–



<span class="folder">wxPerl</span>

Folder

–



<span class="file">INSTALL.pod</span>

File

8.26 KB



<span class="file">todo.txt</span>

File

2.3 KB



<span class="folder">Python</span>

Folder

–



<span class="folder">PyObjC</span>

Folder

–



<span class="file">announcement.txt</span>

File

2.33 KB



<span class="file">api-notes-macosx.html</span>

File

30.09 KB



<span class="file">api-notes-macosx.txt</span>

File

18.37 KB



<span class="file">C-API.html</span>

File

11 KB



<span class="file">C-API.txt</span>

File

8.67 KB



<span class="file">coding-style.html</span>

File

4.53 KB



<span class="file">coding-style.txt</span>

File

2.92 KB



<span class="file">gnustep.html</span>

File

1.96 KB



<span class="file">gnustep.txt</span>

File

1.52 KB



<span class="file">index.html</span>

File

2.75 KB



<span class="file">index.txt</span>

File

2.64 KB



<span class="file">intro.html</span>

File

44.82 KB



<span class="file">intro.txt</span>

File

38.38 KB



<span class="file">protocols.html</span>

File

3.48 KB



<span class="file">protocols.txt</span>

File

2.79 KB



<span class="file">PyObjCTools.html</span>

File

10.96 KB



<span class="file">PyObjCTools.txt</span>

File

7.9 KB



<span class="folder">Quartz</span>

Folder

–



<span class="file">api-notes.txt</span>

File

1.47 KB



<span class="file">release-process.html</span>

File

3.83 KB



<span class="file">release-process.txt</span>

File

2.65 KB



<span class="file">structure.html</span>

File

6.55 KB



<span class="file">structure.txt</span>

File

5.1 KB



<span class="file">TODO.html</span>

File

13.49 KB



<span class="file">TODO.txt</span>

File

9.06 KB



<span class="folder">tutorial</span>

Folder

–



<span class="folder">tutorial_embed</span>

Folder

–



<span class="file">tutorial_reading.html</span>

File

12.85 KB



<span class="file">tutorial_reading.txt</span>

File

11.12 KB



<span class="file">website.lst</span>

File

0.58 KB



<span class="file">wrapping.html</span>

File

6.04 KB



<span class="file">wrapping.txt</span>

File

5.2 KB



<span class="folder">xcode</span>

Folder

–



<span class="file">Xcode-Templates.html</span>

File

13.97 KB



<span class="folder">wxPython</span>

Folder

–



<span class="folder">RubyCocoa</span>

Folder

–



<span class="folder">wxWidgets</span>

Folder

–



<span class="file">Xcode Tools License.rtf</span>

File

18.79 KB

```
$("#example-advanced").treetable({ expandable: true });

// Highlight selected row
$("#example-advanced tbody").on("mousedown", "tr", function() {
 $(".selected").not(this).removeClass("selected");
 $(this).toggleClass("selected");
});

// Drag & Drop Example Code
$("#example-advanced .file, #example-advanced .folder").draggable({
 helper: "clone",
 opacity: .75,
 refreshPositions: true,
 revert: "invalid",
 revertDuration: 300,
 scroll: true
});

$("#example-advanced .folder").each(function() {
 $(this).parents("#example-advanced tr").droppable({
 accept: ".file, .folder",
 drop: function(e, ui) {
 var droppedEl = ui.draggable.parents("tr");
 $("#example-advanced").treetable("move", droppedEl.data("ttId"), $(this).data("ttId"));
 },
 hoverClass: "accept",
 over: function(e, ui) {
 var droppedEl = ui.draggable.parents("tr");
 if(this != droppedEl[0] && !$(this).is(".expanded")) {
 $("#example-advanced").treetable("expandNode", $(this).data("ttId"));
 }
 }
 });
});
```

# AJAX enabled D&D example

See <https://github.com/ludo/jquery-treetable-ajax-example>.

# Using treetable with PersistJS

See <https://github.com/jughead/jquery-treetable-ajax-persist>.
