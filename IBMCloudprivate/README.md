# Build IIB IBM Cloud private Application
Currently there is not a shipped IBM Integration Bus, IBM Cloud private application, however this is straight forward to build. There are three sections:   
1. Publishing the docker container to a suitable docker repository
2. Creating the helm chart
3. Deploying an instance of IIB with our application

## Publishing the docker container to a suitable docker repository

We assume you have already completed the instructions to [create the docker image locally](../docker/README.md)

The docker images is currently located on your local machine, and often it will be required to publish into a central docker repository. 

For instance the docker repository associated with an IBM Cloud private environment or the Bluemix Container Repository. The following instructions document the process to push the image, and are a walk through of the process documented within the knowledge center:
https://www.ibm.com/support/knowledgecenter/en/SSBS6K_1.2.0/manage_images/using_docker_cli.html

1. The IBM Cloud private master node IP address needs to be identified, this can be found in the top left corner of the admin console:    
   ![](img/IdentifyingTheMasterHost.png)

   This will be hosting the docker repository normally on port **8500**.

1. The master.cfc hostname needs to resolve locally to the identified IP address. Therefore a new entry will be added into the local host file:     
   vi /etc/hosts     
   172.16.251.87    master.cfc     
   ![](img/HostEntry.png)

1. Login into the master node and copy the  /<installation_directory>/misc/configure-registry-cert.sh file to the local machine.

1. Run the downloaded script file:    
   sudo ./configure-registry-cert.sh

1. The script will install a new certificate on the machine, docker needs to be restarted to pick up the change:     
   service ssh restart
   service docker restart

1. You should now be able to login to the docker repository:     
   docker login master.cfc:8500
   Login in as the administrator of the repository: *admin*
   using the same pass that you use for the web administration console

1. The create image can now be tagged and uploaded to the docker repository:    
   docker tag iib10009 master.cfc:8500/default/iib10009:dev
   docker push master.cfc:8500/default/iib10009:dev

## Creating the helm chart
Read [Creating a helm chart compatible with IBM Cloud private and deploying to a runtime](main helm/readme.md)

## Deploying an instance of IIB with our application

### Prerequisite
As a prerequisite, IBM Integration Bus needs to be installed on the machine. See instructions
in [main readme](https://github.com/ibm-cloud-architecture/refarch-integration-esb#on-premise)  
* It is assumed that you have already cloned the git repository


### Building the IBM Integration Bus Application

1. Run the mqsiprofile command to setup the environment:
   . /opt/ibm/iib-10.0.0.9/server/bin/mqsiprofile

1. Run the following command to generate the BAR file:
   mqsipackagebar -a iibApp.bar -w /iibmqDocker/refarch-integration-esb/integration -k RESTAPI

1. Upload the BAR file to an HTTP Server so it can be available to the docker container during start-up. Use whatever mechanism you like to upload the file to the HTTP server, I used scp:     
   scp iibApp.bar callumj@9.19.34.117:/storage/CASE/refarch-privatecloud   


### Creating an Application instance in IBM Cloud private - App Center

1. Log into the IBM Cloud private Web UI, and navigate to the System page, and click **Sync Up Repository**:  

   ![](img/syncRepository.png)
   Confirm the Sync operation.

1. Open the **App Center**, and type IIB within the search bar:      

   ![](img/AppCenterIIB.png)

1. Click on the **Install Package** button, and customize the locations for your Integration Bar file, and potentially the Docker image. Click **Review and install** button to view the final configuration:     

   ![](img/configAppInstall.png)

1. Click **Install** to start the installation process.

1. A success pop-up will appear.

1.  Navigate to the Applications section:      

    ![](img/NavigateToApps.png)

1. Within the search bar enter **iib** and an application should be shown:   

    ![](img/IIBApp.png)    
   At this stage it may show the available as **0** as the containers are still being created.

1. Click into the application to view the details:  
   ![](img/SummaryOfApplication.png)

1. To verify the solution is working by clicking on the **access webui** link. This should load the IIB Web UI within a browser.
