# Lab 200: Build and Test Python REST Service

  ![](images/100/Title.png)

## Introduction

In Lab 200 you also continue to play the role of Derek, but now you get to the fun part, building out Python apps.  You have been asked to enhance the customer experience by providing customer access to modify their profiles and enable customers to maintain their own profiles.  You will use Oracle's cx_Oracle Python interface to enable connectivity to the Autonomous Transaction Processing Database, and use the open source technologies Flask micro web framework, Bokeh interactive visualization library, and ReactJS  to support web site development.

## Lab 200 Objectives

- Setup Python application 
- Check the web service

## Steps


### **STEP 1:** Configure project in Visual Studio Code

- First we add the Visual Studio Code in the favorites so it can be easily accessible. Click on **Applications** and then click on **Activity Overview**

    ![](images/200/0-1.png)

- In the search bar type visual and you should see **Visual Studio Code** in results.

    ![](images/200/0-2.png)

- **Right Click** on Visual Studio Code and then click on **Favorites**. 

    ![](images/200/0-3.png)

- Now that Visual Studio Code is added to favorites. Open the terminal,click on **Applications**, select **Favorites** and then click on **Visual Studio**.

    ![](images/200/1-1.png)

- Click on **File** and then click on **Open Folder** 

    ![](images/200/1.png)

-  In the Dialog box go to the unzipped **lab-resources** folder, look inside of it, click on **pythonWebService**, and then click on **OK**.

    ![](images/200/2.png)

- You will the see the files on the left panes.

    ![](images/200/3.png)

- Open the inbuilt Visual Studio Code terminal by clicking on **View** and then click on **Terminal**. You can also open by keyboard shortcut **[Ctrl + `]**.

    ![](images/200/4.png)

- First we need to intall pip in the image. Type/Copy the following command in the terminal. 
`$ sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`

    ![](images/200/5-1.png)

- Next type the following command `$ sudo python get-pip.py`.

    ![](images/200/5-2.png)

- In the terminal enter command `$ sudo pip install virtualenv`. This will create virtual enviornment to install and run the packages, so that we don't install packages globally.

    ![](images/200/5.png)

- Next enter command `$ virtualenv env` to create entry point.

    ![](images/200/6.png)

- To run the virtual enviornment type `$ source env/bin/activate`. If successfull you will see (env) before the path that means you are now in virtual env.

    ![](images/200/7.png)

- We have all the required packages in requirements file. To install it run the command `$ pip install -r requirements.txt`.

    ![](images/200/8.png)


### **STEP 2:** Run python web service

- Leave the terminal open. Click on **config.py** file from the left pane and change the details in the file with your Autonomous Transaction Processing Database information.

    ![](images/200/9.png)

- We are using **flask** library to create the web service and **cx-Oracle** to connect the Autonomous Transaction Processing Database to our application. The data then fetched from database it parsed and converted to JSON format to display.
- If you closed the terminal, open it again and make sure to be in virtual env. Run the command `$ python app.py`. Now we have our web service running. Leave the terminal as it is.

    ![](images/200/10.png)

- To confirm everything works fine, open firefox and go to URL [http://127.0.0.1:5001/customers/1](http://127.0.0.1:5001/customers/1).
    
    ![](images/200/11.png)


### **STEP 3:** Run frontend application

- Open **customwebapp** in Visual Studio Code by opening the project in a new window: click on **File** and then click on **New Window**. The frontend application is created using ReactJS.

    ![](images/200/12-1.png)

- Click on **File** and then click on **Open Folder** 

    ![](images/200/1.png)

-  In the Dialog box go to the unzipped **lab-resources** folder, look inside of it, click on **customwebapp**, and then click on **OK**.

- Open the inbuilt Visual Studio Code terminal by clicking on **View** and then click on **Terminal**. You can also open by keyboard shortcut **[Ctrl + `]**.

    ![](images/200/4.png)

- Run the command `$ npm install` to install the required packages to run the project.

    ![](images/200/12.png)

- Run the command `$ npm start` to start the application 

    ![](images/200/13.png)


### **STEP 4:** Create and view customer info

- Open the browser and go to localhost:3000 to see the website.Put the customer ID in the search bar. Here for example we entered 1 and then click on **search**.

    ![](images/200/14.png)

- Scroll down and there is edit button next to **Country** row, click on it to edit.

    ![](images/200/15.png)
  
- In the form enter the name of country you want it to change to. Click on **Save Changes**. This will save changes in the Autonomuos Transaction Processing Database using the web API we created.

    ![](images/200/16.png)
   
- Changes are displayed.
    
    ![](images/200/17.png)

- Click on **Create customer** tab on top, and fill the form.

    ![](images/200/18.png)
  
- Click on **Submit**.

    ![](images/200/19.png)

- If everything is filled corrrectly success message is displayed at top.
    
    ![](images/200/20.png)


**This completes the Lab!**

**You are ready to proceed to [Lab 300](LabGuide300.md)**