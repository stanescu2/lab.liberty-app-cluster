lab.liberty-app-cluster
=======================
\

IMPACT 2014

![image](Session_1377_IMPACT2014-Lab-instructions_html_m3e695f67.jpg)\

**April 27 - May 1 Las Vegas, NV**

\

\

\

\

**Session 1377**

**\
Build and Manage A WebSphere\
Liberty Application Cluster\
including the new Admin Center**

\

**Lab Instructions**

\

\

\

\

\

\

\

\

\

\

\

\

\

\

Authors:

Michael C Thompson, WebSphere Developer,
[mcthomps@us.ibm.com](mailto:mcthomps@us.ibm.com)

Chris Vignola, WebSphere Architecture,
[cvignola@us.ibm.com](mailto:cvignola@us.ibm.com)

\

\

**Key Reference Notes for Lab 1377**

**Passwords and resources**

\

Password information

VMWare

User: root

Password: web1sphere

Naming conventions

Liberty installation

/opt/wlp

Lab materials

/opt/lab-materials

Lab Instructions (soft-copy)

/opt/lab-materials/\
Build\_Liberty\_Cluster\_Lab\_Instructions.pdf

\

\

**TABLE OF CONTENTS**

**[1 Objective 4](#_toc267)**

**[2 Prerequisite Knowledge Prerequisite Knowledge](#_toc279)**

**[4](#_toc284)**

[3 Step-by-Step Instructions 5](#_toc288)

[3.1 Introduction to Liberty 5](#_toc289)

[3.2 Create a collective 10](#_toc359)

[3.3 Perform collective operations via scripting 19](#_toc565)

[3.4 Operations through the Admin Center 22](#_toc641)

[3.5 Create a cluster 31](#_toc705)

[3.6 Deploying applications to the cluster 41](#_toc879)

[3.7 Deploying server packages via Admin Center 51](#_toc983)

\

\

1Objective
==========

In this hands-on lab, you build a real Liberty application cluster and
manage it using the Liberty Admin Center, the web-based administrative
interface. Both application clusters and the Admin Center are new
capabilities in the IBM WebSphere Application Server V8.5.5 release.
Learn what a Liberty collective is, how clusters are defined, and how to
operate and manage clusters through both the command line and the Admin
Center and its tools.

\

In the lab, attendees set up a collective, create a cluster, deploy and
verify applications on the cluster, and perform basic operational tasks
on the cluster. After completing this lab, participants are fully
equipped to set up and operate their own production Liberty application
clusters and manage them through the command line or the new Admin
Center.

\

In this lab, you learn:

-   ~~~~ {.western lang="en-US"}
The concepts and operations of a Liberty collective and clustering with the WebSphere Application Server Liberty profile
~~~~

-   ~~~~ {.western lang="en-US"}
Hands-on experience creating, configuring and performing operations on a collective and cluster
~~~~

-   ~~~~ {.western lang="en-US"}
Hands-on experience with the Jython scripting support
~~~~

-   ~~~~ {.western lang="en-US"}
Hands-on experience with the Admin Center, the new web-based administrative interface
~~~~

\

\

2Prerequisite Knowledge
=======================

-   Basic Linux knowledge\
    \

-   This lab uses gedit as the editor of choice in the command
    examples.\
    You are free to use any editor you wish (the VM image has vi and
    emacs available).

\

\
When large amounts of text are displayed as part of an operation output
or screen shot, the important portions are highlighted or otherwise
indicated using red.

\

\

3Step-by-Step Instructions
==========================

3.1Introduction to Liberty
--------------------------

\

****Approximate time: 5 - 10 minutes \
\
\

These steps take you through the most basic operations supported by the
Liberty profile. This entails starting the default server, deploying an
application and dynamically changing the server's configuration.

\

The Liberty profile is designed to provide a world-class application
infrastructure platform as well as a compelling developer experience. \
\
\

3.1.1 Create and start the default server. \
\
 Open a terminal (right-click desktop and select Open in Terminal) and \
 execute the following commands to create and start the default server:

\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
# cd /opt/wlp
# bin/server create# bin/server start
~~~~

\

\

\

[root@localhost \~]\# cd /opt/wlp

\

[root@localhost wlp]\# bin/server create

Server defaultServer created.

\

[root@localhost wlp]\# bin/server start

Starting server defaultServer.

Server defaultServer started with process ID 6336.

\

\
\

\

3.1.2 Deploy the sample application "snoop" by adding the war file to
dropins.\
\
 An application archive placed in the dropins directory will be
automatically\
 deployed and started. Liberty supports two ways to deploy applications:
via \
 the dropins directory demonstrated here, as well as through
configuration.\
 A configuration based deployment will be used later in this lab.

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
# cp /opt/lab-materials/applications/snoop.war   usr/servers/defaultServer/dropins
~~~~

\
\

\
3.1.3 Access the sample application "snoop".

\

Start Firefox, go to the URL
[http://localhost:9080/snoop](http://localhost:9080/snoop)

\

\

You should see a page similar to the following screenshot:

\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_48dcb6f8.png)\
\
 Close the browser.

\
3.1.4 Change the default HTTP port for the server.\
\
 The Liberty profile responds to configuration changes dynamically,\
 no restart is required!\
\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
# gedit usr/servers/defaultServer/server.xml
~~~~

\
Change the HTTP port from 9080 to 8080:

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
 <httpEndpoint id="defaultHttpEndpoint" httpPort="8080" />
~~~~

\

Save and close the file.

\
3.1.5 Use the new port to access the application.\
\

Start Firefox, go to URL
[http://localhost:8080/snoop](http://localhost:8080/snoop)

\

\

You should see a page similar to the following screenshot:

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_44ee6ac5.png)\
\

\
 Close the browser.

\
3.1.6 View the console.log to see messages for the web application.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# gedit usr/servers/defaultServer/logs/console.log

~~~~

Close the file.

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
~~~~

3.1.7 Stop the default server.\
\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
# bin/server stop



[root@localhost wlp]# bin/server stop
Stopping server defaultServer.
Server defaultServer stopped.
~~~~

\

\
You now have experience with basic server operations, configuration and\
deploying an application!

**3.2Create a collective**
--------------------------

\

Approximate time: 10 - 20 minutes \
\
These steps take you through creating and configuring a basic
collective. A collective is the set of Liberty servers in a single
administrative domain. A collective consists of at least one "collective
controller", a server with the collectiveController-1.0 feature enabled.
Optionally, a collective may have many "collective members", servers
with the collectiveMember-1.0 feature enabled. A collective may be
configured to have many collective controllers, called a replica set.
Configuration of the replica set is not covered in this lab, but
documentation is available from wasdev.net.

\

The following illustration shows a sample collective topology with a
replicate set of 3 controllers and 5 collective members.\
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m4841a3d9.png)\
\

\

In this section, a simple one controller and one member collective will
be created.

\

\

3.2.1 Create a server which will be the collective controller.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server create controller1



[root@localhost wlp]# bin/server create controller1
Server controller1 created.
~~~~

\
\

\
3.2.2 Create the collective controller configuration.\
\

This will establish the administrative domain security configuration. \
 The servers in the collective communicate with each other using signed\
 SSL certificates. The 'collective create' command establishes the
initial\
 set of SSL keys.\
\
 The Liberty profile does not ship with any default passwords. As such,
the\
 the create command requires a keystore password. In this lab, all
keystore\
 passwords will be **'Impact2014'**. Each keystore password can be
different,\
 but to keep the lab simple all passwords will be the same.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/collective create controller1 --keystorePassword=Impact2014 --createConfigFile



[root@localhost wlp]# bin/collective create controller1 --keystorePassword=Impact2014 --createConfigFile
Creating required certificates to establish a collective...
This may take a while.
Successfully generated the controller root certificate.
Successfully generated the member root certificate.
Successfully generated the server identity certificate.
Successfully generated the HTTPS certificate.

Successfully set up collective controller configuration for controller1

Add the following lines to the server.xml to enable:

    <include location="${server.config.dir}/collective-create-include.xml" />


Please ensure administrative security is configured for the server.
An administrative user is required to join members to the collective.
~~~~

\

\
\

\
3.2.3 Update the server.xml to include the generated XML file.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# gedit usr/servers/controller1/server.xml
~~~~

\
Add the include from the create operation:

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
<include location="${server.config.dir}/collective-create-include.xml" />
~~~~

\

\

Save and close the file.

\

\

\

\
3.2.4 Update collective-create-include.xml.

\
 The Liberty profile does not ship with any default users. Therefore,
the\
 administrator user name and password must be specified. For the\
 purposes of this lab, use the user name **'admin'** and the password\
 **'adminpwd'** to configure the <quickStartSecurity\> element, which\
 will establish the administrator user.

\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
# gedit usr/servers/controller1/collective-create-include.xml
~~~~

\
Set the user and password to admin / adminpwd:

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
<quickStartSecurity userName="admin" userPassword="adminpwd" />
~~~~

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m706fd666.png)\
\

\

Save and close the file.

\
3.2.5 Start the collective controller.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server start controller1


[root@localhost wlp]# bin/server start controller1
Starting server controller1.
Server controller1 started with process ID 9099.

~~~~

\

3.2.6 Verify the server started correctly and is ready to receive
members.\
\

Look for the following message:

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
CWWKX9003I: CollectiveRegistration MBean is available.
~~~~

The CWWKX9003I message indicates that the collective controller is ready
to receive connections from clients or members.

\

To quickly find the message, search for it using 'grep':

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# grep CWWKX9003I usr/servers/controller1/logs/messages.log
~~~~

\

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# grep CWWKX9003I usr/servers/controller1/logs/messages.log
[3/18/14 13:43:38:424 EDT] 00000015 llective.repository.internal.CollectiveRegistrationMBeanImpl I CWWKX9003I: CollectiveRegistration MBean is available.
~~~~

\

\

\

3.2.7 Create a member to join to the collective.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server create member1


[root@localhost wlp]# bin/server create member1
Server member1 created.
~~~~

\

\

\

\
3.2.8 Join member1 to the collective.\
\
 This will exchange the SSL certificates necessary for the server to
become\
 a collective member. Liberty does not have a client-side SSL
configuration,\
 so you will need to accept the SSL certificate presented by the
controller.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/collective join member1 --host=localhost --port=9443 --user=admin --password=adminpwd --keystorePassword=Impact2014 --createConfigFile
~~~~

\

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# bin/collective join member1 --host=localhost --port=9443 --user=admin --password=adminpwd --keystorePassword=Impact2014 --createConfigFile
Joining the collective with target controller localhost:9443...
This may take a while.

SSL trust has not been established with the target server.

Certificate chain information:
Certificate [0]
Subject DN: CN=localhost, OU=controller1, O=ibm, C=us
Issuer DN: OU=controllerRoot, O=a6291247-6162-45e0-ba14-13f2492c4c43, DC=com.ibm.ws.collective
Serial Number: 8,094,236,004,743
Expires: 3/17/19 1:03 PM
SHA-1 digest: E5:AB:0E:10:EC:36:1F:E2:E0:A9:28:3B:99:31:2E:06:88:A0:3A:01
MD5 digest: 7B:F6:46:A6:9F:F3:9B:F9:62:C8:7C:33:8C:CB:14:5F

Certificate [1]
Subject DN: OU=controllerRoot, O=a6291247-6162-45e0-ba14-13f2492c4c43, DC=com.ibm.ws.collective
Issuer DN: OU=controllerRoot, O=a6291247-6162-45e0-ba14-13f2492c4c43, DC=com.ibm.ws.collective
Serial Number: 8,089,778,095,633
Expires: 3/12/39 12:03 PM
SHA-1 digest: 70:E9:77:87:AF:B0:75:AC:0D:0A:64:87:9C:8A:83:3E:3A:33:97:8F
MD5 digest: BE:E3:34:0F:CC:20:F9:83:0A:B6:62:1F:8E:32:81:3D

Do you want to accept the above certificate chain? (y/n) y (hit Enter)
Successfully completed MBean request to the controller.

Successfully joined the collective for server member1

Add the following lines to the server.xml to enable:

    <include location="${server.config.dir}/collective-join-include.xml" />
~~~~

\
\

\
3.2.9 Update the server.xml for member1.\
\
 Because both the controller and member are running on the same system,\
 the default ports for the member must be changed as the default ports
are\
 already in use by the controller. Update the HTTP and HTTPS ports to be
9081 \
 and 9444.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# gedit usr/servers/member1/server.xml
~~~~

Change the HTTP and HTTPS ports to 9081 and 9444:

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
<httpEndpoint id="defaultHttpEndpoint" httpPort="9081"                                        httpsPort=”9444” />
~~~~

\
Add the include from the join operation:

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
<include location="${server.config.dir}/collective-join-include.xml" />
~~~~

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_5b42c75e.png)\

\

\

Save and close the file.

\

\

\
3.2.10 Start the collective member.

\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server start member1
~~~~

\

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# bin/server start member1
Starting server member1.
Server member1 started with process ID 9559.
~~~~

\

\

\
3.2.11 Verify the member started and is publishing information to the
controller.

\

Look for the following messages:\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
CWWKX8112I: The server's host information was successfully published to the collective repository.
CWWKX8114I: The server's paths were successfully published to the 
collective repository. 
CWWKX8116I: The server STARTED state was successfully published to the collective repository.
~~~~

The CWWKX8112I, CWWKX8114I and CWWKX8116I messages indicate that the
collective member is successfully communicating with the controller.

\

To quickly find the messages, search for them using 'grep':

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# grep CWWKX811[246]I usr/servers/member1/logs/messages.log
~~~~

\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}

[root@localhost wlp]# grep CWWKX811[246]I usr/servers/member1/logs/messages.log
[3/18/14 14:00:42:048 EDT] 00000025 llective.member.internal.publisher.ServerManagementPublisher I CWWKX8112I: The server's host information was successfully published to the collective repository. 
[3/18/14 14:00:42:355 EDT] 00000020 ws.collective.member.internal.publisher.ServerPathsPublisher I CWWKX8114I: The server's paths were successfully published to the collective repository. 
[3/18/14 14:00:42:378 EDT] 00000017 ws.collective.member.internal.publisher.ServerStatePublisher I CWWKX8116I: The server STARTED state was successfully published to the collective repository.
~~~~

\

\

You now have a basic collective topology created. In this topology,
member1 is a collective member and controller1 is a collective
controller.

\

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_4cd59ca4.png)\
\

\

\

\

All collective members publish information about themselves to their
collective controller. This published information is available for query
directly from the controller without need of forwarding the request down
to each collective member.

\

This published information is used by the controller to determine each
member's operational state, and is used by the Admin Center to show
information about the collective. The Admin Center is used in Section
3.4 Operations through the Admin Center.

\
\

\

\

**3.3Perform collective operations via scripting**
--------------------------------------------------

\

Approximate time: 5 – 10 minutes\
\
In this section, you use Jython scripting to perform MBean operations
against the collective controller which allow you to start and stop
registered collective members.\
\
3.3.1 Run the Jython script provided to stop the collective member.\
\
 In order to run the script, set the CLASSPATH and JYTHONPATH
environment \
 variables to include the restConnector.jar and run the provided client
jython\
 script. The script is hard-coded for the host name, paths, user names
and\
 passwords used in these instructions. If you have modified any of
these\
 values, you will need to edit the script.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# export CLASSPATH=/opt/wlp/clients/restConnector.jar# export JYTHONPATH=/opt/wlp/clients/jython/# jython /opt/lab-materials/jython/stopCollectiveMember.py
~~~~

\
\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# jython /opt/lab-materials/jython/stopCollectiveMember.py
Connecting to the server...
Successfully connected to the server "localhost:9443"
Connected to the controller MBean server

Number of registered mbeans: 39

Getting server status
controller1 status: STARTED
member1 status: STARTED

Stopping member1
Stop member1 status:
{returnCode=0, stdout=Stopping server member1.
Server member1 stopped.
, stderr=}

Disconnecting from the MBean server
~~~~

\

\

3.3.2 Verify the server stopped.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server status member1


[root@localhost wlp]# bin/server status member1
Server member1 is not running.
~~~~

\
3.3.3 Start the collective member via scripting.\
\

Copy the stopCollectiveMember.py script to startCollectiveMember.py.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# cp /opt/lab-materials/jython/stopCollectiveMember.py startCollectiveMember.py
~~~~

Modify the startCollectiveMember.py script to invoke the startServer
operation.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# gedit startCollectiveMember.py
~~~~

\
Change 'stop' to 'start':

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
~~~~

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m194400ab.png)\
Save and close the file.

\
\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; page-break-before: always"}
~~~~

Run the modified script.

\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# jython startCollectiveMember.py
~~~~

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# jython startCollectiveMember.py 
Connecting to the server...
Successfully connected to the server "localhost:9443"
Connected to the controller MBean server

Number of registered mbeans: 39

Getting server status
controller1 status: STARTED
member1 status: STOPPED

Starting member1
Start member1 status:
{returnCode=0, stdout=Starting server member1.
Server member1 started with process ID 13050.
, stderr=}

Disconnecting from the MBean server
~~~~

\

\
3.3.4 Verify the server has been started.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server status member1
~~~~

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# bin/server status member1
Server member1 is running with process ID 13050.
~~~~

\
You now have basic Jython experience and have used the MBeans available
on the\
collective controller to perform operations against the registered
collective\
members.\
\

3.4Operations through the Admin Center
--------------------------------------

\

Approximate time: 5 – 10 minutes

\

3.4.1 Enable the Admin Center.\
\
 The Admin Center is a feature, and is therefore not enabled by
default.\
 To enable the Admin Center, add the 'adminCenter-1.0' feature to the
list\
 of enabled features.

\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# gedit usr/servers/controller1/server.xml
~~~~

Add the 'adminCenter-1.0' feature:

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
<feature>adminCenter-1.0</feature>
~~~~

\

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m4ae09542.png)\
\

Save and close the file.

\

\

3.4.2 Log into the Admin Center\
\
 Start Firefox, go to URL
[http://localhost:9080/adminCenter](http://localhost:9080/adminCenter)

\
You will need to accept the server's SSL certificate.\
\
First, click “Add Exception...”

\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_78e31ceb.png)\
\

\

\
\
\

Next, confirm the security exception by clicking “Confirm Security
Exception”.

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_22c745a5.png)\
\

\
\
\

Now log in to the Admin Center with the administrator user name and \
 password ( **'admin'**/ **'adminpwd'** ).\
\
 Enter the user name and password and click “Login”.

\

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m37938f0f.png)\
\

\

\

\

\

3.4.3 The Toolbox\

Each user of the Admin Center has a customized view called 'the
Toolbox'.\
 The Toolbox allows the user to choose the set of tools that they wish
to use. \
\
 By default, the Toolbox is populated with the initial set of tools that
are\
 present in the catalog. The catalog is the set of all tools installed
into the\
 Liberty profile runtime. Users can also add links to commonly used
pages by\
 adding a bookmark.\
\

The Toolbox view:

\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_1996822f.png)\
\
\
\

\

\

3.4.4 Select the Explore tool.\
\
 The Explore tool provides management capabilities for servers,
applications\
 and clusetrs within the collective. The tool's initial view is a
dashboard which\
 displays high-level information about the elements of the topology. \
\
 The Explore Dashboard:

\
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_1c5b9a75.png)\
\

\

3.4.5 Click on the Servers dashboard element.\
\
 Each dashboard element displays the high-level information about
elements\
 in the topology. Clicking on the Servers category on the dashboard
will\
 display individual details about each server.\
\
 From this view, individual or groups of servers can be started or
stopped, and\
 details for a specific server can be seen by clicking on a server.

\
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m32117fe2.png)\
\

\

3.4.6 Stop and restart member1 through the tool.\
\
 Operations can be performed on any server, cluster or application in
the\
 collective via the Admin Center. In this step, stop member1 by
clicking\
 on the green arrow icon in the upper right corner of the member1 card,\
 and select stop.\
\
\

Result of the stop operation:

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m555eb0fb.png)\
\
\
\

\

\

\
 The server can be restarted by repeating the steps and clicking on the
red \
 stopped icon and selecting the start operation. \
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m2d40b3fb.png)\
\

\

\

Result of the start operation:

![image](Session_1377_IMPACT2014-Lab-instructions_html_5f7416dd.png)\
\

\

\
 Be sure to leave member1 running at the end of this section.\
\

Various information and multiple actions are available through the Admin
Center. Not all aspects are examined in this lab.

\

**3.5Create a cluster**
-----------------------

\

Approximate time: 15 - 30 minutes\
\
In this section, you expand the collective by adding a new member,
configure both members to join the default cluster, and finally use
Jython scripting and the Admin Center to perform operations on the
cluster.

\

\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m6a6bc105.png)\
\

\

\

3.5.1 Create a second member to join the collective.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server create member2
~~~~

\

\

\

[root@localhost wlp]\# bin/server create member2

Server member2 created.

\

\

\

\
\

\

3.5.2 Join member2 to the collective.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/collective join member2 --host=localhost --port=9443 --user=admin --password=adminpwd --keystorePassword=Impact2014 --createConfigFile
~~~~

\
\

\

[root@localhost wlp]\# bin/collective join member2 --host=localhost
--port=9443 --user=admin --password=adminpwd
--keystorePassword=Impact2014 --createConfigFile

Joining the collective with target controller localhost:9443...

This may take a while.

\

SSL trust has not been established with the target server.

\

Certificate chain information:

Certificate [0]

Subject DN: CN=localhost, OU=controller1, O=ibm, C=us

Issuer DN: OU=controllerRoot, O=a6291247-6162-45e0-ba14-13f2492c4c43,
DC=com.ibm.ws.collective

Serial Number: 8,094,236,004,743

Expires: 3/17/19 1:03 PM

SHA-1 digest:
E5:AB:0E:10:EC:36:1F:E2:E0:A9:28:3B:99:31:2E:06:88:A0:3A:01

MD5 digest: 7B:F6:46:A6:9F:F3:9B:F9:62:C8:7C:33:8C:CB:14:5F

\

Certificate [1]

Subject DN: OU=controllerRoot, O=a6291247-6162-45e0-ba14-13f2492c4c43,
DC=com.ibm.ws.collective

Issuer DN: OU=controllerRoot, O=a6291247-6162-45e0-ba14-13f2492c4c43,
DC=com.ibm.ws.collective

Serial Number: 8,089,778,095,633

Expires: 3/12/39 12:03 PM

SHA-1 digest:
70:E9:77:87:AF:B0:75:AC:0D:0A:64:87:9C:8A:83:3E:3A:33:97:8F

MD5 digest: BE:E3:34:0F:CC:20:F9:83:0A:B6:62:1F:8E:32:81:3D

\

Do you want to accept the above certificate chain? (y/n) **y (hit
Enter)**

Successfully completed MBean request to the controller.

\

Successfully joined the collective for server member2

\

Add the following lines to the server.xml to enable:

\

<include location="${server.config.dir}/collective-join-include.xml" /\>

\

\

3.5.3 Update the server.xml for member2.\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# gedit usr/servers/member2/server.xml
~~~~

Change the HTTP and HTTPS ports to 9082 and 9445:

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}
<httpEndpoint id="defaultHttpEndpoint" httpPort="9082"                                        httpsPort=”9445” />
~~~~

\
Add an include for the file created by the join operation:

~~~~ {.western lang="en-US"}
    <include location="${server.config.dir}/collective-join-include.xml" />
~~~~

![image](Session_1377_IMPACT2014-Lab-instructions_html_22754e0a.png)\
\

\

Save and close the file.\
\
3.5.4 Start member2.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server start member2
~~~~

\

\

[root@localhost wlp]\# bin/server start member2

Starting server member2.

Server member2 started with process ID 15950.

\

\

3.5.5 Verify the member started and is publishing information to the
controller.

\

'grep' for messages CWWKX8112I, CWWKX8114I and CWWKX8116I:

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# grep CWWKX811[246]I usr/servers/member2/logs/messages.log
~~~~

\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}

[root@localhost wlp]# grep CWWKX811[246]I usr/servers/member2/logs/messages.log
[3/18/14 18:59:33:921 EDT] 00000017 llective.member.internal.publisher.ServerManagementPublisher I CWWKX8112I: The server's host information was successfully published to the collective repository. 
[3/18/14 18:59:35:143 EDT] 00000017 ws.collective.member.internal.publisher.ServerPathsPublisher I CWWKX8114I: The server's paths were successfully published to the collective repository. 
[3/18/14 18:59:35:339 EDT] 00000017 ws.collective.member.internal.publisher.ServerStatePublisher I CWWKX8116I: The server STARTED state was successfully published to the collective repository.
~~~~

\

\

\

3.5.6 Assign member1 and member2 to the default cluster.\
\

The cluster configuration is dynamically updated and published to the\
 collective controller.

\
 Modify each server.xml for the members:

\
 \# gedit usr/servers/member1/server.xml\
\
 \# gedit usr/servers/member2/server.xml\
\
\
 Add the following configuration to each server's server.xml:\
\

\

<featureManager\>\
 <feature\>clusterMember-1.0</feature\>\
 </featureManager\>

\

\

You now have the default cluster created, with member1 and member2
belonging\
to the defaultCluster group. Multiple clusters can be defined with a
single collective, but a server may only belong to one cluster group at
a time.

\

\
3.5.7 Get status for "defaultCluster" via scripting.\
\
 The controller provides the ClusterManagerMBean which defines
operations\
 for obtaining information about defined clusters, starting and stopping
all\
 of the servers in a cluster, and for generating plugin-cfg.xml files.\
\
 The provided getClusterStatus.py script uses various
ClusterManagerMBean \
 operations to determine the defined clusters and the members which
belong \
 to the defaultCluster.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# jython /opt/lab-materials/jython/getClusterStatus.py
~~~~

\

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# jython /opt/lab-materials/jython/getClusterStatus.py
Connecting to the server...
Successfully connected to the server "localhost:9443"
Connected to the controller MBean server

Number of registered mbeans: 39

Defined Clusters:
[defaultCluster]

Cluster members of 'defaultCluster':
[localhost,/opt/wlp/usr,member2, localhost,/opt/wlp/usr,member1]

Status of 'defaultCluster': STARTED

Cluster assignments by server:
controller1 cluster: None
member1 cluster: defaultCluster
member2 cluster: defaultCluster

Disconnecting from the MBean server
~~~~

\

\

\

\
3.5.8 Stop cluster using the stopCluster.py sample script from
wasdev.net\
\
 Multiple sample scripts are available for download on wasdev.net\
 The sample scripts are available in
/opt/lab-materials/jython/sample\_scripts\
\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# export JYTHONPATH=/opt/wlp/clients/jython/:/opt/lab-materials/jython/sample_scripts/lib# jython /opt/lab-materials/jython/sample_scripts/stopCluster.py defaultCluster --host=localhost --port=9443 --user=admin --password=adminpwd --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 

[root@localhost wlp]# jython /opt/lab-materials/jython/sample_scripts/stopCluster.py defaultCluster --host=localhost --port=9443 --user=admin --password=adminpwd --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014
Connecting to the server...
Successfully connected to the server "localhost:9443"
localhost,/opt/wlp/usr,member2 stopped, RC=0
localhost,/opt/wlp/usr,member1 stopped, RC=0
~~~~

\
\
\
3.5.9 Verify the servers in the cluster group have stopped.

\

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
# bin/server status member1
# bin/server status member2
~~~~

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# bin/server status member1
Server member1 is not running.

[root@localhost wlp]# bin/server status member2
Server member2 is not running.
~~~~

\
3.5.10 Start the cluster via the startCluster.py sample script.

\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
# jython /opt/lab-materials/jython/sample_scripts/startCluster.py defaultCluster --host=localhost --port=9443 --user=admin --password=adminpwd --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 
~~~~

\
\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# jython /opt/lab-materials/jython/sample_scripts/startCluster.py defaultCluster --host=localhost --port=9443 --user=admin --password=adminpwd --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014
Connecting to the server...
Successfully connected to the server "localhost:9443"
localhost,/opt/wlp/usr,member2 started, RC=0
localhost,/opt/wlp/usr,member1 started, RC=0
~~~~

\
\
\

The ClusterManager MBean is an example of how the collective controller
acts as an operational repository. Data queries, such as the
listClusterNames, listMembers and getStatus operations are performed
entirely against the operational cache within the collective controller,
while operations which require action on a target such as start and stop
are performed against the respective collective member.\
\

\
\

\
3.5.11 Launch the Explore tool from the Admin Center.\
\
 Now that a cluster is defined, the Explore dashboard will contain a
card\
 which shows information about the defined clusters.

![image](Session_1377_IMPACT2014-Lab-instructions_html_m1dcc7a30.png)\
\

\
3.5.12 Click the Clusters category of the dashboard to see an overview
of the\
 defined clusters.\
\

Click the defaultCluster card to see details about the cluster. \

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_1a27e137.png)\
\

\
3.5.13 The defaultCluster details page shows the details of the
cluster.\
\

No applications are yet deployed to the cluster. Applications will be\
 deployed in the next section.\

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_1d31286.png)\
\

**3.6Deploying applications to the cluster**
--------------------------------------------

\
 Approximate time: 20 – 40 minutes

\

In this section, you build upon the work done in previous sections to
deploy applications and configuration to all members of a cluster. This
section demonstrates the type of compound operations which are possible
with the MBeans available in WebSphere Application Server Liberty
Profile 8.5.5, as well as describes a best practice for cluster
configuration.

\

3.6.1 Update the server.xml for each member to support file transfer.\
\
 By default, servers only support read-only file transfer from their \
 configuration directory. Subsequent steps will use the file transfer
service\
 to deploy applications and new configuration to the cluster.

\

~~~~ {.western lang="en-US"}
 # gedit usr/servers/member1/server.xml
    # gedit usr/servers/member2/server.xml
~~~~

Add the following lines to the server.xml. These lines will enable the\
 file transfer service to write to the server's configuration directory.

~~~~ {.western lang="en-US"}
 <remoteFileAccess>
     <writeDir>${server.config.dir}</writeDir>
 </remoteFileAccess>
~~~~

\
3.6.2 Create a bootstrap.properties file for each cluster member.\
\
 This file will contain the configuration which is unique to each
server, such\
 as port numbers. This will support using a common server.xml for all
of\
 the cluster members.\
\
 Set the contents of each bootstrap.properties as describe below.

\

~~~~ {.western lang="en-US"}
  # gedit usr/servers/member1/bootstrap.properties
http.port=9081
https.port=9444


 # gedit usr/servers/member2/bootstrap.properties
http.port=9082
https.port=9445
~~~~

\

\

\
\
 Save and close the files.

\
3.6.3 Copy the server.xml for member1 and create a new generic
server.xml.\
\
 The generic server.xml will use the new values defined in the\
 bootstrap.properties file. This copy will serve as the basis for the
common\
 cluster member configuration and will use variable substitution for the
server\
 specific configuration values.

\

~~~~ {.western lang="en-US"}
  # cp usr/servers/member1/server.xml clusterServer.xml
 # gedit clusterServer.xml
~~~~

\

Modify the following XML lines to use the new values in
bootstrap.properties:

~~~~ {.western lang="en-US"}
 <httpEndpoint id="defaultHttpEndpoint"
                    httpPort="${http.port}"
                    httpsPort="${https.port}" />
~~~~

\

Save and close the file.

\
3.6.4 Push the application "snoop" to the cluster.\
\
 The deployAppToCluster.py script uses a combination of the
ClusterManager\
 and FileTransfer MBeans to push the application to all of the cluster
members. This operation will push the application to
${server.config.dir}/apps/ for each\
 cluster member.\
\
 This script does not alter the server.xml for the targets. The
server.xml\
 configuration will be updated in the next step.

\

~~~~ {.western lang="en-US"
style="margin-left: 1.24cm; text-align: left; widows: 2; orphans: 2"}
# jython /opt/lab-materials/jython/sample_scripts/deployAppToCluster.py /opt/lab-materials/applications/snoop.war --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd --clusterName=defaultCluster
~~~~

\
\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# jython /opt/lab-materials/jython/sample_scripts/deployAppToCluster.py /opt/lab-materials/applications/snoop.war --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd --clusterName=defaultCluster
Connecting to the server...
Successfully connected to the server "localhost:9443"

Pushing the application to server localhost,/opt/wlp/usr,member2

Pushing the application to server localhost,/opt/wlp/usr,member1
~~~~

\
\
\

\

\
3.6.5 Update the common cluster configuration for the application
"snoop".\
\
 Applications can be explicitly configured in the server.xml using the\
 <application\> stanza.

~~~~ {.western lang="en-US"}
  # gedit clusterServer.xml
~~~~

Add the following lines to the clusterServer.xml:

~~~~ {.western lang="en-US"}
  <application type="war" id="snoop" name="snoop"             location="${server.config.dir}/apps/snoop.war"/>
~~~~

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_784357c5.png)\
\
 Save and close the file.

\
3.6.6 Restart the cluster members.\
\
 This step is required as the bootstrap.properties file is not
dynamically\
 loaded. The bootstrap.properties is processed only during JVM start-up.

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; text-align: left; widows: 2; orphans: 2"}
# jython /opt/lab-materials/jython/sample_scripts/stopCluster.py defaultCluster --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd# jython /opt/lab-materials/jython/sample_scripts/startCluster.py defaultCluster --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd
~~~~

*\
 Command output omitted.\
 See previous steps 3.5.8 and 3.5.10 for the expected output.\
*\

\
\
3.6.7 Push the common cluster configuration to all of the members of the
cluster.\
\
 This step will update the server.xml of all the cluster members. Each
cluster\
 member will dynamically update its runtime configuration after the
file\
 transfer completes.\
\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; text-align: left; widows: 2; orphans: 2"}
# jython /opt/lab-materials/jython/sample_scripts/deployConfigToCluster.py clusterServer.xml --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd --clusterName=defaultCluster

[root@localhost wlp]# jython /opt/lab-materials/jython/sample_scripts/deployConfigToCluster.py clusterServer.xml --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd --clusterName=defaultCluster
Connecting to the server...
Successfully connected to the server "localhost:9443"

Pushing the server.xml to server localhost,/opt/wlp/usr,member2

Pushing the server.xml to server localhost,/opt/wlp/usr,member1
~~~~

\
3.6.8 Access the application "snoop" running on member1 and member2.\
 \
 The application is now available on both members of the cluster.\
 It is out of the scope of this lab, but IBM HTTP Server (IHS) can be
used\
 to perform load balancing across the cluster members.\
\
 Run firefox, go to URLs:
[http://localhost:9081/snoop](http://localhost:9081/snoop)\
 [http://localhost:9082/snoop](http://localhost:9082/snoop)

\
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_492dea5b.png)\
\

\
\
 Close the browser.

\
3.6.9 Install the application "ImpactWeb" to the cluster.\
\
 This operation combines application deploy and configuration file
updates\
 to demonstrate an alternate pattern for installing an application to a\
 cluster.\
\
 This deployment option is limited by the simple application
configuration.\
 For more complex application configurations, it is recommended to
follow\
 steps similar to 3.6.4 and 3.6.7, as those steps provide for the most
flexibility\
 in the application configuration.

~~~~ {.western lang="en-US"
style="margin-left: 1.3cm; text-align: left; widows: 2; orphans: 2"}
# jython /opt/lab-materials/jython/sample_scripts/appInstall.py --install=/opt/lab-materials/applications/ImpactWeb.war  --clusterName=defaultCluster --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd
~~~~

\
\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# jython /opt/lab-materials/jython/sample_scripts/appInstall.py --install=/opt/lab-materials/applications/ImpactWeb.war --clusterName=defaultCluster --truststore=usr/servers/controller1/resources/security/trust.jks --truststorePassword=Impact2014 --host=localhost --port=9443 --user=admin --password=adminpwd
Connecting to the server...
Successfully connected to the server "localhost:9443"
Uploading application ImpactWeb.war to localhost,/opt/wlp/usr,member2
Updating server config for localhost,/opt/wlp/usr,member2
Complete
Uploading application ImpactWeb.war to localhost,/opt/wlp/usr,member1
Updating server config for localhost,/opt/wlp/usr,member1
Complete
~~~~

\
\
\
\
\

\
3.6.10 Access the application "ImpactWeb" running on member1 and
member2.\
\
 Run firefox, go to URLs:
[http://localhost:9081/ImpactWeb/WorkingServlet](http://localhost:9081/ImpactWeb/WorkingServlet)\

[http://localhost:9082/ImpactWeb/WorkingServlet](http://localhost:9082/ImpactWeb/WorkingServlet)[\
](http://localhost:9082/ImpactWeb/WorkingServlet)[\
](http://localhost:9081/snoop)\
\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m5e0dcbe9.png)\
\
 Close the browser.

\
\
\

3.6.11 Launch Explore tool via the Admin Center.\
\
 The dashboard now has information about the defined servers, the
cluster\
 to which they belong, and the applications deployed.[\
](http://localhost:9081/snoop)\
\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m496316ed.png)\
\
 Close the browser.

\
You now have an active cluster group with the "snoop" and "ImpactWeb"
applications\
deployed. The cluster configuration is written in such a way that new
cluster members can be easily added by creating new servers and setting
their bootstrap.properties file accordingly. An alternate choice to
support common configuration is to use include files supported by the
server.xml. The server specific configuration can be stored in a
separate include file, and the common configuration can be stored in the
common server.xml.

\
\

**3.7Deploying server packages via Admin Center**
-------------------------------------------------

\

Approximate time: 10 – 15 minutes\
\
In this section, you use the Admin Center to deploy a server package and
join the deployed server to the collective. A server package can be
deployed to any host that is registered with the collective. The Deploy
tool uses the collective file transfer operations. In order to transfer
a file to a host, the host must be registered with the collective and
the transfer paths must be specified. \
\
3.7.1 Update the localhost configuration to define writable file
transfer paths.

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; text-align: left; widows: 2; orphans: 2"}
# bin/collective updateHost localhost --host=localhost --port=9443 --user=admin --password=adminpwd --rpcUser=root --rpcUserPassword=web1sphere --hostReadPath=/opt --hostWritePath=/opt
~~~~

\
\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# bin/collective updateHost localhost --host=localhost --port=9443 --user=admin --password=adminpwd --rpcUser=root --rpcUserPassword=web1sphere --hostReadPath=/opt --hostWritePath=/opt
Updating the authentication information for the host...

SSL trust has not been established with the target server.

Certificate chain information:
Certificate [0]
Subject DN: CN=localhost, OU=controller1, O=ibm, C=us
Issuer DN: OU=controllerRoot, O=33f0f8ae-136f-4217-a940-e0319865c876, DC=com.ibm.ws.collective
Serial Number: 386,393,443,355
Expires: 4/10/19 9:18 AM
SHA-1 digest: 32:2F:C2:4B:54:70:98:E0:91:7A:38:8E:BD:E8:FB:17:7C:6D:5A:D6
MD5 digest: 39:68:24:6D:2D:7C:1F:3D:0B:6B:12:2B:9A:24:26:EB

Certificate [1]
Subject DN: OU=controllerRoot, O=33f0f8ae-136f-4217-a940-e0319865c876, DC=com.ibm.ws.collective
Issuer DN: OU=controllerRoot, O=33f0f8ae-136f-4217-a940-e0319865c876, DC=com.ibm.ws.collective
Serial Number: 383,670,768,566
Expires: 4/5/39 9:18 AM
SHA-1 digest: C5:B2:C0:BF:AD:35:9F:C2:E1:FC:E4:7D:00:35:41:85:1D:36:25:8E
MD5 digest: 7B:22:25:84:25:3A:71:F3:69:FD:33:C2:29:86:0A:BE

Do you want to accept the above certificate chain? (y/n) y (hit Enter)
Host localhost authentication information successfully updated.
~~~~

\

\

3.7.2 Create a server to be deployed.

\

\# bin/server create memberX

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-weight: normal"}


[root@localhost wlp]# bin/server create memberX
Server memberX created.
~~~~

\

\

3.7.3 Copy the sample application "snoop" to the server to be deployed.
\
\

~~~~ {.western lang="en-US"
style="margin-left: 1.27cm; font-style: normal; font-weight: normal"}
# cp /opt/lab-materials/applications/snoop.war usr/servers/memberX/apps
~~~~

\

\
3.7.4 Configure the server to be deployed.\
\

\# gedit usr/servers/memberX/server.xml

\

Change the HTTP and HTTPS ports to 9079 and 9442 and configure the app.

~~~~ {.western lang="en-US" style="margin-left: 1.27cm"}
<httpEndpoint id="defaultHttpEndpoint" httpPort="9079"                                        httpsPort=”9442” />

    <application type="war" id="snoop" name="snoop"             location="${server.config.dir}/apps/snoop.war"/>
~~~~

\

3.7.5 Package the server.\
\
 Packaging the server will create a self-contained copy of the entire
Liberty\
 installation which includes the server's configuration. This
self-contained\
 package will be deployed to a host in the next steps. \
\

\# bin/server package memberX

\

\

~~~~ {.western lang="en-US"
style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: none; border-right: none; padding: 0.04cm 0cm; font-weight: normal; text-align: left; widows: 2; orphans: 2"}
[root@localhost wlp]# bin/server package memberX
Packaging server memberX.
Server memberX package complete in /opt/wlp/usr/servers/memberX/memberX.zip.
~~~~

\

\

\
3.7.6 Launch the Deploy tool from the Admin Center.\
\
 In this lab, the only available host is localhost. In a real
environment, \
 additional hosts can be added as deployment targets.

\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m3331e285.png)\
\

\

3.7.7 Select 'localhost' from the list of available hosts.

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m29b7759f.png)\
\

3.7.8 Scroll down and click 'Browse'.\
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_m681db6fb.png)\
\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_58226598.png)\
3.7.9 Upload memberX.zip.\
\
 Navigate to /opt/wlp/usr/servers/memberX via the File System button on
the\
 File Upload navigator's side bar.\
\
\
\
\
\
\
\
\
\
\
\
\
\
\
\
\
\

\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_7d3c9e69.png)\
![image](Session_1377_IMPACT2014-Lab-instructions_html_162b8f32.png)\
3.7.10 Set the target installation directory to **/opt/wlp-deployed\
**Set the keystore passwords to **Impact2014**\
\
\
3.7.11 Enter the admin password '**adminpwd**' and click 'Deploy'\
\
\
3.7.12 Once the upload completes, select the background task button in
the upper\
 right-hand corner to view the background tasks.

\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m1ce2e34e.png)\
 The Background Tasks page:\
\

![image](Session_1377_IMPACT2014-Lab-instructions_html_4d951639.png)\
\

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_6a17ad57.png)\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m2dd95a64.png)\
![image](Session_1377_IMPACT2014-Lab-instructions_html_44e4bb52.png)\
3.7.13 The background tasks can be expanded to see the details of the
task.\
\
\
3.7.14 Return to the Explore tool.\
\
 Hit back button in the Background Tasks view.\
\
 Hit the Toolbox button in the Deploy view.\
\
 Launch the Explore tool.

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m4a0718dd.png)\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m18a3c365.png)\
3.7.15 The total number of servers has increased to 4 servers. Start the
new server.\
\
 The newly deployed server will show up in 'Unknown' state because it
has\
 never been started. Click the Servers view.\
 Click memberX.

\
 Click the Actions button (wrench icon) and start the server.

\
![image](Session_1377_IMPACT2014-Lab-instructions_html_5346d69e.png)\
![image](Session_1377_IMPACT2014-Lab-instructions_html_m76ff750d.png)\
\
 Result of the start operation:

\
3.7.16 The server memberX is now started.\
\
 Return to the 'All Servers' view by clicking on the breadcrumb.

![image](Session_1377_IMPACT2014-Lab-instructions_html_56399d05.png)\
\

\
\
 Close the browser.\
\
\

The deployed server is automatically joined to the collective when
deployed via the Deploy tool and can be managed via the Admin Center and
scripting. Additional hosts can be registered to the collective via the
bin/collective registerHost command. The Deploy tool highlights just one
of the deployment options available through the MBeans provided by the
collective controller. For more details on available collective
controller MBeans, see the wasdev.net.

\

\

\

\

\

\

\

***Thank you!***\
This lab is available from wasdev.net

© 2014 Copyright IBM Corporation Page 61 of 61

\

\
