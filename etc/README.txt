Nuage - VCloud Director integration workflows for vRealize Orchestrator (vRO) README

Prerequisites:
==============
 
- vRO server and client (version 6.0.x or 7.0.x) are already installed and configured.
- vCloud Director (version 5.5.x or 8.x) is already installed and configured, including the Organizations and Organization VDCs.
- Nuage VSP (version 3.2Rx or 4.0Rx) is already installed and configured.

Installation steps:
===================

1. Install RabbitMQ server and integrate with vCloud Director:

   a. Follow the instructions presented in this blog: http://vbyron.com/blog/install-configure-rabbitmq-integration-vcloud-director/
   b. In vCloud Director, go to System->Administration->Extensibility->Settings, put a check mark beside "Enable notifications" then click "Apply".
   
2. Install and configure VSPK plug-in (version 3.2.x or 4.0.x) in vRO:

   a. Aquire VSPK plug-in bundle (o11nplugin-vspk-x.x.x.vmoapp).
   b. Follow the installation and configuration instructions in INSTALL.txt.
   c. There should be a session to the VSD server configured once the configuration steps have been performed.
      
3. Install and configure vCloud Director plug-in version 5.5.1.2 or 8.0 in vRO:
   
   a. Download plug-in from the following location (it requires a My VMWare account): 
         Version 5.5.1.2: http://www.vmware.com/download/download.do?downloadGroup=VCD_VCO_PLUGIN_5512
         Version 8.0:     http://www.vmware.com/download/download.do?downloadGroup=VCD_VCO_PLUGIN_800
   b. Follow installation and configuration instructions from the following page: 
         http://pubs.vmware.com/orchestrator-plugins/topic/com.vmware.using.vcloud_director.plugin.doc_55/GUID-20B31427-3E27-481B-9C1D-6ABC187098E7.html
   c. There should be a connection to the vCloud Director instance configured once the configuration steps have been performed.

4. Configure the vCenter plug-in in vRO:

   a. In the Orchestrator Client main window, click the "Workflows" icon.
   b. Expand the following folders: Library->vCenter->Configuration.
   c. Right click on the "Add a vCenter Server instance" workflow.
   d. Select "Start Workflow...".
   e. Enter the host name or IP address and Port of the vCenter Server instance.
   f. Click the "Next" button.
   g. Enter the User name, Password (and the Domain name if required) to login to the vCenter Server.
   h. Click the "Submit" button. 
     
5. Install Nuage-VCloud Director integration package in vRO:

   a. Aquire Nuage vCD workflow bundle (net.nuagenetworks.vro.vspk.vcloud-x.x.x.package),
   b. Log in to the vRO client.
   c. In the Orchestrator Client main window, select "Design" mode.
   d. Click on the "Import package..." icon.
   e. Select the package file and click the "Open" button.
   f. Click on the "Import" button.
   g. Make sure all the elements listed have a check beside them and then click the "Import selected elements" button.
   h. There should now be a new package called "net.nuagenetworks.vro.vspk.vcloud" in the list of installed packages.

6. Create Enterprise template in VSD

   a. Create an EnterpriseProfile with the desired settings.
   b. Create an Enterprise that is linked to EnterpriseProfile created in step a.
   c. Create a L2DomainTemplate.
   d. Create an IngressACLTemplate under L2DomainTemplate with the desired settings.
   e. Create an EgressACLTemplate under L2DomainTemplate with the desired settings.
   f. Create a DomainTemplate.
   g. Create an IngressACLTemplate under DomainTemplate with the desired settings.
   h. Create an EgressACLTemplate under DomainTemplate with the desired settings.

NOTE: The following blog can be used as a textual and visual reference for the steps listed below: 
         http://www.vcoteam.info/articles/learn-vco/179-configure-the-amqp-plug-in.html
       
7. Configure the AMQP plug-in:

   A. Configure a broker:

       a. In the Orchestrator Client main window, click the "Workflows" icon.
       b. Expand the following folders: Library->AMQP->Configuration.
       c. Right click on the "Add a broker" workflow.
       d. Select "Start Workflow...".
       e. Enter a Name e.g. "vCloud Director". 
       f. Click the "Next" button.
       g. Enter the IP address and Port of the vCloud Director instance.
       h. Enter the same SSL settings (if enabled), Virtual Host, User name and Password as configured in vCloud Director's AMQP Broker Settings, 
          in the system->Administration->Extensibility section.
       i. Click the "Submit" button.

   B. Declare a queue:

       a. In the Orchestrator Client main window, click the "Workflows" icon.
       b. Expand the following folders: Library->AMQP.
       c. Right click on the "Declare a queue" workflow.
       d. Select "Start Workflow...".
       e. Select the Broker instance that was created in step A. 
       f. Click the "Next" button.
       g. Enter a Name for the queue e.g "vro".
       h. Set "Durable" field to "Yes".
       i. Click the "Submit" button.

   C. Declare an exchange:

       a. In the Orchestrator Client main window, click the "Workflows" icon.
       b. Expand the following folders: Library->AMQP.
       c. Right click on the "Declare an exchange" workflow.
       d. Select "Start Workflow...".
       e. Select the Broker instance that was created in step A. 
       f. Click the "Next" button.
       g. Enter the same Name as the Exchange configured in vCloud Director's AMQP Broker Settings (e.g. systemExchange), 
          in the system->Administration->Extensibility section.
       h. Set "Type" to "topic".	       
       i. Set "Durable" field to "Yes".
       j. Click the "Submit" button.

   D. Create a bind:

       a. In the Orchestrator Client main window, click the "Workflows" icon.
       b. Expand the following folders: Library->AMQP.
       c. Right click on the "Bind" workflow.
       d. Select "Start Workflow...".
       e. Select the Broker instance that was created in step A. 
       f. Click the "Next" button.
       g. Enter the Name of the the queue that was configured in step B.
       h. Enter the Exchange name that was configured in step C.
       i. Enter "#" (without the quotes) as the Routing key.
       j. Click the "Submit" button.

       Please note that for performance reasons, you may want to configure one binding per notification type. So instead of creating
       a single binding with the generic "#" routing key, create one binding for each of following routing keys:

            true.*.*.*.com.vmware.vcloud.event.org.create
            true.*.*.*.com.vmware.vcloud.event.org.delete
            true.*.*.*.com.vmware.vcloud.event.network.create            
            true.*.*.*.com.vmware.vcloud.event.network.delete
            true.*.*.*.com.vmware.vcloud.event.vm.create
            true.*.*.*.com.vmware.vcloud.event.vm.delete
            
    E. Create a subscription:
    
       a. In the Orchestrator Client main window, click the "Workflows" icon.
       b. Expand the following folders: Library->AMQP->Configuration.
       c. Right click on the "Subscribe to queues" workflow.
       d. Select "Start Workflow...".
       e. Enter a Name for the subscription e.g. "vro".
       f. Click the "Next" button.
       g. Select the Broker instance that was created in step A. 
       h. Click the "Next" button.
       i. Select the queue that was configured in step B.
       j. Click the "Submit" button.

7. Configure Policy in vRO:

   a. In the Orchestrator Client main window, select "Administer" mode.
   b. Click the "Policy Templates" icon.
   c. Expand the following folders: Library->AMQP.
   d. Right click "Subscription".
   e. Select "Apply Policy...".
   f. Optionally change the Policy name and description.
   g. Select the Subscription that was created at step 6E.
   h. Click the "Submit" button.
   i. In the Orchestrator Client main window, select "Run" mode.
   j. Right click the Policy that was just created.
   k. Select "Edit".
   l. On the "General" tab, for the "Startup" option, select: "On server startup, start the policy".
   m. On the "Scripting" tab, click on the top level node called "Subscription".
   n. Go to the bottom pane ("General" sub-tab).
   o. Click the "Add Attribute" icon. Set the Name to "session", Type to "VSPK:Session" and Value to the session configured in step 2b.
   p. Click the "Add Attribute" icon. Set the Name to "vcdHost", Type to "vCloud:Host" and Value to the connection configured in step 3b.
   q. Click the "Add Attribute" icon. Set the Name to "vcServer", Type to "VC:SdkConnection" and Value to the connection configured in step 4 (as the first array entry).
      Note that more vCenter servers can be added to the array if needed.
   r. Expand the "Subscription" top level node, then expand the "subscription" node and select the "OnMessage" trigger event.
   s. In the bottom pane ("Workflow" sub-tab), click the magnifying glass and select the following workflow: "Process vCloud Notification"
   t. For the "subscription" Local parameter, click on "not set" under the "Source parameter" column and set the parameter to "self".
   u. For the "messageBody" Local parameter, click on "not set" under the "Source parameter" column and set the parameter to "event.key.body". 
   v. For the "session" Local parameter, click on "not set" under the "Source parameter" column and set the parameter to the "session" attribute created in step o.
   w. For the "vcdHost" Local parameter, click on "not set" under the "Source parameter" column and set the parameter to the "vcdHost" attribute created in step p.
   x. For the "vcServer" Local parameter, click on "not set" under the "Source parameter" column and set the parameter to the "vcServer" attribute created in step q.
   y. Click the "Save and close" button.
   z. Right click the subscription and select "Start policy".
  
Support information:
====================

For support and feature requests, please contact:
nuage-oss-support@alcatel-lucent.com