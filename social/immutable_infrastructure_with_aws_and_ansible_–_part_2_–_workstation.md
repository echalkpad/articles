# Immutable Infrastructure with AWS and Ansible – Part 2 – Workstation

[Original URL](http://vcdxpert.com/?p=148)

> In the first part of this series, we setup our workstation so that it could communicate with the Amazon Web Services APIs, and setup our AWS account so that it was ready to provision EC2 compute...

In the [first part of this series](http://vcdxpert.com/?p=105), we setup our workstation so that it could communicate with the Amazon Web Services APIs, and setup our AWS account so that it was ready to provision EC2 compute infrastructure. In this section, we'll start building our Ansible playbook that will provision immutable infrastructure.

## Ansible Dynamic Inventory

When working with cloud resources, Ansible has the capability of using a dynamic inventory system to find and configure all of your instances within AWS, or any other cloud provider. In order for this to work properly, we need to setup the EC2 external inventory script in our playbook.

1. First, create the playbook folder (I named mine ~/immutable) and the inventory folder within it:<br>
  `mkdir -p ~/immutable/inventory;cd ~/immutable/inventory`
2. Next, download the EC2 external inventory script from Ansible:<br>
  `wget https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.py`
3. Make the script executable by typing:<br>
  `chmod +x ec2.py`
4. Configure the EC2 external inventory script by creating a new file called ec2.ini in the inventory folder alongside the ec2.py script. If you specify the region you are working with, you will significantly decrease the execution time because the script will not need to scan every EC2 region for instances. Here is a copy of my ec2.ini file, configured to use the us-east-1 region:

  <div class="crayon-nums-content">
    <p>100</p>
    <p>101</p>
    <p>102</p>
    <p>103</p>
    <p>104</p>
    <p>105</p>
    <p>106</p>
    <p>107</p>
    <p>108</p>
    <p>109</p>
    <p>110</p>
    <p>111</p>
    <p>112</p>
    <p>113</p>
    <p>114</p>
    <p>115</p>
    <p>116</p>
    <p>117</p>
    <p>118</p>
    <p>119</p>
    <p>120</p>
    <p>121</p>
    <p>122</p>
    <p>123</p>
    <p>124</p>
    <p>125</p>
    <p>126</p>
    <p>127</p>
    <p>128</p>
    <p>129</p>
    <p>130</p>
    <p>131</p>
    <p>132</p>
    <p>133</p>
    <p>134</p>
    <p>135</p>
    <p>136</p>
    <p>137</p>
  </div> | <div class="crayon-pre">
    <p>
    <span class="crayon-p"># Ansible EC2 external inventory script settings</span>
  </p>
    <p>
    <span class="crayon-sy">[</span>
    <span class="crayon-v">ec2</span>
    <span class="crayon-sy">]</span>
  </p>
    <p>
    <span class="crayon-p"># to talk to a private eucalyptus instance uncomment these lines</span>
  </p>
    <p>
    <span class="crayon-p"># and edit edit eucalyptus<em>host to be the host name of your cloud controller</em></span>
  </p>
    <em>
    <p><span class="crayon-p">#eucalyptus = True</span></p>
    <p><span class="crayon-p">#eucalyptus_host = clc.cloud.domain.org</span></p>
    <p><span class="crayon-p"># AWS regions to make calls to. Set this to ‘all’ to make request to all regions</span></p>
    <p><span class="crayon-p"># in AWS and merge the results together. Alternatively, set this to a comma</span></p>
    <p><span class="crayon-p"># separated list of regions. E.g. ‘us-east-1,us-west-1,us-west-2’</span></p>
    <p><span class="crayon-v">regions</span><span class="crayon-o">=</span><span class="crayon-v">us</span><span class="crayon-o">-</span><span class="crayon-v">east</span><span class="crayon-o">-</span><span class="crayon-cn">1</span></p>
    <p><span class="crayon-v">regions_exclude</span><span class="crayon-o">=</span></p>
    <p><span class="crayon-p"># When generating inventory, Ansible needs to know how to address a server.</span></p>
    <p><span class="crayon-p"># Each EC2 instance has a lot of variables associated with it. Here is the list:</span></p>
    <p><span class="crayon-p">#   <a href="http://docs.pythonboto.org/en/latest/ref/ec2.html#module-boto.ec2.instance">http://docs.pythonboto.org/en/latest/ref/ec2.html#module-boto.ec2.instance</a></span></p>
    <p><span class="crayon-p"># Below are 2 variables that are used as the address of a server:</span></p>
    <p><span class="crayon-p">#   - destination_variable</span></p>
    <p><span class="crayon-p">#   - vpc_destination_variable</span></p>
    <p><span class="crayon-p"># This is the normal destination variable to use. If you are running Ansible</span></p>
    <p><span class="crayon-p"># from outside EC2, then ‘public_dns_name’ makes the most sense. If you are</span></p>
    <p><span class="crayon-p"># running Ansible from within EC2, then perhaps you want to use the internal</span></p>
    <p><span class="crayon-p"># address, and should set this to ‘private_dns_name’. The key of an EC2 tag</span></p>
    <p><span class="crayon-p"># may optionally be used; however the boto instance variables hold precedence</span></p>
    <p><span class="crayon-p"># in the event of a collision.</span></p>
    <p><span class="crayon-v">destination_variable</span><span class="crayon-o">=</span><span class="crayon-v">public_dns_name</span></p>
    <p><span class="crayon-p">#destination_variable = private_dns_name</span></p>
    <p><span class="crayon-p"># For server inside a VPC, using DNS names may not make sense. When an instance</span></p>
    <p><span class="crayon-p"># has ‘subnet_id’ set, this variable is used. If the subnet is public, setting</span></p>
    <p><span class="crayon-p"># this to ‘ip_address’ will return the public IP address. For instances in a</span></p>
    <p><span class="crayon-p"># private subnet, this should be set to ‘private_ip_address’, and Ansible must</span></p>
    <p><span class="crayon-p"># be run from within EC2\. The key of an EC2 tag may optionally be used; however</span></p>
    <p><span class="crayon-p"># the boto instance variables hold precedence in the event of a collision.</span></p>
  </em>
    <p><em><span class="crayon-p"># WARNING: - instances that are in the private vpc, _without</span></em> public ip address </p>
    <p>
    <span class="crayon-p"># will not be listed in the inventory until You set:</span>
  </p>
    <p>
    <span class="crayon-p">#vpc_destination_variable = private_ip_address</span>
  </p>
    <p>
    <span class="crayon-v">vpc_destination_variable</span>
    <span class="crayon-o">=</span>
    <span class="crayon-v">ip_address</span>
  </p>
    <p>
    <span class="crayon-p"># To tag instances on EC2 with the resource records that point to them from</span>
  </p>
    <p>
    <span class="crayon-p"># Route53, uncomment and set ‘route53’ to True.</span>
  </p>
    <p>
    <span class="crayon-v">route53</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-p"># To exclude RDS instances from the inventory, uncomment and set to False.</span>
  </p>
    <p>
    <span class="crayon-p">#rds = False</span>
  </p>
    <p>
    <span class="crayon-p"># To exclude ElastiCache instances from the inventory, uncomment and set to False.</span>
  </p>
    <p>
    <span class="crayon-p">#elasticache = False</span>
  </p>
    <p>
    <span class="crayon-p"># Additionally, you can specify the list of zones to exclude looking up in</span>
  </p>
    <p>
    <span class="crayon-p"># ‘route53_excluded_zones’ as a comma-separated list.</span>
  </p>
    <p>
    <span class="crayon-p"># route53_excluded_zones = samplezone1.com, samplezone2.com</span>
  </p>
    <p>
    <span class="crayon-p"># By default, only EC2 instances in the ‘running’ state are returned. Set</span>
  </p>
    <p>
    <span class="crayon-p"># ‘all_instances’ to True to return all instances regardless of state.</span>
  </p>
    <p>
    <span class="crayon-v">all_instances</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-p"># By default, only RDS instances in the ‘available’ state are returned.  Set</span>
  </p>
    <p>
    <span class="crayon-p"># ‘all_rds_instances’ to True return all RDS instances regardless of state.</span>
  </p>
    <p>
    <span class="crayon-v">all_rds_instances</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-p"># By default, only ElastiCache clusters and nodes in the ‘available’ state</span>
  </p>
    <p>
    <span class="crayon-p"># are returned. Set ‘all_elasticache_clusters’ and/or ‘all_elastic_nodes’</span>
  </p>
    <p>
    <span class="crayon-p"># to True return all ElastiCache clusters and nodes, regardless of state.</span>
  </p>
    <p>
    <span class="crayon-p"># Note that all_elasticache_nodes only applies to listed clusters. That means</span>
  </p>
    <p>
    <span class="crayon-p"># if you set all_elastic_clusters to false, no node will be return from</span>
  </p>
    <p>
    <span class="crayon-p"># unavailable clusters, regardless of the state and to what you set for</span>
  </p>
    <p>
    <span class="crayon-p"># all_elasticache_nodes.</span>
  </p>
    <p>
    <span class="crayon-v">all_elasticache_replication_groups</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-v">all_elasticache_clusters</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-v">all_elasticache_nodes</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-p"># API calls to EC2 are slow. For this reason, we cache the results of an API</span>
  </p>
    <p>
    <span class="crayon-p"># call. Set this to the path you want cache files to be written to. Two files</span>
  </p>
    <p>
    <span class="crayon-p"># will be written to this directory:</span>
  </p>
    <p>
    <span class="crayon-p">#   - ansible-ec2.cache</span>
  </p>
    <p>
    <span class="crayon-p">#   - ansible-ec2.index</span>
  </p>
    <p>
    <span class="crayon-v">cache_path</span>
    <span class="crayon-o">=</span>
    <span class="crayon-o">~</span>
    <span class="crayon-o">/</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-v">ansible</span>
    <span class="crayon-o">/</span>
    <span class="crayon-v">tmp</span>
  </p>
    <p>
    <span class="crayon-p"># The number of seconds a cache file is considered valid. After this many</span>
  </p>
    <p>
    <span class="crayon-p"># seconds, a new API call will be made, and the cache file will be updated.</span>
  </p>
    <p>
    <span class="crayon-p"># To disable the cache, set this value to 0</span>
  </p>
    <p>
    <span class="crayon-v">cache_max_age</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
  </p>
    <p>
    <span class="crayon-p"># Organize groups into a nested/hierarchy instead of a flat namespace.</span>
  </p>
    <p>
    <span class="crayon-v">nested_groups</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">False</span>
  </p>
    <p>
    <span class="crayon-p"># The EC2 inventory output can become very large. To manage its size,</span>
  </p>
    <p>
    <span class="crayon-p"># configure which groups should be created.</span>
  </p>
    <p>
    <span class="crayon-v">group_by_instance_id</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_region</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_availability_zone</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_ami_id</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_instance_type</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_key_pair</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_vpc_id</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_security_group</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_tag_keys</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_tag_none</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_route53_names</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_rds_engine</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_rds_parameter_group</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_elasticache_engine</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_elasticache_cluster</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_elasticache_parameter_group</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-v">group_by_elasticache_replication_group</span>
    <span class="crayon-o">=</span>
    <span class="crayon-t">True</span>
  </p>
    <p>
    <span class="crayon-p"># If you only want to include hosts that match a certain regular expression</span>
  </p>
    <p>
    <span class="crayon-p"># pattern_include = staging-<em>
  </em></span>
  </p>
    <em>
    <p><span class="crayon-p"># If you want to exclude any hosts that match a certain regular expression</span></p>
  </em>
    <p>
    <em><span class="crayon-p"># pattern_exclude = staging-</span></em>
  </p>
    <p>
    <span class="crayon-p"># Instance filters can be used to control which instances are retrieved for</span>
  </p>
    <p>
    <span class="crayon-p"># inventory. For the full list of possible filters, please read the EC2 API</span>
  </p>
    <p>
    <span class="crayon-p"># docs: <a href="http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeInstances.html#query-DescribeInstances-filters">http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeInstances.html#query-DescribeInstances-filters</a></span>
  </p>
    <p>
    <span class="crayon-p"># Filters are key/value pairs separated by ‘=’, to list multiple filters use</span>
  </p>
    <p>
    <span class="crayon-p"># a list separated by commas. See examples below.</span>
  </p>
    <p>
    <span class="crayon-p"># Retrieve only instances with (key=value) env=staging tag</span>
  </p>
    <p>
    <span class="crayon-p"># instance_filters = tag:env=staging</span>
  </p>
    <p>
    <span class="crayon-p"># Retrieve only instances with role=webservers OR role=dbservers tag</span>
  </p>
    <p>
    <span class="crayon-p"># instance_filters = tag:role=webservers,tag:role=dbservers</span>
  </p>
    <p>
    <span class="crayon-p"># Retrieve only t1.micro instances OR instances with tag env=staging</span>
  </p>
    <p>
    <span class="crayon-p"># instance_filters = instance-type=t1.micro,tag:env=staging</span>
  </p>
    <p>
    <span class="crayon-p"># You can use wildcards in filter values also. Below will list instances which</span>
  </p>
    <p>
    <span class="crayon-p"># tag Name value matches webservers1<em>
  </em></span>
  </p>
    <em>
    <p><span class="crayon-p"># (ex. webservers15, webservers1a, webservers123 etc) </span></p>
  </em>
    <p>
    <em><span class="crayon-p"># instance_filters = tag:Name=webservers1</span></em>
  </p>
  </div>
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

5. Next, create the default Ansible configuration for this playbook by editing a file in the root of the playbook directory (in our example, ~/immutable), named ansible.cfg. This file should have the following text in it:<br>

  <span class="crayon-title">ansible.cfg</span>

   | <div class="crayon-pre">
    <p>
    <span class="crayon-i">[defaults]</span>
  </p>
    <p>remote<span class="crayon-sy">
    <em>
  </em>
  </span><em>user=ubuntu</em></p>
    <em>
  </em>
    <p><em>host<span class="crayon-sy">
  </span></em>key<span class="crayon-sy">
    <em>
  </em>
  </span><em>checking=False</em></p>
    <em>
    <p>inventory=<span class="crayon-o">.</span>/inventory/ec2<span class="crayon-o">.</span>py</p>
    <p><span class="crayon-i">[ssh_connection]</span></p>
  </em>
    <p><em>control<span class="crayon-sy">
  </span></em>path=<span class="crayon-sy">%</span><span class="crayon-sy">(</span>directory<span class="crayon-sy">)</span>s/<span class="crayon-sy">%</span><span class="crayon-ta">%C</span></p>
  </div>
   | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

To test and ensure that this script is working properly (and that your boto credentials are setup properly), execute the following command:<br>
`./ec2.py --list`<br>
You should see the following output:<br>

[<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-12.06.19-PM-300x86.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-12.06.19-PM-768x220.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-12.06.19-PM.png%20944w" class="alignnone wp-image-173" width="399">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-12.06.19-PM.png)

## Ansible Roles

Roles are a way to automatically load certain variables and tasks into an Ansible playbook, and allow you to reuse your tasks in a modular way. We will heavily (ab)use roles to make the tasks in our playbook reusable, since many of our infrastructure provisioning operations will use the same tasks repeatedly.

### Group Variables

The following group variables will apply to any tasks configured in our playbook, unless we override them at the task level. This will allow us to specify a set of sensible defaults that will work for most provisioning use cases, but still have flexibility to change them when we need it. Start by creating a folder for your playbook (I called it immutable, but you can call it whatever you like), then create a group_vars folder underneath it:<br>
`cd ~immutable; mkdir group_vars`<br>
Now, we can edit the file all.yml in that folder we just created, group_vars, to contain the following text. Please note that indentation is important in YAML syntax:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-o">—</span>
</p>
  <p>
  <span class="crayon-c"># group_vars/all.yml</span>
</p>
  <p>
  <span class="crayon-s">region</span>
  <span>: us-east-1</span>
</p>
  <p>
  <span class="crayon-s">zone</span>
  <span>: us-east-1a # zone that the master AMI will be configured in</span>
</p>
  <p>
  <span class="crayon-s">keypair</span>
  <span>: immutable</span>
</p>
  <p>
  <span class="crayon-s">security_groups</span>
  <span>: [’default’]</span>
</p>
  <p>
  <span class="crayon-s">instance_type</span>
  <span>: t2.micro</span>
</p>
  <p>
  <span class="crayon-c"># specify group_name on the command line with -e group_name=devX</span>
</p>
  <p>
  <span class="crayon-s">group_name</span>
  <span>: test</span>
</p>
  <p>
  <span class="crayon-s">instances_min</span>
  <span>: 1 # minimum number of instances in the auto scaling group</span>
</p>
  <p>
  <span class="crayon-s">instances_max</span>
  <span>: 3 # maximum number of instances in the auto scaling group</span>
</p>
  <p>
  <span class="crayon-s">iam_profile</span>
  <span>: “noaccess”</span>
</p>
  <p>
  <span class="crayon-s">volumes</span>
  <span>:</span>
</p>
  <p>
  <span class="crayon-s">- device_name</span>
  <span>: /dev/sda1</span>
</p>
  <p>
  <span class="crayon-s">device_type</span>
  <span>: gp2</span>
</p>
  <p>
  <span class="crayon-s">volume_size</span>
  <span>: 8 # size of the root disk</span>
</p>
  <p>
  <span class="crayon-s">delete_on_termination</span>
  <span>: true</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now that our group_vars are setup, we can move onto creating our first role.

### The Launch Role

The launch role performs an important first step – it first searches for the latest Ubuntu 14.04 LTS (long term support) AMI (Amazon Machine Image) that is published by Canonical, the creators of Ubuntu, then launches a new EC2 compute instance, in the region and availability zone specified in our group_vars file. Note that the launch role also launches a very small compute instance (t2.micro) because this instance will only live for a short time while it is configured by subsequent tasks, then baked into a golden master AMI snapshot that lives in S3 object storage.

A quick note about Availability Zones: if you comment out the zone variable in our group_vars file, your instances will be launched in a random zone within the region specified. This can be useful if you want to ensure that an outage in a single AZ doesn't take down every instance in your auto-scaling group, but there is a trade-off as data transfer between zones incurs a charge, so if your database, for example, is in another zone, you'll pay a small network bandwidth fee to access it.

Create a new folder under your playbook directory called roles, and create a launch folder within it, then create a tasks folder under that, then edit a file called main.yml in this tasks folder:<br>
`mkdir -p roles/launch/tasks`<br>
Now, put the following contents in the main.yml file:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-o">—</span>
</p>
  <p>
  <span class="crayon-c"># roles/launch/tasks/main.yml</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Search for the latest Ubuntu 14.04 AMI</span>
</p>
  <p>
  <span class="crayon-s">ec2<em>ami_find</em></span>
  <em><span>:</span></em>
</p>
  <em>
  <p><span class="crayon-s">region</span><span>: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>region<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    name: "</span>ubuntu/images/hvm-ssd/ubuntu-trusty-14<span class="crayon-o">.</span>04-amd64-server-*<span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    owner: 099720109477</span></p>
  <p><span class="crayon-i">    sort: name</span></p>
  <p><span class="crayon-i">    sort_order: descending</span></p>
  <p><span class="crayon-i">    sort_end: 1</span></p>
  <p><span class="crayon-i">    no_result_action: fail</span></p>
  <p><span class="crayon-i">  register: ami_result</span></p>
  <p><span class="crayon-i">- name: Launch new instance</span></p>
  <p><span class="crayon-i">  ec2:</span></p>
  <p><span class="crayon-i">    region: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>region<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    keypair: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>keypair<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    zone: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>zone<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
</em>
  <p><em><span class="crayon-i">    group: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>security<span class="crayon-sy">
</span></em>groups<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    image: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>ami<span class="crayon-sy">
  <em>
</em>
</span><em>result<span class="crayon-o">.</span>results<span class="crayon-i">[0]</span><span class="crayon-o">.</span>ami<span class="crayon-sy">
</span></em>id<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p>
  <span class="crayon-i"># No point in creating an expensive instance just during the AMI configuration phase - let the ASG do that</span>
</p>
  <p><span class="crayon-i">#    instance<em>type: "</em></span><em><span class="crayon-o">{</span><span class="crayon-o">{</span>instance<span class="crayon-sy">
</span></em>type<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p>
  <span class="crayon-i">    instance<em>type: "</em></span>
  <em>t2<span class="crayon-o">.</span>micro<span class="crayon-i">"</span></em>
</p>
  <em>
  <p><span class="crayon-i">    instance_tags:</span></p>
  <p><span class="crayon-i">      Name: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>name<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    volumes: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>volumes<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    wait: yes</span></p>
  <p><span class="crayon-i">  register: ec2</span></p>
  <p><span class="crayon-i">- name: Add new instances to host group</span></p>
  <p><span class="crayon-i">  add_host:</span></p>
</em>
  <p><em><span class="crayon-i">    name: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>item<span class="crayon-o">.</span>public<span class="crayon-sy">
</span></em>dns<span class="crayon-sy">
  <em>
</em>
</span><em>name<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></em></p>
  <em>
  <p><span class="crayon-i">    groups: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>name<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">    ec2_id: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>item<span class="crayon-o">.</span>id<span class="crayon-o">}</span><span class="crayon-o">}</span><span class="crayon-i">"</span></p>
  <p><span class="crayon-i">  with_items: ec2.instances</span></p>
  <p><span class="crayon-i">- name: Wait for instance to boot</span></p>
  <p><span class="crayon-i">  wait_for:</span></p>
</em>
  <p><em><span class="crayon-i">    host: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>item<span class="crayon-o">.</span>public<span class="crayon-sy">
</span></em>dns<span class="crayon-sy">_</span>name<span class="crayon-o">}</span><span class="crayon-o">}</span>"</p>
  <p>
  <span class="crayon-s">port</span>
  <span>: 22</span>
</p>
  <p>
  <span class="crayon-s">delay</span>
  <span>: 30</span>
</p>
  <p>
  <span class="crayon-s">timeout</span>
  <span>: 300</span>
</p>
  <p>
  <span class="crayon-s">state</span>
  <span>: started</span>
</p>
  <p>
  <span class="crayon-s">with_items</span>
  <span>: ec2.instances</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You'll notice that this launch role also waits for the instance to boot by waiting for port 22 (ssh) to be available on the host. This is useful because subsequent tasks will use an ssh connection to configure the system, so we want to ensure the system is completely booted before we proceed.

### The Workstation Role

Now that we have a role that can launch a brand new t2.micro instance, our next role will allow us to configure this instance to be used as a workstation. This workstation configuration will be fairly simplistic, however, you can easily customize it as much as you want later. This is mainly just to illustrate how you would configure the golden image.

There are two directories we need to create for this role, the tasks directory, as well as the files directory, as there is an init script we want to populate onto the workstation that will create a swapfile on first boot:<br>
`mkdir -p roles/workstation/tasks;mkdir roles/workstation/files`<br>
Next, we'll create the task:

<span class="crayon-title">Workstation Play</span>

<div class="crayon-nums-content">
  <p>100</p>
  <p>101</p>
  <p>102</p>
  <p>103</p>
  <p>104</p>
  <p>105</p>
  <p>106</p>
  <p>107</p>
  <p>108</p>
  <p>109</p>
  <p>110</p>
  <p>111</p>
  <p>112</p>
  <p>113</p>
  <p>114</p>
  <p>115</p>
  <p>116</p>
  <p>117</p>
  <p>118</p>
  <p>119</p>
  <p>120</p>
  <p>121</p>
  <p>122</p>
  <p>123</p>
  <p>124</p>
  <p>125</p>
  <p>126</p>
  <p>127</p>
  <p>128</p>
  <p>129</p>
  <p>130</p>
  <p>131</p>
  <p>132</p>
  <p>133</p>
  <p>134</p>
  <p>135</p>
  <p>136</p>
  <p>137</p>
  <p>138</p>
  <p>139</p>
  <p>140</p>
  <p>141</p>
  <p>142</p>
  <p>143</p>
  <p>144</p>
  <p>145</p>
  <p>146</p>
  <p>147</p>
  <p>148</p>
  <p>149</p>
  <p>150</p>
  <p>151</p>
  <p>152</p>
  <p>153</p>
  <p>154</p>
  <p>155</p>
  <p>156</p>
  <p>157</p>
  <p>158</p>
  <p>159</p>
  <p>160</p>
  <p>161</p>
  <p>162</p>
  <p>163</p>
  <p>164</p>
  <p>165</p>
  <p>166</p>
  <p>167</p>
  <p>168</p>
  <p>169</p>
</div> | <div class="crayon-pre">
  <p>
  <span class="crayon-o">—</span>
</p>
  <p>
  <span class="crayon-c"># roles/workstation/tasks/main.yml</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: add timezone configuration</span>
</p>
  <p><span class="crayon-s">command</span><span>: bash -c ’echo US/Eastern </span><span class="crayon-o">&gt;</span>/etc/timezone<span class="crayon-i">’</span></p>
  <p>
  <span class="crayon-i">  - name: add kernel parameters to /etc/sysctl.d/60-custom.conf</span>
</p>
  <p>
  <span class="crayon-i">    blockinfile: |</span>
</p>
  <p>
  <span class="crayon-i">      dest=/etc/sysctl.d/60-custom.conf</span>
</p>
  <p>
  <span class="crayon-i">      create=yes</span>
</p>
  <p>
  <span class="crayon-i">      content="# Auto-reboot linux 10 seconds after a kernel panic</span>
</p>
  <p>
  <span class="crayon-i">        kernel.panic = 10</span>
</p>
  <p>
  <span class="crayon-i">        kernel.panic_on_oops = 10</span>
</p>
  <p>
  <span class="crayon-i">        kernel.unknown_nmi_panic = 10</span>
</p>
  <p>
  <span class="crayon-i">        kernel.panic_on_unrecovered_nmi = 10</span>
</p>
  <p>
  <span class="crayon-i">        kernel.panic_on_io_nmi = 10</span>
</p>
  <p>
  <span class="crayon-i">        # Controls whether core dumps will append the PID to the core filename, useful for debugging multi-threaded applications</span>
</p>
  <p>
  <span class="crayon-i">        kernel.core_uses_pid = 1</span>
</p>
  <p>
  <span class="crayon-i">        # Turn on address space randomization - security is super important</span>
</p>
  <p>
  <span class="crayon-i">        kernel.randomize_va_space = 2</span>
</p>
  <p>
  <span class="crayon-i">        vm.swappiness = 0</span>
</p>
  <p>
  <span class="crayon-i">        vm.dirty_ratio = 80</span>
</p>
  <p>
  <span class="crayon-i">        vm.dirty_background_ratio = 5</span>
</p>
  <p>
  <span class="crayon-i">        vm.dirty_expire_centisecs = 12000</span>
</p>
  <p>
  <span class="crayon-i">        vm.overcommit_memory = 1</span>
</p>
  <p>
  <span class="crayon-i">        # —— VM ——</span>
</p>
  <p>
  <span class="crayon-i">        fs.file-max = 204708</span>
</p>
  <p>
  <span class="crayon-i">        #fs.epoll.max_user_instances = 4096</span>
</p>
  <p>
  <span class="crayon-i">        fs.suid_dumpable = 0</span>
</p>
  <p>
  <span class="crayon-i">        # —— NETWORK SECURITY ——</span>
</p>
  <p>
  <span class="crayon-i">        # Turn on protection for bad icmp error messages</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.icmp_ignore_bogus_error_responses = 1</span>
</p>
  <p>
  <span class="crayon-i">        # Turn on syncookies for SYN flood attack protection</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_syncookies = 1</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_max_syn_backlog = 8096</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_synack_retries = 2</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_syn_retries = 2</span>
</p>
  <p>
  <span class="crayon-i">        # Log suspicious packets, such as spoofed, source-routed, and redirect</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.conf.all.log_martians = 1</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.conf.default.log_martians = 1</span>
</p>
  <p>
  <span class="crayon-i">        # Disables these ipv4 features, not very legitimate uses</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.conf.all.accept_source_route = 0</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.conf.default.accept_source_route = 0</span>
</p>
  <p>
  <span class="crayon-i">        # —— NETWORK PERFORMANCE ——</span>
</p>
  <p>
  <span class="crayon-i">        # Netflix 2014 recommendations</span>
</p>
  <p>
  <span class="crayon-i">        net.core.netdev_max_backlog = 5000</span>
</p>
  <p>
  <span class="crayon-i">        net.core.rmem_max = 16777216</span>
</p>
  <p>
  <span class="crayon-i">        net.core.wmem_max = 16777216</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_wmem = 4096 12582912 16777216</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_rmem = 4096 12582912 16777216</span>
</p>
  <p>
  <span class="crayon-i">        # Allow reusing sockets in TIME_WAIT state for new connections</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_tw_reuse = 1</span>
</p>
  <p>
  <span class="crayon-i">        # Socket max connections waiting to get accepted; the listen() backlog.</span>
</p>
  <p>
  <span class="crayon-i">        # Default is 128.</span>
</p>
  <p>
  <span class="crayon-i">        net.core.somaxconn = 4096</span>
</p>
  <p>
  <span class="crayon-i">        # Decrease fin timeout. After telling the client we are closing, how long to wait for a FIN, ACK?</span>
</p>
  <p>
  <span class="crayon-i">        # Default is 60.</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_fin_timeout = 10</span>
</p>
  <p>
  <span class="crayon-i">        # Avoid falling back to slow start after a connection goes idle</span>
</p>
  <p>
  <span class="crayon-i">        # keeps our cwnd large with the keep alive connections</span>
</p>
  <p>
  <span class="crayon-i">        net.ipv4.tcp_slow_start_after_idle = 0"</span>
</p>
  <p>
  <span class="crayon-i">  - name: reload sysctl kernel parameter settings</span>
</p>
  <p><span class="crayon-i">    command: bash -c ’</span>sysctl-p/etc/sysctl<span class="crayon-o">.</span>d/60-custom<span class="crayon-o">.</span>conf<span class="crayon-i">’</span></p>
  <p>
  <span class="crayon-i">  - name: copy swapfile init script</span>
</p>
  <p>
  <span class="crayon-i">    copy:  src=aws-swap-init dest=/etc/init.d/aws-swap-init mode=0755</span>
</p>
  <p>
  <span class="crayon-i">  - name: register swapfile init script</span>
</p>
  <p><span class="crayon-i">    command: bash -c ’</span>update-rc<span class="crayon-o">.</span>daws-swap-initdefaults<span class="crayon-i">’</span></p>
  <p>
  <span class="crayon-i">  - name: add entries to sudoers</span>
</p>
  <p>
  <span class="crayon-i">    lineinfile: |</span>
</p>
  <p>
  <span class="crayon-i">      dest=/etc/sudoers.d/90-cloud-init-users</span>
</p>
  <p>
  <span class="crayon-i">      line=“luke ALL=(ALL) NOPASSWD:ALL”</span>
</p>
  <p>
  <span class="crayon-i">  - name: add luke user account</span>
</p>
  <p>
  <span class="crayon-i">    user: name=luke uid=1001 comment=“Luke Youngblood” createhome=yes shell=/bin/bash</span>
</p>
  <p>
  <span class="crayon-i">  - name: accept Oracle license part 1</span>
</p>
  <p><span class="crayon-i">    command: bash -c ’</span>echodebconfshared/accepted-oracle-license-v1-1selecttrue<span class="crayon-o">|</span>/usr/bin/debconf-set-selections<span class="crayon-i">’</span></p>
  <p>
  <span class="crayon-i">  - name: accept Oracle license part 2</span>
</p>
  <p><span class="crayon-i">    command: bash -c ’</span>echodebconfshared/accepted-oracle-license-v1-1seentrue<span class="crayon-o">|</span>/usr/bin/debconf-set-selections<span class="crayon-i">’</span></p>
  <p>
  <span class="crayon-i">  - name: Update apt cache</span>
</p>
  <p>
  <span class="crayon-i">    apt: update_cache=yes</span>
</p>
  <p>
  <span class="crayon-i">  - name: Add apt key for ubuntu</span>
</p>
  <p>
  <span class="crayon-i">    apt_key: keyserver=keyserver.ubuntu.com id=E56151BF</span>
</p>
  <p>
  <span class="crayon-i">  - name: Determine Linux distribution distributor</span>
</p>
  <p>
  <span class="crayon-i">    shell: lsb_release -is | tr ’</span>
  <span class="crayon-i">‘[:upper:]’</span>
  <span class="crayon-i">‘ ’</span>
  <span class="crayon-i">‘[:lower:]’</span>
  <span class="crayon-i">’</span>
</p>
  <p>
  <span class="crayon-i">    register: release_distributor</span>
</p>
  <p>
  <span class="crayon-i">  - name: Determine Linux distribution codename</span>
</p>
  <p>
  <span class="crayon-i">    command: lsb_release -cs</span>
</p>
  <p>
  <span class="crayon-i">    register: release_codename</span>
</p>
  <p>
  <span class="crayon-i">  - name: Add Mesosphere repository to sources list</span>
</p>
  <p>
  <span class="crayon-i">    lineinfile: &gt;</span>
</p>
  <p>
  <span class="crayon-i">      dest=/etc/apt/sources.list.d/mesosphere.list</span>
</p>
  <p>
  <span class="crayon-i">      line=“deb <a href="http://repos.mesosphere.io/{{">http://repos.mesosphere.io/{{</a> release_distributor.stdout }} {{ release_codename.stdout }} main”</span>
</p>
  <p>
  <span class="crayon-i">      mode=0644</span>
</p>
  <p>
  <span class="crayon-i">      create=yes</span>
</p>
  <p>
  <span class="crayon-i">  - name: Add java apt repository</span>
</p>
  <p>
  <span class="crayon-i">    apt_repository: repo=’</span>
  <span class="crayon-s">ppa</span>
  <span>:webupd8team/java’</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Add Google Chrome key</span>
</p>
  <p>
  <span class="crayon-s">shell</span>
  <span>: wget -q -O - https</span>
  <span>://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Add Google Chrome repo</span>
</p>
  <p>
  <span class="crayon-s">copy</span>
  <span>: </span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>content=<span class="crayon-i">“deb <a href="http://dl.google.com/linux/chrome/deb/">http://dl.google.com/linux/chrome/deb/</a> stable main”</span></p>
  <p>dest=<span class="crayon-i">“/etc/apt/sources.list.d/google-chrome.list”</span></p>
  <p>owner=root</p>
  <p>group=root</p>
  <p>mode=0644</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Update apt cache</span>
</p>
  <p>
  <span class="crayon-s">apt</span>
  <span>: update_cache=yes</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Install packages</span>
</p>
  <p><span class="crayon-s">apt</span><span>: pkg=</span><span class="crayon-o">{</span><span class="crayon-o">{</span>item<span class="crayon-o">}</span><span class="crayon-o">}</span>state=installed</p>
  <p>
  <span class="crayon-s">with_items</span>
  <span>: </span>
</p>
  <p>-nfs-common</p>
  <p>-oracle-java8-installer</p>
  <p>-liblapack-dev</p>
  <p>-libblas-dev</p>
  <p>-gfortran</p>
  <p>-ntp</p>
  <p>-vnc4server</p>
  <p>-xfce4</p>
  <p>-xfce4-goodies</p>
  <p>-emacs</p>
  <p>-firefox</p>
  <p>-git</p>
  <p>-maven</p>
  <p>-npm</p>
  <p>-python-pip</p>
  <p>-clusterssh</p>
  <p>-graphviz</p>
  <p>-google-chrome-stable</p>
  <p>-python-numpy</p>
  <p>-python-scipy</p>
  <p>-python-dev</p>
  <p>-python-nose</p>
  <p>-g++</p>
  <p>-libopenblas-dev</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Install boto and run setup</span>
</p>
  <p>
  <span class="crayon-s">command</span>
  <span>: bash -c ’git clone git</span>
  <span>://github.com/boto/boto.git;cd /home/ubuntu/boto;python setup.py install’</span>
</p>
  <p>
  <span class="crayon-s">args</span>
  <span>:</span>
</p>
  <p>
  <span class="crayon-s">chdir</span>
  <span>: /home/ubuntu</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Install bower</span>
</p>
  <p>
  <span class="crayon-s">command</span>
  <span>: bash -c ‘npm install -g bower’</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Install Theano</span>
</p>
  <p>
  <span class="crayon-s">command</span>
  <span>: bash -c ‘pip install Theano’</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Update all packages to the latest version</span>
</p>
  <p>
  <span class="crayon-s">apt</span>
  <span>: upgrade=dist</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: Create nodejs symlink</span>
</p>
  <p>
  <span class="crayon-s">file</span>
  <span>: src=/usr/bin/nodejs dest=/usr/bin/node state=link</span>
</p>
  <p>
  <span class="crayon-s">- name</span>
  <span>: execute dpkg-reconfigure</span>
</p>
  <p>
  <span class="crayon-s">command</span>
  <span>: “dpkg-reconfigure -f noninteractive tzdata”</span>
</p>
</div>
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### Initializing a swap file automatically

When you provision the Ubuntu 14.04 LTS instance, it won't have a swapfile by default. This is a bit risky because if you run out of memory your system could become unstable. This file should be placed in roles/workstation/files/aws-swap-init, where the task above will copy it to your workstation during the configuration process, so that a swap file will be created when the system is booted for the first time.

<span class="crayon-title">roles/workstation/files/aws-swap-init</span>

<div class="crayon-nums-content">
  <p>100</p>
  <p>101</p>
  <p>102</p>
  <p>103</p>
  <p>104</p>
  <p>105</p>
  <p>106</p>
  <p>107</p>
  <p>108</p>
  <p>109</p>
  <p>110</p>
  <p>111</p>
  <p>112</p>
  <p>113</p>
  <p>114</p>
  <p>115</p>
  <p>116</p>
</div> | <div class="crayon-pre">
  <p>
  <span class="crayon-p">#!/bin/sh</span>
</p>
  <p>
  <span class="crayon-c"># aws-swap-init</span>
</p>
  <p>
  <span class="crayon-c"># chkconfig: 2345 99 10</span>
</p>
  <p>
  <span class="crayon-c"># description: Check to see if epemeral disk is mounted. If it is, and swap doesn’t exist, \</span>
</p>
  <p>
  <span class="crayon-c">#              create swap equivalent to memory on the ephemeral drive. If it isn’t, and \</span>
</p>
  <p>
  <span class="crayon-c">#              exist, create 4096MB of swap on /.</span>
</p>
  <p>
  <span class="crayon-c"># processname: aws-swap-init</span>
</p>
  <p>
  <span class="crayon-c">### BEGIN INIT INFO</span>
</p>
  <p>
  <span class="crayon-c"># Provides:          aws-swap-init</span>
</p>
  <p>
  <span class="crayon-c"># Required-Start:    $remote_fs $syslog</span>
</p>
  <p>
  <span class="crayon-c"># Required-Stop:     $remote_fs $syslog</span>
</p>
  <p>
  <span class="crayon-c"># Default-Start:     2 3 4 5</span>
</p>
  <p>
  <span class="crayon-c"># Default-Stop:      0 1 6</span>
</p>
  <p>
  <span class="crayon-c"># Short-Description: Ensure swap is created and enabled</span>
</p>
  <p>
  <span class="crayon-c"># Description:       Check to see if epemeral disk is mounted. If it is, and swap doesn’t exist,</span>
</p>
  <p>
  <span class="crayon-c">#                    create swap equivalent to memory on the ephemeral drive. If it isn’t, and</span>
</p>
  <p>
  <span class="crayon-c">#                    exist, create 512MB of swap on /.</span>
</p>
  <p>
  <span class="crayon-c">### END INIT INFO</span>
</p>
  <p>
  <span class="crayon-c"># Copyright 2012 Corsis</span>
</p>
  <p>
  <span class="crayon-c">#    <a href="http://www.corsis.com/">http://www.corsis.com/</a></span>
</p>
  <p>
  <span class="crayon-c">#    Licensed under the Apache License, Version 2.0 (the “License”);</span>
</p>
  <p>
  <span class="crayon-c">#    you may not use this file except in compliance with the License.</span>
</p>
  <p>
  <span class="crayon-c">#    You may obtain a copy of the License at</span>
</p>
  <p>
  <span class="crayon-c">#        <a href="http://www.apache.org/licenses/LICENSE-2.0">http://www.apache.org/licenses/LICENSE-2.0</a></span>
</p>
  <p>
  <span class="crayon-c">#    Unless required by applicable law or agreed to in writing, software</span>
</p>
  <p>
  <span class="crayon-c">#    distributed under the License is distributed on an “AS IS” BASIS,</span>
</p>
  <p>
  <span class="crayon-c">#    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.</span>
</p>
  <p>
  <span class="crayon-c">#    See the License for the specific language governing permissions and</span>
</p>
  <p>
  <span class="crayon-c">#    limitations under the License.</span>
</p>
  <p>
  <span class="crayon-v">DEFAULTSIZE</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">4194304</span>
</p>
  <p>
  <span class="crayon-e">start</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c"># Check to see if there is swap mounted right now</span>
</p>
  <p>
  <span class="crayon-c"># If there is, we’re done here</span>
</p>
  <p>
  <span class="crayon-v">ISSWAP</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;bin&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-r"&gt;cat&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;proc&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;meminfo&lt;/span&gt;&lt;span class="crayon-o"&gt;|&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;bin&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-r"&gt;grep&lt;/span&gt;&lt;span class="crayon-v"&gt;SwapTotal&lt;/span&gt;&lt;span class="crayon-o"&gt;|&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;usr&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;bin&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-r"&gt;awk&lt;/span&gt;&lt;span class="crayon-s"&gt;‘{print $2}’&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">$ISSWAP</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">ne</span>
  <span class="crayon-cn">0</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-st">then</span>
</p>
  <p>
  <span class="crayon-r">exit</span>
  <span class="crayon-cn">0</span>
</p>
  <p>
  <span class="crayon-c"># What OS are we running?</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">f</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">etc</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">system</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">release</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">o</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">f</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">etc</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">redhat</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">release</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-st">then</span>
</p>
  <p>
  <span class="crayon-v">OSTYPE</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“amzn”</span>
</p>
  <p>
  <span class="crayon-st">elif</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">f</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">etc</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">lsb</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">release</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-st">then</span>
</p>
  <p>
  <span class="crayon-v">OSTYPE</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“ubuntu”</span>
</p>
  <p>
  <span class="crayon-c"># Set the target directory. If unsupported platform, use root</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-s">“$OSTYPE”</span>
  <span class="crayon-st">in</span>
</p>
  <p>
  <span class="crayon-v">amzn</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">TARGET</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/media/ephemeral0”</span>
</p>
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">TARGET</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/mnt”</span>
</p>
  <p>
  <span class="crayon-v">TARGET</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/”</span>
</p>
  <p>
  <span class="crayon-st">esac</span>
</p>
  <p>
  <span class="crayon-c"># Does a swapfile already exist? If so, activate and be done</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">f</span>
  <span class="crayon-v">$TARGET</span>
  <span class="crayon-o">/</span>
  <span class="crayon-i">swapfile00</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-st">then</span>
</p>
  <p>
  <span class="crayon-o">/</span>
  <span class="crayon-v">sbin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-i">swapon</span>
  <span class="crayon-v">$TARGET</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">swapfile00</span>
</p>
  <p>
  <span class="crayon-r">exit</span>
  <span class="crayon-cn">0</span>
</p>
  <p>
  <span class="crayon-c"># OK, so there’s no existing swapfile. Let’s make one and activate it.</span>
</p>
  <p>
  <span class="crayon-c"># If we’re on an unsupported OS, or ephemeral disk isn’t mounted, use a safe</span>
</p>
  <p>
  <span class="crayon-c"># default size. Otherwise, use RAM size</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">$TARGET</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/”</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-st">then</span>
</p>
  <p>
  <span class="crayon-v">SIZE</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">$DEFAULTSIZE</span>
</p>
  <p>
  <span class="crayon-st">else</span>
</p>
  <p>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">mount</span>
  <span class="crayon-o">|</span>
  <span class="crayon-r">grep</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">q</span>
  <span class="crayon-s">" on $TARGET type "</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">?</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">eq</span>
  <span class="crayon-cn">0</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-st">then</span>
</p>
  <p>
  <span class="crayon-v">SIZE</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;bin&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-r"&gt;cat&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;proc&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;meminfo&lt;/span&gt;&lt;span class="crayon-o"&gt;|&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;bin&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-r"&gt;grep&lt;/span&gt;&lt;span class="crayon-s"&gt;“^MemTotal”&lt;/span&gt;&lt;span class="crayon-o"&gt;|&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;usr&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-v"&gt;bin&lt;/span&gt;&lt;span class="crayon-o"&gt;/&lt;/span&gt;&lt;span class="crayon-r"&gt;awk&lt;/span&gt;&lt;span class="crayon-s"&gt;‘{print $2}’&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-st">else</span>
</p>
  <p>
  <span class="crayon-v">SIZE</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">$DEFAULTSIZE</span>
</p>
  <p>
  <span class="crayon-v">TARGET</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“/”</span>
</p>
  <p>
  <span class="crayon-c"># OK, time to get down to business.</span>
</p>
  <p>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-r">dd</span>
  <span class="crayon-st">if</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">dev</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">zero </span>
  <span class="crayon-v">of</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">$TARGET</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">swapfile00 </span>
  <span class="crayon-v">bs</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1024</span>
  <span class="crayon-v">count</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">$SIZE</span>
</p>
  <p>
  <span class="crayon-o">/</span>
  <span class="crayon-v">sbin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-i">mkswap</span>
  <span class="crayon-v">$TARGET</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">swapfile00</span>
</p>
  <p>
  <span class="crayon-o">/</span>
  <span class="crayon-v">sbin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-i">swapon</span>
  <span class="crayon-v">$TARGET</span>
  <span class="crayon-o">/</span>
  <span class="crayon-i">swapfile00</span>
</p>
  <p>
  <span class="crayon-e">stop</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">exit</span>
  <span class="crayon-cn">0</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-s">“$1”</span>
  <span class="crayon-st">in</span>
</p>
  <p>
  <span class="crayon-v">start</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">stop</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-r">echo</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-s">“Usage: $0 {start|stop}”</span>
</p>
  <p>
  <span class="crayon-r">exit</span>
  <span class="crayon-cn">2</span>
</p>
  <p>
  <span class="crayon-st">esac</span>
</p>
  <p>
  <span class="crayon-r">exit</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">?</span>
</p>
</div>
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### The DeployWorkstation Play

Now, we'll create a play that calls these tasks in the right order to provision our workstation and configure it. This file will be created in the root of your playbook, and I named it deployworkstation.yml.

<span class="crayon-title">The Deploy Workstation Play</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-o">—</span>
</p>
  <p>
  <span class="crayon-c"># deployworkstation.yml</span>
</p>
  <p>
  <span class="crayon-s">- hosts</span>
  <span>: localhost</span>
</p>
  <p>
  <span class="crayon-s">connection</span>
  <span>: local</span>
</p>
  <p>
  <span class="crayon-s">gather<em>facts</em></span>
  <em><span>: no</span></em>
</p>
  <em>
  <p><span class="crayon-s">roles</span><span>:</span></p>
  <p><span class="crayon-s">- role</span><span>: launch</span></p>
</em>
  <p><em><span class="crayon-s">name</span><span>: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>group<span class="crayon-sy">
</span></em>name<span class="crayon-o">}</span><span class="crayon-o">}</span>amibuild<span class="crayon-i">"</span></p>
  <p><span class="crayon-i">  - hosts: "</span><span class="crayon-o">{</span><span class="crayon-o">{</span>group<span class="crayon-sy">_</span>name<span class="crayon-o">}</span><span class="crayon-o">}</span>amibuild"</p>
  <p>
  <span class="crayon-s">become</span>
  <span>: True</span>
</p>
  <p>
  <span class="crayon-s">become_user</span>
  <span>: root</span>
</p>
  <p>
  <span class="crayon-s">become_method</span>
  <span>: sudo</span>
</p>
  <p>
  <span class="crayon-s">roles</span>
  <span>:</span>
</p>
  <p>-yaegashi<span class="crayon-o">.</span>blockinfile</p>
  <p>-workstation</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Testing our Playbook

To test our work so far, we simply need to execute it with Ansible and see if we are successful:<br>
`ansible-playbook -vv -e group_name=test deployworkstation.yml`<br>
You should see some output at the end of the playbook run like this:<br>

[<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.16.05-PM-300x26.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.16.05-PM-768x67.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.16.05-PM-1024x89.png%201024w" class="alignnone wp-image-182 size-large" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.16.05-PM.png)

Next, connect to your instance with ssh by typing the following command:<br>
`ssh ubuntu@ec2-52-90-220-60.compute-1.amazonaws.com`<br>
(hint: copy/paste the hostname above)

You should see something like the following after you connect with ssh:<br>

[<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.17.01-PM-300x187.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.17.01-PM-768x478.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.17.01-PM-1024x637.png%201024w" class="alignnone wp-image-183 size-large" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.17.01-PM.png)

That's it! You've now created a workstation in the Amazon public cloud. Be sure to terminate the instance you've created so that you don't incur any unexpected fees. You can do this by navigating to the EC2 (top left) dashboard from the AWS console, then selecting any running instances and choosing to terminate them:<br>

[<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.32-PM-300x100.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.32-PM-768x256.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.32-PM-1024x341.png%201024w" class="alignnone wp-image-184 size-large" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.32-PM.png)

After selecting to Terminate them from the Instance State menu, you'll need to confirm it:<br>

[<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.52-PM-300x132.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.52-PM-768x339.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.52-PM-1024x452.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.52-PM.png%201532w" class="alignnone wp-image-185 size-large" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-10.18.52-PM.png)

Now that you've terminated any running instances, in the next part, we'll learn how to create snapshots, launch configurations, and auto-scaling groups from our immutable golden master images.
