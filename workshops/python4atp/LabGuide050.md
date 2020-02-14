# Lab 050: Setup Cloud Environment

  ![](images/050/Title.png)

## Introduction

In Lab 50 (as Derek) you will initiate the Oracle cloud environment that you will use to create and deploy your microservices applications. This environment will be contained within a cloud Compartment, and communication within the Compartment will be via a Virtual Cloud Network (VCN). The Compartment and VCN will isolate and secure the overall environment. You will deploy two Oracle Cloud Services for this environment. An Oracle Cloud Developer Image will be used to develop and deploy your microservices code. The microservices will access data within an Autonomous Transaction Processing (ATP) Cloud Service.

To deploy these services, you will be using Terraform, a tool for building, changing, and versioning infrastructure safely and efficiently. It is an important tool for anyone looking to standardize IaaS (Infrastructure as a Service) within their organization.

***To log issues***, click here to go to the [github oracle](https://github.com/oracle/learning-library/issues/new) repository issue submission form.

***We recommend that you create a notes page to write down all of the credentials you will need.***

## Lab 050 Objectives

- Log into OCI tenancy.
- Setup your IAAS environment and create common components.
- Create a new Cloud Developer Image from Marketplace.
- Create an Autonomous Transaction Processing (ATP) Database.
- Create an Object Storage bucket.

## Steps

### **STEP 1:** Your Oracle Cloud Trial Account

You have already applied for and received your Oracle Cloud Free Tier Account.


### **STEP 2:** Log in to your OCI dashboard

- From any browser go to oracle.com to access the Oracle Cloud.

    [https://www.oracle.com/](https://www.oracle.com/)

    ![](images/login-screen.png)

- Click the icon in the upper right corner.  Click on **Sign in to Cloud** at the bottom of the drop down.   *NOTE:  Do NOT click the Sign-In button, this will take you to Single Sign-On, not the Oracle Cloud*

    ![](images/signup.png)   
 
- Enter your **Cloud Account Name** in the input field and click the **Next** button.

  ![](images/050/002.png)

    **Note this is NOT your email. This is the name of your tenancy noted in the email you received during signup**
- Enter your username (this may be your email address) and password and click on **Sign In**.
  ![](images/050/003.png)

- Once you log in you will see a page similar to the one below.

    ![](images/hamburger.png)  


### **STEP 3:** Download and Install Terraform zip folder

- Grab the zip file [here](https://github.com/edercervantes/terraform-OCI-for-resource-manager) and save it somewhere you can find later.

    ![](images/050/018.png)

### **STEP 4:** Get Your Oracle Cloud Credentials

To run our Terraform folder in the cloud, we will take advantage of OCI resource manager. It is a powerful tool for planning, and executing multiple Terraform jobs, all without having to installing anything locally. In order for resource manager to create resources for you, it needs to know a few key credentials on the OCI console.

- Click on the profile icon in the top right. Then click into the tenancy link.

  ![](images/050/013.png)

- Copy the **Object Storage Namespace** in your notes.

  ![](images/050/014.png)

 ### **STEP 5:** Generate your SSH key pair

- On Linux or Mac enter this in a command shell.
    
    `$ ssh-keygen -b 2048 -t rsa`

    You can call the key whatever you want (the default is easiest).  It will create a private key and a public key. The public key is used when you are prompted for a SSH key when you create services, and the matching private key is used to access those services after creation. (eg: Cloud Developer Image).

    ![](images/050/028.png)

- On Windows, follow these [instructions](https://www.ssh.com/ssh/putty/windows/puttygen).
    You can call the key whatever you want (the default is easiest).  It will create a private key and a public key. The public key is used when you are prompted for a SSH key when you create services, and the matching private key is used to access those services after creation. (eg: Cloud Developer Image). 

### **STEP 6:** Creating a Resource Manager Stack

Now, we will see the true power of Terraform as opposed to manual creation. By using Terraform, you have a reusable process for creating infrastructure. In some cases, like this one, you don't have to know anything about how the process works. You can deploy different pre-designed infrastructure designs for many different purposes, which frees up users to focus on their projects.

- On the OCI console, click on the hamburger menu and scroll down to **Solutions and Platform**. Hover over **Resource Manager** and click on **Stacks**.

    ![](images/050/020.png)

- Make sure the **Compartment** on the left side says root. If not, then change it to root. Then, click **Create Stack**.

    ![](images/050/030.png)

- Drag and drop the zip file you downloaded earlier into the dashed line box, or click on **Browse** and find it. Then, you can give your **Stack** a name, like `python4dev_terraform`. You can also give a description if you'd like, but it is not necessary. Make sure you are still in the root compartment, and using Terraform version 0.11.x. Then click **Next**.

    ![](images/050/022.png)

- You will see a list of variables that will be used. Find **SSH_PUBLIC_KEY** and paste the public ssh key you created earlier in the given field. **It must be in text format.**

    ![](images/050/023.png)

- Then, make an **AUTONOMOUS_DATABASE_ADMIN_PASSWORD**. **The password must be between 12 and 30 characters long, and must contain at least 1 uppercase, 1 lowercase, and 1 numeric character. It cannot contain the double quote symbol (") or the username "admin", regardless of casing.**

    ![](images/050/026.png)

- Next, populate the **OBJ_STORE_NAMESPACE** field with the **Object Storage Namespace** credential you saved earlier. Then click **Next**.

    ![](images/050/024.png)

- Finally, review your variables and make sure everything looks good. Then click **create**.

    ![](images/050/025.png)
	
### **STEP 7:** Creating OCI resources in Resource Manager

- Now inside of the resource manager, hover over **Terraform Actions** and click on **Plan**.

    ![](images/050/065.png)

-  You can give the plan a name, or keep the default. Then click on **Plan** to begin.

    ![](images/050/066.png)

- Wait for the plan to succeed, then click on **Stack Details**.

    ![](images/050/019.png)

- Again, hover over **Terraform Actions** and click on **Apply**.

    ![](images/050/011.png)

- You can give the apply a name, or keep the default. You can leave the other settings the same. Then click on **Apply**.

    ![](images/050/012.png)

- **The apply may take several minutes. Please be patient.**

    ![](images/050/057.png)

### **STEP 8:** Connect to your Marketplace Developer Image

For more information about the Marketplace Developer Image [click here](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/54030984).

- Click on the hamburger menu, and navigate to **Core Infrastructure**. Hover over **Compute** and click on **Instances**.

	![](images/050/034.png)

- Click on your image to identify the IP address. You will use this to ssh into the image.

	![](images/050/035.png)

    **If you cannot see your instance, make sure you are in the Demo compartment.**

- SSH into the image. 
    **Note if you are on Windows you will need to use putty.**
    Open a terminal window on a Mac or command shell on Linux and enter the following command:

    `$ ssh -i <path/to/your/private-key> opc@<instance IP address>`

	![](images/050/036.png)

- Enter `$ vncpasswd` to set your VNC access (make it a secure one!).

	![](images/050/037.png)

- Enter `$ vncserver` to start the vncserver.

	![](images/050/038.png)

- Enter `$ exit` to go back to your local directory.

- Open a SSH tunnel.
    ***NOTE:*** do not close this terminal window.  It maintains the tunnel to the developer image, which we access through VNC.  If for whatever reason the window is closed or you are otherwise logged out (sometimes tunnels drop), then just run this again to log in.
    
    For Windows, follow these [instructions](https://www.skyverge.com/blog/how-to-set-up-an-ssh-tunnel-with-putty/) for information on how to create a tunnel on Windows.

     This example works on Linux and Mac. **Note:** on Linux you will need to be su.

     `$ ssh -i <path/to/your/private-key> -L 5901:localhost:5901 opc@<your IP address>`

    ![](images/050/039.png)

- Open a vnc viewer session.  If you don't already have vnc viewer you can download it [here](https://www.realvnc.com/en/connect/download/viewer/).

    Enter `localhost::5901` into the browser and then press Enter.

    ![](images/050/068.png)

    Enter the **vncpasswd** password you created earlier. 

	![](images/050/040.png)

    Now you have a user interface for your instance.

	![](images/050/041.png)

### **STEP 9:** Download Files Used in this Workshop

[Click to Download](https://oracle.github.io/learning-library/workshops/python4atp/lab-resources.zip). **Keep track of which directory this zip file gets saved to.**

- Next, open a new terminal window. **It is important that this is a new window, since the session you opened previously must not be closed.**

- Run the command below.

    `$ ssh -i <path/to/your/private-key> opc@<instance IP address>`

    If you are prompted, enter `yes`.

- Run `$ pwd` to view your home path. Then run `$ exit` to go back to your local environment. If your home path is different than the one below, then change it to yours.

    `$ scp </path/to/lab-resources.zip> opc@<instance IP address>:/home/opc`

    If you are prompted, enter `yes`.

    _Now the zip file has been copied into your instance!_

- Finally, go back into your instance.
    
    Once inside, run `$ ls` to verify that you see your zip file.

![](images/050/064.png)

**This completes the Lab!**

**You are ready to proceed to [Lab 100](LabGuide100.md)**


<!--

EXTRA IMAGES IN THE IMAGES->050 FOLDER THAT ARE NO LONGER NEEDED

![](images/050/018.1.png)
![](images/050/058.png)
![](images/050/011.png)
![](images/050/012.png)
![](images/050/032.png)
![](images/050/033.png)
![](images/050/031.png)
![](images/050/061.png)
![](images/050/062.png)
![](images/050/067.png)
![](images/050/063.png)
![](images/050/015.png)
![](images/050/016.png)
![](images/050/017.png)
![](images/050/027.png)
![](images/050/060.png)
-->