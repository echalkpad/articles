# Performance Co-Pilot

[Original URL](http://pcp.io/docs/guide.html)

> PCP Quick Reference Guide Introduction Performance Co-Pilot (PCP) is an open source framework and toolkit for monitoring, analyzing, and responding to details of live and historical system...



## PCP Quick Reference Guide

[]()

## Introduction

[Performance Co-Pilot](http://www.pcp.io/) (PCP) is an open source framework and toolkit for monitoring, analyzing, and responding to details of live and historical system performance. PCP has a fully distributed, plug-in based architecture making it particularly well suited to centralized analysis of complex environments and systems. Custom performance metrics can be added using the C, C++, Perl, and Python interfaces.

This page provides quick instructions how to install and use PCP on a set of hosts of which one (a monitor host) will be used for monitoring and analyzing itself and other hosts (collector hosts). []()

## Installation

PCP is available on all recent distribution releases, include Debian/Fedora/RHEL/Ubuntu. For earlier releases and other distributions you might want to consider installation [from sources](http://www.pcp.io/source.html) or checking auxiliary package repositories, like [EPEL](http://fedoraproject.org/wiki/EPEL) . []()

### Installing Collector Hosts

| <br>
To install basic PCP tools and services and enable collecting performance data on Fedora/RHEL, run:<br>
**<br>
# yum install pcp<br>
# chkconfig pmcd on<br>
# service pmcd start<br>
# chkconfig pmlogger on<br>
# service pmlogger start**
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| <br>
To install basic PCP tools and services and enable collecting performance data on Debian/Ubuntu, run:<br>
**<br>
$ sudo apt-get install pcp<br>
$ sudo update-rc.d pmcd defaults<br>
$ sudo update-rc.d pmlogger defaults<br>
$ sudo service pmcd restart<br>
$ sudo service pmlogger restart**

This will enable the Performance Metrics Collector Daemon ( [pmcd(1)](http://pcp.io/man/man1/pmcd.1.html) ) on the host which then in turn will control and request metrics on behalf of clients from various Performance Metrics Domain Agents (PMDAs). The PMDAs provide the actual data from different components (domains) in the system, for example from the Linux Kernel PMDA or the NFS Client PMDA. The default configuration includes over 1000 metrics with negligible overall overhead. Local PCP archive logs will also be enabled on the host for convenience with [pmlogger(1)](http://pcp.io/man/man1/pmlogger.1.html) .

| <br>
To enable PMDAs which are not enabled by default, for example the Postfix PMDA, run the corresponding Install script:<br>
**<br>
# cd /var/lib/pcp/pmdas/postfix<br>
# ./Install**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The client tools will contact local or remote PMCDs as needed, communication with PMCD over the network uses TCP port 44321 by default. []()

### Installing Monitor Host

The following additional packages can be optionally installed on the monitoring host to extend the set of monitoring tools from the base pcp package.

| <br>
Install graphical analysis tools and documentation on Fedora/RHEL:<br>
**<br>
# yum install pcp-doc pcp-gui**
| -----------------------------------------------------------------------------------------------------------------------------
| <br>
Install graphical analysis tools and documentation on Debian/Ubuntu:<br>
**<br>
$ sudo apt-get install pcp-doc pcp-gui**

To enable centralized archive log collection on the monitoring host, its pmlogger is configured to fetch performance metrics from collector hosts. Add each collector host to the pmlogger configuration file `/etc/pcp/pmlogger/control` and then restart the pmlogger service on the monitoring host.

| <br>
Enable recording of metrics from remote host **acme.com** : **<br><br>
# echo acme.com n n PCP_LOG_DIR/pmlogger/acme.com -r -T24h10m -c config.acme.com >> /etc/pcp/pmlogger/control<br><br>
# service pmlogger restart**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Checks for remote log collection will be done every half an hour. You may also wish to run /usr/libexec/pcp/bin/pmlogger_check -V -C (on Fedora/RHEL) or /usr/lib/pcp/bin/pmlogger_check -V -C (on Debian/Ubuntu) manually (service restart above issues this command internally).

Note that a default configuration file (config.acme.com above) will be generated if it does not exist already. This process is optional (a custom configuration for each host can be provided instead), see the [pmlogconf(1) manual page](http://pcp.io/man/man1/pmlogconf.1.html) for details on this. []()

### Dynamic Host Discovery

In dynamic environments manually configuring every host is not feasible, perhaps even impossible. PCP Manager ( [pmmgr(1)](http://pcp.io/man/man1/pmmgr.1.html) , from the pcp-manager package) can be used instead of directly invoking pmlogger and pmie to auto-discover and auto-configure new collector hosts.

| <br>
To install the PMMGR daemon and begin monitoring either statically or dynamically configured hosts, run:<br>
**<br>
## Fedora/RHEL:<br>
# yum install pcp-manager<br>
# chkconfig pmmgr on<br>
## Debian/Ubuntu:<br>
$ sudo apt-get install pcp-manager<br>
$ sudo update-rc.d pmmgr defaults<br>
# Common:<br>
# echo acme.com >> /etc/pcp/pmmgr/target-host<br>
# echo avahi >> /etc/pcp/pmmgr/target-discovery<br>
# echo probe= ip.addr.tup.le/netmask >> /etc/pcp/pmmgr/target-discovery<br>
# service pmmgr restart<br>
# find /var/log/pcp/pmmgr**
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Discover use of the PCP pmcd service on the local network:<br>
**<br>
$ [pmfind](http://pcp.io/man/man1/pmfind.1.html) -s pmcd**
| -------------------------------------------------------------------------------------------------------------------------------------

[]()

### Installation Health Check

Basic installation health check for running services, network connectivity between hosts, and enabled PMDAs can be done simply as follows.

| <br>
Check PCP services on remote host **munch** and historically, from a local archive for host **smash** :<br>
**<br>
$ [pcp](http://pcp.io/man/man1/pcp.1.html) -h munch**

```
Performance Co-Pilot configuration on munch:
 platform: SunOS munch 5.11 oi_151a8 i86pc
 hardware: 4 cpus, 3 disks, 4087MB RAM
 timezone: EST-10
 services: pmcd pmproxy
 pmcd: Version 3.8.9-1, 3 agents
 pmda: pmcd mmv solaris
 pmie: /var/log/pcp/pmie/munch/pmie.log```

**<br>
$ [pcp](http://pcp.io/man/man1/pcp.1.html) -a /var/log/pcp/pmlogger/ smash /20140729**

```
Performance Co-Pilot configuration on smash:
 archive: /var/log/pcp/pmlogger/smash/20140729
 platform: Linux smash 2.6.32-279.46.1.el6.x86_64 #1 SMP Mon May 19 16:16:00 EDT 2014 x86_64
 hardware: 8 cpus, 2 disks, 1 node, 23960MB RAM
 timezone: EST-10
 services: pmcd pmproxy pmwebd
 pmcd: Version 3.9.8-1, 8 agents
 pmda: pmcd proc xfs linux mmv nvidia dmcache postgresql
 pmlogger: primary logger: /var/log/pcp/pmlogger/smash/20140729.00.10
 pmie: /var/log/pcp/pmie/smash/pmie.log```
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

## System Level Performance Monitoring

PCP comes with a wide range of command line utilities for accessing live performance metrics via PMCDs or historical data using archive logs. The following examples illustrate some of the most useful use cases, please see the corresponding manual pages for each command for additional information. In the examples below **-h <host>** is always optional, the default is the local host. []()

### Monitoring Live Performance Metrics

| <br>
Display all the enabled performance metrics on a host (use with -t to include a short description for each):<br>
**<br>
$ [pminfo](http://pcp.io/man/man1/pminfo.1.html) -h acme.com**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Display detailed information about a performance metric and its current values:<br>
**<br>
$ [pminfo](http://pcp.io/man/man1/pminfo.1.html) -dfmtT disk.partitions.read -h acme.com**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor live disk write operations per partition with two second interval using fixed point notation (use _-i_ instance to list only certain metrics and _-r_ for raw values):<br>
**<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -t 2sec -f 3 disk.partitions.write -h acme.com**
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor live CPU load, memory usage, and disk write operations per partition with two second interval using fixed width columns:<br>
**<br>
$ [pmdumptext](http://pcp.io/man/man1/pmdumptext.1.html) -Xlimu -t 2sec 'kernel.all.load[1]' mem.util.used disk.partitions.write -h acme.com**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor live process creation rate and free/used memory with two second interval printing timestamps and using GBs for output values in CSV format:<br>
**<br>
$ [pmrep](http://pcp.io/man/man1/pmrep.1.html) -h acme.com -p -b GB -t 2sec -o csv kernel.all.sysfork mem.util.free mem.util.used**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor system metrics in a top-like window:<br>
**<br>
$ [pcp atop](http://pcp.io/man/man1/pcp-atop.1.html)**
| -------------------------------------------------------------------------------------------------------------------

| <br>
Monitor system metrics in a sar-like (System Activity Report) manner:<br>
**<br>
$ [pcp atopsar](http://pcp.io/man/man1/pcp-atopsar.1.html)**
| --------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor system metrics in a sar like fashion with two second interval from two different hosts:<br>
**<br>
$ [pmstat](http://pcp.io/man/man1/pmstat.1.html) -t 2sec -h acme1.com -h acme2.com**
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor system metrics in an iostat like fashion with two second interval:<br>
**<br>
$ [pmiostat](http://pcp.io/man/man1/pmiostat.1.html) -t 2sec -h acme.com**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor performance metrics with a GUI application with two second default interval from two different hosts. Use _File->New Chart_ to select metrics to be included in a new view and use _File->Open View_ to use a predefined view:<br>
**<br>
$ [pmchart](http://pcp.io/man/man1/pmchart.1.html) -t 2sec -h acme1.com -h acme2.com**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

### Retrospective Performance Analysis

PCP archive logs are located under `/var/log/pcp/pmlogger/` **hostname** , and the archive names indicate the date they cover. Archives are self-contained, and machine-independent so can be transfered to any machine for offline analysis.

| <br>
Check the host and the time period an archive covers:<br>
**<br>
$ [pmdumplog](http://pcp.io/man/man1/pmdumplog.1.html) -l acme.com/20140902**
| ---------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Check PCP configuration at the time when an archive was created:<br>
**<br>
$ [pcp](http://pcp.io/man/man1/pcp.1.html) -a acme.com/20140902**
| --------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Display all enabled performance metrics at the time when an archive was created:<br>
**<br>
$ [pminfo](http://pcp.io/man/man1/pminfo.1.html) -a acme.com/20140902**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Display detailed information about a performance metric at the time when an archive was created:<br>
**<br>
$ [pminfo](http://pcp.io/man/man1/pminfo.1.html) -df mem.freemem -a acme.com/20140902**
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Dump past disk write operations per partition in an archive using fixed point notation (use _-i_ instance to list only certain metrics and _-r_ for raw values):<br>
**<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -f 3 disk.partitions.write -a acme.com/20140902**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Replay past disk write operations per partition in an archive with two second interval using fixed point notation between 9 AM and 10 AM (use full dates with syntax like _@"2014-08-20 14:00:00"_ ):<br>
**<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -d -t 2sec -f 3 disk.partitions.write -S @09:00 -T @10:00 -a acme.com/20140902**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Calculate average values of performance metrics in an archive between 9 AM / 10 AM using table like formatting including the time of minimum/maximum value and the actual minimum/maximum value:<br>
**<br>
$ [pmlogsummary](http://pcp.io/man/man1/pmlogsummary.1.html) -HlfiImM -S @09:00 -T @10:00 acme.com/20140902 disk.partitions.write mem.freemem**
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Dump past CPU load, memory usage, and disk write operations per partition in an archive averaged over 10 minute interval with fixed columns between 9 AM and 10 AM:<br>
**<br>
$ [pmdumptext](http://pcp.io/man/man1/pmdumptext.1.html) -Xlimu -t 10m -S @09:00 -T @10:00 'kernel.all.load[1]' 'mem.util.used' 'disk.partitions.write' -a acme.com/20140902**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Dump past CPU load, memory usage, and disk write operations per partition in an archive with extended header using MBs but without interpolation between 9 AM and 10 AM:<br>
**<br>
$ [pmrep](http://pcp.io/man/man1/pmrep.1.html) -a acme.com/20140902 -p -u -b MB -x -S @09:00 -T @10:00 kernel.all.load mem.util.used disk.partitions.write**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Summarize differences in past performance metrics between two archives, comparing 2 AM / 3 AM in the first archive to 9 AM / 10 AM in the second archive (grep for _'+'_ to quickly see values which were zero during the first period):<br>
**<br>
$ [pmdiff](http://pcp.io/man/man1/pmdiff.1.html) -S @02:00 -T @03:00 -B @09:00 -E @10:00 acme.com/20140902 acme.com/20140901**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Replay past system metrics in an archive in a top-like window starting 9 AM:<br>
**<br>
$ [pcp atop](http://pcp.io/man/man1/pcp-atop.1.html) -b 09:00 -r acme.com/20140902<br>
$ [pcp](http://pcp.io/man/man1/pcp-atop.1.html) -S @09:00 -a acme.com/20140902 atop**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Dump past system metrics in a sar like fashion averaged over 10 minute interval in an archive between 9 AM and 10 AM:<br>
**<br>
$ [pmstat](http://pcp.io/man/man1/pmstat.1.html) -t 10m -S @09:00 -T @10:00 -a acme.com/20140902**
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Dump past system metrics in an [iostat(1)](http://man7.org/linux/man-pages/man1/iostat.1.html) like fashion averaged over one hour interval in an archive:<br>
**<br>
$ [pmiostat](http://pcp.io/man/man1/pmiostat.1.html) -t 1h -a acme.com/20140902**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Dump past system metrics in a [free(1)](http://man7.org/linux/man-pages/man1/free.1.html) like fashion at a specific historical time offset:<br>
**<br>
$ [pcp](http://pcp.io/man/man1/pcp.1.html) -a acme.com/20140902 -O @10:02 free**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Replay performance metrics with a GUI application with two second default interval in an archive between 9 AM and 10 AM. Use _File->New Chart_ to select metrics to be included in a new view and use _File->Open View_ to use a predefined view:<br>
**<br>
$ [pmchart](http://pcp.io/man/man1/pmchart.1.html) -t 2sec -S @09:00 -T @10:00 -a acme.com/20140902**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Merge several archives as a new combined archive (see the manual page how to write configuration file to collect only certain metrics):<br>
**<br>
$ [pmlogextract](http://pcp.io/man/man1/pmlogextract.1.html) <archive1> <archive2> <newarchive>**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

### Visualizing iostat and sar Data

[iostat](http://man7.org/linux/man-pages/man1/iostat.1.html) and [sar](http://man7.org/linux/man-pages/man1/sar.1.html) data can be imported as PCP archives which then allows inspecting and visualizing the data with PCP tools. The [iostat2pcp(1)](http://pcp.io/man/man1/iostat2pcp.1.html) importer is in the _pcp-import-iostat2pcp_ package and the [sar2pcp(1)](http://pcp.io/man/man1/sar2pcp.1.html) importer is in the _pcp-import-sar2pcp_ package.

| <br>
Import iostat data to a new PCP archive and visualize it:<br>
**<br>
$ [iostat](http://man7.org/linux/man-pages/man1/iostat.1.html) -t -x 2 > iostat.out<br>
$ [iostat2pcp](http://pcp.io/man/man1/iostat2pcp.1.html) iostat.out iostat.pcp<br>
$ [pmchart](http://pcp.io/man/man1/pmchart.1.html) -t 2sec -a iostat.pcp**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Import sar data from an existing sar archive to a new PCP archive and visualize it (sar logs are under `/var/log/sysstat` on Debian/Ubuntu):<br>
**<br>
$ [sar2pcp](http://pcp.io/man/man1/sar2pcp.1.html) /var/log/sa/sa15 sar.pcp<br>
$ [pmchart](http://pcp.io/man/man1/pmchart.1.html) -t 2sec -a sar.pcp**
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

## Process Level Performance Monitoring

PCP provides details of each running process via the standard PCP interfaces and tools on the localhost but due to security and performance considerations, most of the process related information is not stored in archive logs by default. Also for security reasons, only root can access some details of running processes of other users.

Custom application instrumentation is possible with the Memory Mapped Value (MMV) PMDA. []()

### Live and Retrospective Process Monitoring

| <br>
Display all the available process related metrics:<br>
**<br>
$ [pminfo](http://pcp.io/man/man1/pminfo.1.html) proc**
| --------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor the number of open file descriptors of the process 1234:<br>
**<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -t 2sec 'proc.fd.count[1234]'**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Monitor the CPU time, memory usage (RSS), and the number of threads of the process 1234 ( _-h local:_ is a workaround needed for the time being):<br>
**<br>
$ [pmdumptext](http://pcp.io/man/man1/pmdumptext.1.html) -Xlimu -t 2sec 'proc.psinfo.utime[1234]' 'proc.memory.rss[1234]' 'proc.psinfo.threads[1234]' -h local:**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Display all the available process related metrics in an archive:<br>
**<br>
$ [pminfo](http://pcp.io/man/man1/pminfo.1.html) proc -a acme.com/20140902**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Display the number of running processes on 2014-08-20 14:00:<br>
**<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -s 1 -S @"2014-08-20 14:00" proc.nprocs -a acme.com/20140820**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

### Monitoring "Hot" Processes with Hotproc

It is also possible to monitor "hot" or "interesting" processes by name with PCP 3.10.5 or later, for example all processes of which command name is `java` or `python` . This monitoring of "hot" processes can also be enabled or disabled based on certain criterias or from the command line on the fly. The metrics will be available under the namespace _hotproc_ .

Configuring processes to be monitored contantly using the _hotproc_ namespace can be done using the configuration file `/var/lib/pcp/pmdas/proc/hotproc.conf` - see the [pmdaproc(1)](http://pcp.io/man/man1/pmdaproc.1.html) manual page for details. This allows monitoring these processes regardless of their PIDs and also logging the metrics easily.

| <br>
Enable monitoring of all Java instances on the fly and display all the collected metrics:<br>
**<br>
# [pmstore](http://pcp.io/man/man1/pmstore.1.html) hotproc.control.config 'fname == "java"'<br>
# [pminfo](http://pcp.io/man/man1/pminfo.1.html) -f hotproc**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

### Application Instrumentation

Applications can be instrumented in the PCP world by using Memory Mapped Values (MMVs). [pmdammv](http://pcp.io/man/man1/pmdammv.1.html) is a PMDA which exports application level performance metrics using memory mapped files. It offers an extremely low overhead instrumentation facility that is well-suited to long running, mission critical applications where it is desirable to have performance metrics and availability information permanently enabled.

Application to be instrumented with MMV need to be PCP MMV aware, APIs are available for several languages including C, C++, Perl, and Python. Java applications may use the separate [Parfait](https://code.google.com/p/parfait/) class library for enabling MMV.

Instrumentation of unaltered Java applications is a known feature request and is planned for a not-too-distant release.

See the [Performance Co-Pilot Programmer's Guide PDF](http://www.pcp.io/doc/pcp-programmers-guide.pdf) for more information about application instrumentation. []()

## Derived Metrics

PCP provides a wide range of performance metrics but still in some cases the readily available metrics may not exactly provide what is needed. Derived metrics (see [pmLoadDerivedConfig(3)](http://pcp.io/man/man3/pmloadderivedconfig.3.html) ) may be used to extend the available metrics with new (derived) metrics by using simple arithmetic expressions (see [pmRegisterDerived(3)](http://pcp.io/man/man3/pmregisterderived.3.html) ).

The following example illustrates how to define corresponding metrics which are displayed by sar -d but are not provided by default by PCP:

| <br>
Create a file containing definitions of derived metrics and point PCP_DERIVED_CONFIG to it when running PCP utilities: **<br>
$ cat ./pcp-deriv-metrics.conf<br>
disk.dev.avqsz = disk.dev.read_rawactive + disk.dev.write_rawactive<br>
disk.dev.avrqsz = 2 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md 30_fantastic_online_business_apps**_**«_webappstorm.md a_beginners_guide_to_lvm.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md anders_breivik's_inexplicable_crime.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md an_introduction_to_pythons_flask_framework**tuts_code_tutorial.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul**scotts_weblog**the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion**the_analytical_approach.md best_practices_when_working_with_javascript_templates**tuts_code_tutorial.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals**introduction.md concise_electronics_for_geeks.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs**tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it's_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md developing_google_chrome_extensions.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md dremel_made_simple_with_parquet.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back**simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash's_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md harvey_an_operating_system_with_plan_9s_shadow_--_this_is_not_a_monad_tutorial.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md hooking_up_zapping_your_brain.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_access_ssh_terminal_in_web_browser_on_linux.md how_to_be_polite_--_the_message.md how_to_diff_and_merge_files_or_directories_on_linux.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_look_up_the_geographic_location_of_an_ip_address_from_the_command_line.md how_to_search_text_files_for_patterns_efficiently.md how_to_secure_a_mail_server_using_encryption.md how_to_set_up_webbased_network_traffic_monitoring_system_on_linux.md how_to_spoof_the_mac_address_of_a_network_interface_on_linux.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don't_hire_0x_engineers.md lars_and_the_real_girl_guy_and_doll_and_the_woman_behind_them.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine**make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain**helen_keller's_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md methods_of_proof_--_diagonalization.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming project_spartan_forensics.md receiving_weather_satellite_images_for_£8.md robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md testing_javascript_with_phantomjs**tuts_code_tutorial.md tfidf_is_about_what_matters.md the_beginners_guide_to_unit_testing_what_is_unit_testing**tuts_code_article.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company's_secrets.md the_fundamentals_of_bash_scripting.md the_hitchhikers_guide_to_modern_javascript_tooling.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv wave_interference_and_beat_frequency.md weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md rate(disk.dev.total_bytes) / rate(disk.dev.total)<br>
disk.dev.await = 1000 10_years_of_git_an_interview_with_git_creator_linus_torvalds.md 30_fantastic_online_business_apps_«_webappstorm.md a_beginners_guide_to_lvm.md accuracy_in_the_palm_of_your_hand.md a_clear_view_of_the_brain.md a_liberator_but_never_free.md a_look_at_alteras_opencl_sdk_for_fpgas.md anders_breivik's_inexplicable_crime.md an_important_step_in_artificial_intelligence.md an_introduction_to_matlab_metaprogramming.md an_introduction_to_pythons_flask_framework**tuts_code_tutorial.md announcing_the_nulecule_specification_for_composite_applications.md a_quick_introduction_to_consul**scotts_weblog**the_weblog_of_an_it_pro_specializing_in_virtualization_networking_open_source_and_cloud_computing.md a_thorough_introduction_guide_to_docker_containers.md a_tmux_crash_course.md bayesian_inference_of_a_binomial_proportion**the_analytical_approach.md best_practices_when_working_with_javascript_templates**tuts_code_tutorial.md browser_monitoring_for_githubcom.md can_ecstasy_replace_xanax.md circles_sines_and_signals**introduction.md concise_electronics_for_geeks.md creating_kvm_machines_with_boxgrinder_and_vmbuilder.md creating_shazam_in_java.md crossplatform_development_with_nwjs**tuts_code_tutorial.md data_archeology.md data.json data_ml david_deutsch_explains_why_it's_good_to_be_wrong.md deku_how_we_built_our_functional_alternative_to_react.md designing_a_state_machine_without_conditionals.md developing_google_chrome_extensions.md devops discovering_and_monitoring_hardware_in_linux.md docker does_long_term_use_of_psychiatric_drugs_cause_more_harm_than_good.md dremel_made_simple_with_parquet.md drug_perks_up_old_muscles_and_aging_brains.md electronics_for_the_javascript_developer_using_wifi_and_bluetooth_le.md elements_of_scale_composing_and_scaling_data_platforms.md elements_of_scale_composing_and_scaling_data platforms.md embedded eve_online_how_a_virtual_world_went_to_the_edge_of_apocalypse_and_back**simon_parkin.md explaining_a_cornerstone_of_game_theory_john_nash's_equilibrium.md five_funny_little_linux_network_testers_and_monitors.md fixing_engineerings_loyalty_and_longevity_problem.md gaming github go golden_gate_bridge_builders_ate_special_meals_to_prevent_dizziness.md google_systems_guru_explains_why_containers_are_the_future_of_computing.md hackers_build_a_new_tor_client_designed_to_beat_the_nsa.md harvey_an_operating_system_with_plan_9s_shadow_--_this_is_not_a_monad_tutorial.md highavailability_storage_with_halvm.md historyfuture_proof_fingerprint.md hooking_up_zapping_your_brain.md how_i_got_converted_to_gmo_food.md how_shazam_works.md how_to_access_ssh_terminal_in_web_browser_on_linux.md how_to_be_polite_--_the_message.md how_to_diff_and_merge_files_or_directories_on_linux.md how_to_get_your_very_own_rstudio_server_and_shiny_server_with_digitalocean.md how_to_look_up_the_geographic_location_of_an_ip_address_from_the_command_line.md how_to_search_text_files_for_patterns_efficiently.md how_to_secure_a_mail_server_using_encryption.md how_to_set_up_webbased_network_traffic_monitoring_system_on_linux.md how_to_spoof_the_mac_address_of_a_network_interface_on_linux.md how_to_turn_your_pc_or_other_device_into_a_retro_arcade_with_lakka.md html implementing_a_trustless_security_solution_with_hardware_wallets_and_multisignature.md inside_the_mind_that_built_google_brain_on_life_creativity_and_failure.md ira_glass_public_radio_can_capitalize_on_its_popularity_without_selling_out_its_mission.md itspritecom.md javascript join_the_engineering_leisure_class.md just_don't_hire_0x_engineers.md lars_and_the_real_girl_guy_and_doll_and_the_woman_behind_them.md linux linux_commandline_video_editing.md make.md make_your_own_injection_molding_machine**make_diy_projects_howtos_electronics_crafts_and_ideas_for_makers.md mark_twain**helen_keller's_special_friendship_he_treated_me_not_as_a_freak_but_as_a_person_dealing_with_great_difficulties.md mbuntu_y_macbuntu_transformation_pack_available_for_ubuntu_1504_vivid_vervet.md meeting_a_silk_road_drugs_boss.md methods_of_proof_--_diagonalization.md mobile no_death_no_taxes.md null.md old on_edgar_allan_poe_by_marilynne_robinson.md optimize_python_with closures.md phd_is_the_doctoral_thesis_obsolete.md pisoc_learn_to_create.md png polymatheia__persistent_vector_performance_summarised.md popular_electric_brain_stimulation_method_tdcs_is_detrimental_to_iq_scores_study.md programming project_spartan_forensics.md receiving_weather_satellite_images_for_£8.md robust_landmarkbased_audio_fingerprinting.md roll_your_own_customized_ubuntu_with_uck.md running_a_small_docker_swarm_cluster.md rust_discovery_or_how_i_figure_things_out.md safe_connection.md scantool__obdii_car_diagnostic_software_for_linux.md science smart_regulation_for_smart_drugs.md social software_install_guide.md stigler_diet_–_how_i_became_a_data_scientist_despite_having_been_a_math_major.md testing_javascript_with_phantomjs**tuts_code_tutorial.md tfidf_is_about_what_matters.md the_beginners_guide_to_unit_testing_what_is_unit_testing**tuts_code_article.md the_business_economics_and_opportunity_of_opensource_data_science.md the_entrepreneur_disrupting_the_tech_world_by_spilling_her_company's_secrets.md the_fundamentals_of_bash_scripting.md the_hitchhikers_guide_to_modern_javascript_tooling.md the_identity_underground.md the_man_who_became_big bird.md the_most_common_illegal_job_interview_questions_you_should_watch_out_for.md theory.md the_psychology_of_ux.md the_roland_tr808_the_drum_machine_that_revolutionised_music.md the_shazam_effect.md the_sound_of_ted_a_case_for_distaste.md the_unreasonable_effectiveness_of_recurrent_neural_networks.md tidy understanding_c_by_learning_assembly.md url_to_filename.csv wave_interference_and_beat_frequency.md weave_is_kinda_slow.md web_dev what_richard_branson_and_30_amazing_entrepreneurs_taught_me_in_7_days_on_necker_island.md who_funds_the_future.md why_we_age_–_part_i_the_evolution_of_aging.md wireless_raspberry_pi_speaker.md (rate(disk.dev.read_rawactive) + rate(disk.dev.write_rawactive)) / rate(disk.dev.total)<br>
$ export PCP_DERIVED_CONFIG=./pcp-deriv-metrics.conf<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -t 2sec -f 3 disk.dev.avqsz<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -t 2sec -f 3 disk.dev.avrqsz -h acme.com<br>
$ [pmval](http://pcp.io/man/man1/pmval.1.html) -t 2sec -f 3 disk.dev.await -a acme.com/20140902**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

| <br>
Define a derived metric on the command line and monitor it with regular metrics: **<br>
$ [pmrep](http://pcp.io/man/man1/pmrep.1.html) -t 2sec -p -b MB -e "mem.util.allcache = mem.util.bufmem + mem.util.cached + mem.util.slab" mem.util.free mem.util.allcache mem.util.used**
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

## Performance Metrics Inference

Performance Metrics Inference Engine ( [pmie(1)](http://pcp.io/man/man1/pmie.1.html) ) can evaluate rules and generate alarms, run scripts, or automate system management tasks based on live or past performance metrics.

| <br>
To enable and start PMIE on Fedora/RHEL:<br>
**<br>
# chkconfig pmie on<br>
# service pmie start**
| ---------------------------------------------------------------------------------------------------------------------------------
| <br>
To make sure PMIE is running on Debian/Ubuntu:<br>
**<br>
$ sudo update-rc.d pmie defaults<br>
$ sudo service pmie restart**

To enable the monitoring host to run PMIE for collector hosts, add each host to the `/etc/pcp/pmie/control` configuration file.

| <br>
Enable monitoring of metrics from remote host **acme.com** : **<br>
# echo acme.com n PCP_LOG_DIR/pmie/acme.com -c config.acme.com<br><br>
# service pmie restart**
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Some examples in plain English describing what could be done with PMIE:

- If the number of IP received packets exceeds a threshold run a script to adjust firewall rules to limit the incoming traffic
- If 3 out of 4 consecutive samples taken every minute of disk operations exceeds a threshold between 9 AM and 5 PM send an email and write a system log message
- If all hosts in a group have CPU load over a threshold for more than 10 minutes or they have more application processes running than a threshold limit generate an alarm and run a script to tune the application

| <br>
This example shows a PMIE script, checks its syntax, runs it against an archive, and prints a simple message if more than 5 GB of memory was in use between 9 AM and 10 AM using one minute sampling interval:<br>
**<br>
$ cat pmie.ex**

```
bloated = ( mem.util.used > 5 Gbyte )
 -> print "%v memory used on %h!"```

**<br>
$ [pmie](http://pcp.io/man/man1/pmie.1.html) -C pmie.ex<br>
$ [pmie](http://pcp.io/man/man1/pmie.1.html) -t 1min -c pmie.ex -S @09:00 -T @10:00 -a acme.com/20140820**
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[]()

## PCP Web Services

[]()

### Performance Metrics Web Daemon

Performance Metrics Web Daemon ( [pmwebd(1)](http://pcp.io/man/man1/pmwebd.1.html) ) is a front-end to both PMCD and PCP archives, providing a REST web service (over HTTP/JSON) suitable for use by web-based tools wishing to access performance data over HTTP. Custom applications can access all the available PCP information using this method, including custom metrics generated by custom PMDAs.

| <br>
To install the PCP web service on Fedora/RHEL:<br>
**<br>
# yum install pcp-webapi<br>
# chkconfig pmwebd on<br>
# service pmwebd start**
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| <br>
To install the PCP web service on Debian/Ubuntu:<br>
**<br>
$ sudo apt-get install pcp-webapi<br>
$ sudo update-rc.d pmwebd defaults<br>
$ sudo service pmwebd restart**

[]()

### User Web Interface for Performance Metrics

Several browser interfaces for accessing PCP performance metrics are also available. These web interfaces make PCP metrics available via your choice of [Grafana](http://grafana.org/) or [Graphite](http://graphite.wikidot.com/) .

After installing the PCP web services daemon as described above, install the pcp-webjs package and then just point a browser toward <http://localhost:44323> . []()

## Customizing and Extending PCP

PCP PMDAs offer a way for administrators and developers to customize and extend the default PCP installation. The pcp-libs-devel package contains all the needed development related examples, headers, and libraries. New PMDAs can easily be added, below is a quick list of references for starting development:

- Some examples exist below `/var/lib/pcp/pmdas/` - the simple, sample, and txmon PMDAs are easy to read PMDAs.

  - The simple PMDA provides implementations in C, Perl and Python.

- A simple command line monitor tool is `/usr/share/pcp/demos/pmclient` (C language).
- Good initial Python monitor examples are `/usr/libexec/pcp/bin/pcp/pcp-*` (Fedora/RHEL) or `/usr/lib/pcp/bin/pcp-*` (Debian/Ubuntu).

  - Slightly more complex examples are the pcp-free, pmiostat, pmcollectl commands.

- The applications in the [pcp-webjs](https://www.sourceware.org/git/gitweb.cgi?p=pcpfans.git;a=shortlog;h=refs/heads/webjs) source tree are helpful when developing new web applications.

[]()

## Additional Information
