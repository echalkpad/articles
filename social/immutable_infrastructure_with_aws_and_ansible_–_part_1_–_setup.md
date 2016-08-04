# Immutable Infrastructure with AWS and Ansible – Part 1 – Setup

[Original URL](http://vcdxpert.com/?p=105)

> Immutable infrastructure is a very powerful concept that brings stability, efficiency, and fidelity to your applications through automation and the use of successful patterns from programming....

Immutable infrastructure is a very powerful concept that brings stability, efficiency, and fidelity to your applications through automation and the use of successful patterns from programming. The general idea is that you never make changes to running infrastructure. Instead, you ensure that all infrastructure is created through automation, and to make a change, you simply create a new version of the infrastructure, and destroy the old one. Chad Fowler was one of the first to [mention this concept on his blog](http://chadfowler.com/blog/2013/06/23/immutable-deployments/), and I believe it resonates with anyone that has spent a significant amount of time doing system administration:

## "Why? Because an old system inevitably grows warts..."

_They start as one-time hacks during outages. A quick edit to a config file saves the day. "We'll put it back into Chef later," we say, as we finally head off to sleep after a marathon fire fighting session._

_Cron jobs spring up in unexpected places, running obscure but critical functions that only one person knows about. Application code is deployed outside of the normal straight-from-source-control process._

_The system becomes finicky. It only accepts deploys in a certain manual way. The init scripts no longer work unless you do something special and unexpected._

_And, of course the operating system has been patched again and again (in the best case) in line with the standard operating procedures, and the inevitable entropy sets in. Or, worse, it has never been patched and now you're too afraid of what would happen if you try._

_The system becomes a house of cards. You fear any change and you fear replacing it since you don't know everything about how it works._ -- Chad Fowler – [Trash Your Servers and Burn Your Code: Immutable Infrastructure and Disposable Components](http://chadfowler.com/blog/2013/06/23/immutable-deployments/)

## Requirements

To begin performing immutable infrastructure provisioning, you'll need a few things first. You need some type of "cloud" infrastructure. This doesn't necessarily mean you need a virtual server somewhere in the cloud; what you really need is the ability to provision cloud infrastructure with an API. A permanent virtual server running in the cloud is the very opposite of immutable, as it will inevitably grow the warts Chad mentions above.

### Amazon Web Services

For this series, we'll use Amazon Web Services as our cloud provider. Their APIs and services are frankly light years ahead of the competition. I'm sure you could provision immutable infrastructure on other public cloud providers, but it wouldn't be as easy, and you might not have access to the wealth of features and services available that can make your infrastructure provisioning non-disruptive with zero downtime. If you've never used AWS before, the good news is that you can get access to a ["free" tier](https://aws.amazon.com/free/) that gives you limited amounts of compute resources per month for 12 months. 750 hours a month of t2.micro instance usage should be plenty if you are just learning AWS in your free time, but please be aware that if you aren't careful, you can incur additional charges that aren't covered in your "free" tier.

### Ansible

The second thing we'll need is an automation framework that allows us to treat infrastructure as code. Ansible has taken the world by storm due to it's simplicity and rich ecosystem of modules that are available to talk directly to infrastructure. There is a [huge library of Ansible modules for provisioning cloud infrastructure](http://docs.ansible.com/ansible/list_of_cloud_modules.html). The AWS specific modules cover almost every AWS service imaginable, and far exceed those available from other infrastructure as code tools like Chef and Puppet.

### OS X or Linux

The third thing we'll need is an OS X or Linux workstation to do the provisioning from. As we get into the more advanced sections, I'll demonstrate how to provision a dedicated orchestrator that can perform provisioning operations on your behalf, but in the short-term, you'll need a UNIX-like operating system to run things from. If you're running Windows, you can download [VirtualBox from Oracle](https://www.virtualbox.org/), and [Ubuntu Linux from Canonical](http://www.ubuntu.com/download/desktop), then install Ubuntu Linux in a VM. The following steps will get your workstation setup properly to begin provisioning infrastructure in AWS:

#### Mac OS X Setup

1. Install Homebrew by executing the following command:<br>
  `ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`<br>
  You should see output like the following:

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.05-AM-768x442.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.05-AM-1024x589.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.05-AM.png%201188w" class="alignnone size-large wp-image-106" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.05-AM.png)

2. Install Ansible with Homebrew by executing the following command:<br>
  `brew install ansible`<br>
  You should see output like the following: (note, I'm actually running Ansible 2.0.0.2 now, but this output was for an older version; use Ansible 2.0+ as it's the future ![:)](http://vcdxpert.com/wp-includes/images/smilies/simple-smile.png)<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.32-AM-300x269.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.32-AM-768x688.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.32-AM-1024x917.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.32-AM.png%201059w" class="alignnone size-large wp-image-107" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.14.32-AM.png)

3. Install the AWS Command Line Interface (CLI) with Homebrew by executing the following command:<br>
  `brew install awscli`<br>
  You should see output like the following:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-2.15.32-PM-300x162.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-2.15.32-PM-768x414.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-2.15.32-PM-1024x551.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-2.15.32-PM.png%201025w" class="alignnone size-large wp-image-109" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-2.15.32-PM.png)

4. Install wget through homebrew by executing the following command:<br>
  `brew install wget`<br>
  You should see output like the following:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.56.37-AM-300x33.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.56.37-AM-768x85.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.56.37-AM-1024x113.png%201024w" class="alignnone size-large wp-image-171" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.56.37-AM.png)

#### Linux Setup

1. Install Ansible by executing the following command:<br>
  `sudo pip install ansible`
2. Install the AWS Command Line Interface (CLI) by executing the following command:<br>
  `sudo pip install awscli`
3. Install wget using your package manager.

#### Generic Workstation Setup

These steps need to be followed whether you're running a Mac or Linux for your workstation.

1. Install a good text editor. My favorite is [Sublime Text 2](https://www.sublimetext.com/), but you can use whatever you want.
2. Install the [yaegashi.blockinfile Ansible role](https://github.com/yaegashi/ansible-role-blockinfile) from Ansible galaxy. This is a very useful role that will allow us to add blocks of text to configuration files, rather than simply changing single lines. Type the following command to install it:<br>
  `sudo ansible-galaxy install yaegashi.blockinfile`<br>
  You should see output like the following:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.24.55-AM-300x35.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.24.55-AM-768x90.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.24.55-AM-1024x120.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.24.55-AM.png%201143w" class="alignnone size-large wp-image-108" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-11.24.55-AM.png)

## Amazon Setup

There are a few things you'll need to begin provisioning infrastructure in your AWS account. First, you'll need to make sure the default security group in your VPC allows traffic from your workstation. This is necessary because Ansible will configure your EC2 compute instances over SSH, and needs network connectivity to them from your workstation.

1. Login to your AWS Console and select VPC from the bottom left of the dashboard.
2. Click on Security Groups on the bottom left hand side under Security.
3. Select/highlight the security group named "default", and select the Inbound Rules tab. Click the Edit button, then click Add another rule, and for the rule type, select "ALL Traffic", and insert your workstation's Internet IP address, with a /32 at the end to indicate the CIDR netmask. If you don't know your workstation's true Internet IP address, you can find it at [this website](https://www.whatismyip.com/).<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-3.34.08-PM-300x133.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-3.34.08-PM-768x341.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-3.34.08-PM-1024x455.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-3.34.08-PM.png%201222w" class="alignnone size-large wp-image-118" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-08-at-3.34.08-PM.png)

  <br>
  Note: I blanked my IP address in the image above.
4. Click Save to Save the Inbound Rules.
5. Go back to the AWS Console dashboard, and click "Identity & Access Management." It is located towards the middle of the second column, under Security & Identity.
6. Click on Users on the left, then click "Create New Users." Enter a username for yourself, and leave the checkbox selected to Generate an access key for each user. Click the Create button:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.04-PM-300x106.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.04-PM-768x271.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.04-PM-1024x361.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.04-PM.png%201145w" class="alignnone size-large wp-image-125" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.04-PM.png)

7. Your AWS credentials will be shown on the next screen. It's important to save these credentials, as they will not be shown again:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.31-PM-300x87.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.31-PM-768x224.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.31-PM.png%20871w" class="alignnone size-full wp-image-126" width="871">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.49.31-PM.png)

8. Using your text editor, edit a file named ~/.boto, which should include the credentials you were just given, in the following format:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-i">[Credentials]</span>
  </p>
    <p>aws<span class="crayon-sy">
    <em>
  </em>
  </span><em>access<span class="crayon-sy">
  </span></em>key<span class="crayon-sy">
    <em>
  </em>
  </span><em>id=<span class="crayon-sy">(</span>accesskey<span class="crayon-sy">)</span></em></p>
    <em>
  </em>
    <p><em>aws<span class="crayon-sy">
  </span></em>secret<span class="crayon-sy">
    <em>
  </em>
  </span><em>access<span class="crayon-sy">
  </span></em>key=<span class="crayon-sy">(</span>secretaccesskey<span class="crayon-sy">)</span></p>
  </div>
   | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

9. At the command line, execute the following command, and input the same AWS credentials, along with the AWS region you are using:<br>
  `aws configure`<br>
  For most of you, this will be either "us-east-1" or "us-west-1". If you're not in the US, use [this page](http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region) to determine what your EC2 region is.

10. Click on Groups, then click "Create New Group":<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.18-PM-300x118.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.18-PM-768x303.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.18-PM-1024x404.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.18-PM.png%201251w" class="alignnone size-large wp-image-122" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.18-PM.png)

11. Name the group PowerUsers, then click Next:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.35-PM-300x101.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.35-PM.png%20724w" class="alignnone size-full wp-image-123" width="724">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-5.59.35-PM.png)

12. In the Attach Policy step, search for "PowerUser" in the filter field, and check the box next to "PowerUserAccess", then click "Attach Policy":<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.09-PM-300x123.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.09-PM-768x314.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.09-PM-1024x419.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.09-PM.png%201236w" class="alignnone size-large wp-image-124" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.09-PM.png)

13. Click Next to Review, and save your group.
14. Select/Highlight the PowerUsers group you've just created, and click Actions, then "Add Users to Group":<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.41-PM-300x59.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.41-PM-768x150.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.41-PM-1024x200.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.41-PM.png%201249w" class="alignnone size-large wp-image-129" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.41-PM.png)

15. Select the user account you just created, and add that user to the group:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.59-PM-300x62.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.59-PM-768x158.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.59-PM-1024x211.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.59-PM.png%201248w" class="alignnone size-large wp-image-130" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.00.59-PM.png)

16. Now, we'll need to create an IAM policy that gives zero access to any of our resources. The reason for this is that we'll be provisioning EC2 instances with an IAM policy attached, and if those instances get compromised, we don't want them to have permission to make any changes to our AWS account. Click Policies on the left hand side (still under Identity & Access Management), then click Get Started:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.26-PM-300x171.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.26-PM-768x437.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.26-PM-1024x583.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.26-PM.png%201243w" class="alignnone size-large wp-image-131" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.26-PM.png)

17. Click Create Policy:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.39-PM-300x114.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.39-PM-768x292.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.39-PM-1024x390.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.39-PM.png%201264w" class="alignnone size-large wp-image-132" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.06.39-PM.png)

18. Select "Create Your Own Policy" from the list:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.07.11-PM-300x116.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.07.11-PM-768x297.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.07.11-PM-1024x396.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.07.11-PM.png%201247w" class="alignnone size-large wp-image-133" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-09-at-6.07.11-PM.png)

19. Give the policy a name, "noaccess", and a description, then paste the following code into the policy document:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-i">“Version”</span>
    <span>: “2012-10-17”</span>
    <span class="crayon-o">,</span>
  </p>
    <p>
    <span class="crayon-i">“Statement”</span>
    <span>: [</span>
  </p>
    <p>
    <span class="crayon-i">“Action”</span>
    <span>: “<em>”</em></span>
    <em><span class="crayon-o">,</span></em>
  </p>
    <em>
    <p><span class="crayon-i">“Effect”</span><span>: “Deny”</span><span class="crayon-o">,</span></p>
  </em>
    <p><em><span class="crayon-i">“Resource”</span><span>: “</span></em>”</p>
  </div>
   | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

20. Click Validate Policy at the bottom. It should show "This policy is valid," as you see below:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-10-at-7.27.10-AM-300x142.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-10-at-7.27.10-AM-768x363.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-10-at-7.27.10-AM-1024x485.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-10-at-7.27.10-AM.png%201230w" class="alignnone size-large wp-image-134" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-10-at-7.27.10-AM.png)

21. Click Create Policy, then click Roles on the left-hand side of the screen.<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.41-AM-300x135.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.41-AM-768x345.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.41-AM-1024x460.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.41-AM.png%201228w" class="alignnone size-large wp-image-140" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.41-AM.png)

22. Click Create New Role, then type in a role name, "noaccess":<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.54-AM-300x95.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.54-AM-768x244.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.54-AM-1024x325.png%201024w" class="alignnone size-large wp-image-141" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.01.54-AM.png)

23. Under the Select Role Type screen, select "Amazon EC2":<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.06-AM-300x133.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.06-AM-768x341.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.06-AM-1024x455.png%201024w" class="alignnone size-large wp-image-142" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.06-AM.png)

24. On the Attach Policy screen, filter for the "noaccess" policy we just created, and check the box next to it to select it:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.22-AM-300x76.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.22-AM-768x193.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.22-AM-1024x258.png%201024w" class="alignnone size-large wp-image-143" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.22-AM.png)

25. On the Review screen, click the Create Role button at the bottom right:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.33-AM-300x101.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.33-AM-768x258.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.33-AM-1024x344.png%201024w" class="alignnone size-large wp-image-144" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-9.02.33-AM.png)

26. Now, go back to the main screen of the AWS console, and click EC2 in the top left.
27. Click "Key Pairs" under the Security section on the left:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.35-PM-300x199.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.35-PM-768x509.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.35-PM-1024x679.png%201024w" class="alignnone size-large wp-image-150" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.35-PM.png)

28. Click "Create Key Pair", then give the Key Pair a name:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.56-PM-300x116.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.56-PM-768x296.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.56-PM.png%20986w" class="alignnone size-full wp-image-151" width="986">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.38.56-PM.png)

29. The private key will now be downloaded by your browser. Save this key in a safe place, like your ~/.ssh folder, and make sure it can't be read by other users by changing the mode on it:<br>
  `mv immutable.pem ~/.ssh chmod 600 ~/.ssh/immutable.pem`
30. Run ssh-agent, and add the private key to it, by executing the following commands:<br>
  `eval ssh-agent`<br>
  ssh-add ~/.ssh/immutable.pem<br>
  You should see output like the following:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.49.16-PM-300x31.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.49.16-PM-768x79.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.49.16-PM-1024x106.png%201024w" class="alignnone size-large wp-image-152" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-1.49.16-PM.png)

31. Next, install pip using the following command:<br>
  `sudo easy_install pip`<br>
  You should see output like the following:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.52.01-AM-300x161.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.52.01-AM-768x413.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.52.01-AM-1024x550.png%201024w" class="alignnone size-large wp-image-169" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.52.01-AM.png)

32. Then, install boto using the following command:<br>
  `sudo pip install boto`<br>
  You should see output like the following:<br>

  [<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.54.04-AM-300x50.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.54.04-AM-768x127.png%20768w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.54.04-AM-1024x170.png%201024w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.54.04-AM.png%201350w" class="alignnone size-large wp-image-170" width="650">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-22-at-11.54.04-AM.png)

The setup of your environment is now complete. To test and ensure you can communicate with the AWS EC2 API, execute the following command:<br>
`aws ec2 describe-instances`

You should see output like the following:<br>

[<embed src="http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-10.00.17-AM-300x55.png%20300w,%20http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-10.00.17-AM.png%20529w" class="alignnone size-full wp-image-146" width="529">](http://vcdxpert.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-12-at-10.00.17-AM.png)

In the next article, we'll begin setting up our Ansible playbook and provisioning a test system.
