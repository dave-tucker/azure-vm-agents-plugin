azure-slave-plugin
==================

Jenkins Plugin to create Azure slaves

Supports creating 

1. Windows slave on Azure Cloud using SSH and JNLP
  * For windows images to launch via SSH, the image needs to be preconfigured with ssh.  
   For preparing custom windows image, refer to [Azure documentation](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-capture-image-windows-server/)
 
2. Linux slaves on Azure Cloud using SSH
  * For preparing custom linux image, refer to [Azure documentation]( http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-capture-image/)




## How to install the Azure Slave plugin
1. Within the Jenkins dashboard, click Manage Jenkins.
2. In the Manage Jenkins page, click Manage Plugins.
3. Click the Available tab.
4. In the "Cluster Management and Distributed Build" section, select the Azure Slave plugin.
5. Click either “Install without restart” or “Download now and install after restart”.
6. Restart Jenkins if necessary.<br>

<H2>Configure the plugin : Azure profile configuration</H2>
a)    Within the Jenkins dashboard, click Manage Jenkins --> Configure System --> Scroll to the bottom of the page 
      and find the section with the dropdown "Add new cloud" --> click on it and select "Microsoft Azure"<br>
b)    Enter the subscription ID and the management certificate from your publish settings file. 
      If you don’t have a publish settings file, click on the help button and follow the directions to 
      download the publish settings file.<br>
c)    Click on “Verify configuration” to make sure that the profile configuration is done correctly.<br>
d)    Save and continue with the template configuration (See instructions below)<br>

<H2>Configure the plugin : Template configuration.</H2>
a)    Click on the "Add" option to add a template. A template is used to define an Azure slave configuration, like 
      its VM  size, its region, or its retention time.<br>
b)    For the template name, provide a valid DNS name. Jenkins will create a cloud service with same name if one 
      does not already exists.<br>
c)    For the description, provide any remarks you wish about this template configuration. This field is not 
      used for slave provisioning.<br> 
d)    For the label, provide any valid string. E.g. “windows” or “linux”. The label defined in a template can be
      used during a job configuration.<br>
e)    Select the desired region from the combo box.<br>
f)    Select the desired VM size.<br>
g)    Specify the Azure Storage account name. Please note that the storage account and cloud service should use the 
      same region. Alternatively you can leave it blank to let Jenkins create a storage account automatically if needed.<br>
h)    Specify the retention time in minutes. This defines the number of minutes Jenkins can wait before automatically 
      deleting an idle slave.<br>
      Specify 0 if you do not want idle slaves to be deleted automatically.<br>
i)    Select a usage option:<br>
      If "Utilize this node as much as possibl" is selected, then Jenkins may run any job on the slave as long as it 
      is available.<br>
      If "Leave this node for tied jobs only" is selected, Jenkins will only build a project (or job) on this node 
      when that project specifically was tied to that node.<br>
      This allows a slave to be reserved for certain kinds of jobs.<br>
j)    For the Image Family or ID , enter either an available image family name or a specific image ID. <br>
      If you want to specify an image family, then just enter the first character with the proper case to see an
      automatically generated list of available families. Jenkins will use the latest image within the selected family.<br>
      If you want to specify a specific image ID, enter the name of the image. Note that since image ID’s are not auto   
      populated, the exact name needs to be entered manually. Also, if you are referring to an image using an image ID from   
      the public Azure image gallery rather than your own account, note that such public images with specific IDs are 
      available in Azure only for a limited time as they eventually get deprecated in favor of newer images in the same 
      family. For this reason, it is recommended that you use the image family to refer to public platform images, and image 
      ID’s for your own custom-prepared images.<br>
k)    For the launch method, select SSH or JNLP.<br>
         Linux slaves can be launched using SSH only.<br> 
         Windows slaves can be launched using SSH or JNLP. For Windows slaves, if the launch method is SSH then 
         image needs to be custom-prepared with an SSH server pre-installed.<br>
      When using the JNLP launch option, ensure the following:<br>
      1) Jenkins URL (Manage Jenkins --> configure system --> Jenkins Location) 
         The URL needs to be reachable by the Azure slave, so make sure to configure any relevant                                      firewall rules accordingly.<br> 
      2) TCP port for JNLP slave agents (Manage Jenkins --> configure global security --> Enable    
         security --> TCP port for JNLP slaves). <br>
      The TCP port needs to be reachable from the Azure slave launched using JNLP. It is recommended to use a fixed port so         that any necessary firewall exceptions can be made. <br>
      If the Jenkins master is running on Azure, then open an endpoint for "TCP port for JNLP slave agents" and, in case of 
      Windows, add the necessary firewall rules inside virtual machine (Run --> firewall.cpl).<br> 
l)    For the Init script, provide a script to install at least a Java runtime if the image does not have Java   
      pre-installed.<br>
      For the JNLP launch method, the init script must be in PowerShell.<br>
      If the init script is expected to take a long time to execute, it is recommended to prepare custom images with the            necessary software pre-installed.<br>
      For more details about how to prepare custom images, refer to the below links:<br>
      http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-capture-image-windows-server/<br>
      http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-capture-image/<br>
m)    Specify a user name and a password as per the rules explained in the help text.<br>
n)    Make sure to validate the template configuration by clicking on the link “Verify Template”. This will connect 
      to your Azure account to verify the correctness of the supplied information. <br><br>


<h2>Template Configuration for Ubuntu images.</h2>
a) Configure an Azure profile and Template as per the above instructions.<br>
b) If the init script is expected to take a long time to complete, it is recommended to use a custom-prepared Ubuntu 
   image that has the required software pre-installed, including a Java runtime<br> 
c) For platform images, you may specify an Init script as below to install Java, Git and Ant:<br>

<pre><code>
      #Install Java
      sudo apt-get -y update
      sudo apt-get install -y openjdk-7-jdk
      sudo apt-get -y update --fix-missing
      sudo apt-get install -y openjdk-7-jdk
      
      # Install Git
      sudo apt-get install -y git
      
      #Install Ant
      sudo apt-get install -y ant
      sudo apt-get -y update --fix-missing
      sudo apt-get install -y ant
</code></pre>

<h2>Template configuration for Windows images with launch method JNLP. </h2>
a) Make sure to follow the instructions specified in III k above for JNLP.<br>
b) If the Jenkins master does not have a security configuration, leave the Init script blank for the default 
   script to execute on the slave.<br>
c) If the Jenkins master has a security configuration, then refer to the script at    
   https://gist.github.com/snallami/5aa9ea2c57836a3b3635 and modify the script with the proper Jenkins credentials.<br>

   At a minimum, the script needs to be modified with the Jenkins user name and API token.
   To get the API token, click on your username --> configure --> show api token<br>

   The below statement in the script needs to be modified:
   $credentails="username:apitoken"

 
