# Docker Hands-on Lab #

## Lab Introduction
Docker is a set of platform-as-a-service products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels.

Lab courtesy of NATD Solution Engineering Team.  Check out the original lab [here!](http://go.oracle.com/docker)

### Lab Assumptions
- Each participant has completed the Environment Setup lab and successfully created a compute instance.
- The Virtual Compute Network (VCN) has been created with the appropriate Ingress rules.
- Each participant has created a docker hub [account](http://docker.hub.com).
- Participants are not logged onto Oracle's VPN.
- Participants are using Chrome as the preferred browser and have installed Chrome's JSON formatter.


## Step 1: Login to your Oracle Cloud Account

1.  From any browser go to www.oracle.com to access the Oracle Cloud.

    <img src="../options/img/login-screen.png" width="600">

2. Click the icon in the upper right corner.  Click on **Sign in to Cloud** at the bottom of the drop down.  *NOTE:  Do NOT click the Sign-In button, this will take you to Single Sign-On, not the Oracle Cloud*

    ![](../options/img/signup.png)    

3. Enter your **Cloud Account Name**: `c4u03` in the input field.

    <img src="../options/img/login-tenancy.png" width="600">

4.  Enter your **Username** and **Password** in the input fields and click **Sign In**.

    ![](../options/img/cloud-login.png)

## Step 2: Lab Setup

1. Login to the ssh terminal.

    ````
    ssh -i optionskey opc@<your ip address>
    ````

1.  Before beginning the lab, you will need switch to the oracle user and stop the default listener running on port 1521.

    ````
    sudo su - oracle
    ps -ef | grep tns
    lsnrctl status LISTENER
    lsnrctl stop LISTENER
    ps -ef | grep tns
    exit
    ````

2. You will use yum (a package management tool for Linux) to install the Docker engine, enable it to start on re-boot, grant docker privledges to the opc user and finally install GIT.  When prompted, press *Y* to download.  All of these steps will be performed as the root user.

    ````
    sudo -s
    yum install docker-engine
    usermod -aG docker opc
    systemctl enable docker
    systemctl start docker
    ````
   ![](../options/img/docker/python1.png)

    ![](../options/img/docker/python2.png)

3. Next, we are going to install git using yum as the root user.

    ````
    yum install git
    ````
    ![](../options/img/docker/installgit.png)

4.  Verify the version by switching to the opc user.

    ````
    su - opc
    docker version
    docker images
    git --version
    ````
    ![](../options/img/docker/gitversion.png)

5.  Place your server in permissive mode.

    ````
    exit
    setenforce 0
    sestatus
    ````
    ![](../options/img/docker/setenforce.png)

6. Switch back to the opc user and verify you are the `opc` user.

    ````
    su - opc
    whoami
    `````

## Step 3: Docker Basic Concepts

1.  Check the version of docker.

    ````
    docker version
    ````
    ![](../options/img/docker/dockerversion2.png)

2.  Start your application, restclient, in docker on port 8002 in json format.  


    ````
    docker ps
    docker run -d -it --rm --name restclient -p=8002:8002 -e DS='json' wvbirder/restclient
    ````

    - "-d" flag runs the container in the background
    - "-it" flags instructs docker to allocate a pseudo-TTY connected to the container’s stdin, creating an interactive bash capable shell in the container (which we will use in a moment when we connect into the container)
    - "-h" We give the container a hostname "oracledb-ao" to make it easier to start/stop/remove, reference from other containers, etc
    - "-p" We map port 8002 from within the container to the same ports on the HOST for accessibility from outside of the container's private subnet (typically 172.17.0.0/16). This allows the container to be accessed from the HOST, for example. The default port for Oracle's tns listener is on port 1521 and port 5600 is used for HTTP access to Enterprise Manager Express
    - "--name" The name of the container will be "restclient"
    - "-v" This maps the directory where you downloaded the restclient setup.
    ![](../options/img/docker/dockerps.png)

3.  Find the public IP address of your instances.  Compute -> Instance.

    ![](../options/img/docker/computeinstance.png)

    <img src="../options/img/docker/selectdboptions2.png" width="600">

    <img src="../options/img/docker/dboptions2.png" width="600">

4.  Open up a browser on your laptop and go to your public URL on port 8002.  Go to http://Enter IP Address:8002/products. Depending on whether you have a JSON formatter, you should see the products in your application, in RAW or FORMATTED format.  `Note:  If you are on the VPN, disconnect`

    <img src="../options/img/products2-8002.png" width="600">

    <img src="../options/img/docker/products.png" width="600">

5.  The `restclient` container was started earlier with the -rm option.  This means when stopping it will remove ALL allocated resources.  The `ps` command with the `-a` option shows the status of ALL containers that are running.  As you can see, there are no containers running.

    ````
    docker stop restclient
    docker ps -a
    ````
    ![](../options/img/docker/restclient2.png)

 6.  Let's start another container on your compute instance's 18002 port.  Type the following command:

        ````
        docker run -d -it --rm --name restclient -p=18002:8002 -e DS='json' wvbirder/restclient
        docker ps -a
        ````
        ![](../options/img/docker/restclient.png)

7.  Go back to your browser and change the port to 18002.

    <img src="../options/img/docker/18002.png" width="600">

## Step 4: Docker Networking Basics

Now that you know how to start, stop and relocate a container, let's see how to get information about the network.

1.  Inspect the network bridge that docker created for you out of the box.  This shows network information about all the containers running on the default bridge. We see that our restclient container is assigned IP Address 172.17.0.2. You can ping that address from your compute instance.

    ````
    docker network inspect bridge
    ````
    ![](../options/img/docker/network.png)

2.  Ping that address for your restclient container from your compute instance.

    ````
    ping 172.17.0.2 -c3
    ````
4.  Stop your restclient container.

    ````
    docker stop restclient
    ````  
## Step 5: Create an Oracle Database Container

1.  Verify your docker version.
    ````
    cd ~
    docker version
    ````
2.  Make sure you are in the /home/opc directory.  You will clone some setup scripts from git.
    ````
    pwd
    git clone https://github.com/wvbirder/AlphaOfficeSetup.git
    cd /home/opc
    chmod -R 777 Alpha*
    ````

3.  Login with your Docker Hub credentials.
    ````
    docker login
    ````

3.  There are database setup files that you cloned in an earlier step.   Ensure the listener has stopped.  Let's see how easy it is to deploy an Oracle Database to a docker container.  Issue the command below.  
    ````
    docker run -d -it --name orcl -h='oracledb-ao' -p=1521:1521 -p=5600:5600 -v /home/opc/AlphaOfficeSetup:/dbfiles wvbirder/database-enterprise:12.2.0.1-slim
    ````

- -d runs the command in the background
- -h assigns it the hostname oracleadb-ao
- -p maps ports 1521 and 5600 (Enterprise Manager Express) inside the container to your compute instance. In an earlier step, we added ingress rules for these ports
- --name is the name of the container
- v maps the directory where you downloaded the setup files to the /dbfiles directory inside the container

4.  To watch the progress type the following command passing the name of the container:  orcl.
    ````
    docker logs --follow orcl
    ````
5.  When the database creating is complete, you may see "The database is ready for use". The instance creation may happen quickly and that message may scroll past. Press control-c to continue.

## Step 6: Create A Schema in Container Running Oracle Database and Login to EM Express

1.  To create the schema we need to "login" to the container.  Type the following:
    ````
    docker exec -it orcl bash
    ````
2.  Let's make sure the /dbfiles directory mapped earlier is writeable.
    ````
    cd /dbfiles
    touch xxx
    ls
    ````
3.  Now run the sql script from inside the container using SQL*Plus.
    ````
    sqlplus / as sysdba
    @setupAlphaOracle.sql
    exit
    ````
4.  Now that our schema is created, let's login to Enterprise Manager Express.  Enter the address below into your browser:
    ````
    http://<Public IP Address>:5600/em
    ````

5.  If prompted to enable Adobe Flash, click Allow.  Login using the credentials below.  Leave the container name field blank.  Explore the database using EM Express.
    ````
    Username: sys
    Password: Oradoc_db1
    Check the "as SYSDBA" checkbox
    ````

## Step 7: Deploy Application

1.  Download the docker image, twitterfeed, extract it and run the container.  The download is from the wvbirder docker hub account where this application is staged.
    ````
    docker run -d --name=twitterfeed -p=9080:9080 wvbirder/twitterfeed
    ````
2.  Check to see which containers are running.  
    ````
    docker ps
    ````

3.  Open up a broswer to see the application with the stream of texts.  http://Public IP address:9080/statictweets

4.  Let's run the restclient with the Oracle Database as the datasource.
    ````
    docker run -d -it --rm --name restclient -p=8002:8002 --link orcl:oracledb-ao -e ORACLE_CONNECT='oracledb-ao/orclpdb1.localdomain' -e DS='oracle' wvbirder/restclient

    ````

3.  Go back to your browser to see the application with the stream of texts.  http://Public IP address:8002/products

4.  An application called AlphaOfficeUI has been staged in wvbirders docker hub account.  Let's download it, extract and run it.
    ````
    docker run -d --name=alphaofficeui -p=8085:8085 wvbirder/alpha-office-ui-js
    ````
3.  Go back to your browser to see the application running on port 8085.  http://Public IP address:8085.  Click on one of the products to see the details and the twitter feed comments.

## Step 8: Change Application (optional)

1. Copy a background image from your compute instance into the filesystem of the container.
    ````
    docker cp /home/opc/AlphaOfficeSetup/dark_blue.jpg alphaofficeui:/pipeline/source/public/Images
    ````

2.  wvbirder's container does not have vim installed.  So you will configure it.  First you need to login to the container.
    ````
    docker exec -it alphaofficeui bash
    apt-get update
    apt-get install vim
    ````

3.  Verify the dark_blue.jpg file is in the container and then use vim to edit the html file for the main page in your application.  Change the highlighted areas to your name.
    ````
    ls /pipeline/source/public/Images
    vim /pipeline/source/public/alpha.html
    ````

4.  Let's edit the css file as well and change the background color of the app.
    ````
    vim /pipeline/source/public/css/alpha.css
    exit
    ````
5.  Let's commit this new docker image to your docker hub now.  Wvbirder thanks but we have our own Docker account.  Once committed, list the images.  Note that your image is now listed.
    ````
    docker commit alphaofficeui (your-dockerhub-account)/(image-name)
    docker images
    ````
6.  Let's start a container based on your image.  First we need to stop the existing container.
    ````
    docker stop alphaofficeui
    docker rm alphaofficeui
    ````

7.  Let's download, extract and install the new container from your docker account
    ````
    docker run -d --name=alphaofficeui -p=8085:8085 (your-dockerhub-account)/(image-name)
    ````
3.  Go back to your browser to view the application.  http://Public IP address:8085

4.  Now let's push this image to your docker hub account
    ````
    docker push (your-dockerhub-account)/(image-name)
    ````
5.  Open up a new browser tab and login to hub.docker.com.  Verify your new account is there.

6.  Don't forget to restart your oracle listener!
    ````
    sudo su - oracle
    . oraenv ORCL
    lsnrctl start LISTENER
    ps -ef | grep tns
    ````
