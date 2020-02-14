# Lab 100: Load and Access Data

  ![](images/100/Title.png)

## Introduction

Derek is a Python developer, and so he avoids spending time in secondary tasks such as database-related work. However, some database work is unavoidable. It’s necessary to secure the database with authorization token files. It’s necessary to create database users. And it’s necessary to load application data. The Oracle Autonomous Transaction Processing Database Cloud Service reduces database-related work to a bare minimum.  In Lab 100 you will first create an Object Store Bucket to provide accessible file storage within the Compartment containing the Autonomous Transaction Processing Database. You will then secure database access by creating authorization tokens and distributing the related wallet file. You will also create necessary database users and necessary database objects. Then you will import necessary application data from a remote export file into the Autonomous Transaction Processing Database. As a final task you will create an external table (accessible via the Autonomous Transansaction Processing Database) containing customer credit score data.

## Lab 100 Objectives

- Download data pump export file.
- Upload data pump export file to the object store bucket.
- Generate an auth token.
- Setup a SQL Developer connection to your Autonomous Transaction Processing (ATP) Database.
- Create a DBMS_CLOUD credential.
- Import Data into the Autonomous Transaction Processing Database.
- Create an external table.

## Steps
<!-- **Please Note:** Steps 1-3 are done locally. Steps 4 and on are done on your Oracle Cloud Developer Image Instance. -->

**Please Note:** All steps are done on your Oracle Cloud Developer Image Instance.


### **STEP 1:** Log in to your OCI dashboard and switch regions

- Open firefox from inside the image (you should still be in the image from the previous lab) and [login](https://www.oracle.com/index.html) to your cloud account.

  ![](images/100/025.png)

  ![](images/login-screen.png)

- Login to your Oracle Cloud Account if you have not done so already.


### **STEP 2:** Add data to Object Storage Bucket

- Click the **Menu icon** in the upper left corner to open the navigation menu. Under the **Core Infrastructure** section, select **Object Storage** then **Object Storage** .

  ![](images/100/002.png)

- Select the **Compartment** `python4dev`.

  ![](images/100/003.png)

- Your new Object Storage Bucket should show up in the list. Once it appears click on the `py4dev` bucket url to view the details.

  ![](images/100/005.png)

- Unzip the lab-resources.zip file you copied from earlier.

- Navigate to your object storage bucket and then click **Upload Object**

  ![](images/100/015.png)

  ![](images/100/016.png)

- Click `select files`, then select the `expdp_alpha.dmp` and the `credit_scoring_100k_pq`. Note we will be importing data from the expdp_alpha.dmp file, and later querying data from the credit_scoring_100k_pq (big data parquet file). The latter file will be used in a future lab. This is just showing you how easy it is to query parquet (and avro) files. Click `Open`, then `Upload Objects`.

  ![](images/100/017.png)

  ![](images/100/018.png)

  ![](images/100/019.png)


### **STEP 3:** Generate auth token for user

- Navigate to `Identity` > `Users`.  

  ![](images/100/020.png)

- Select the username of the current logged in userid (yours will be different from the screenshot).

  ![](images/100/021.png)

- Select `Auth Tokens` on the left, and then `Generate Token`.

  ![](images/100/022.png)

  ![](images/100/023.png)

- Copy the token and save it for later.  If you lose this you can always generate more tokens.

  ![](images/100/024.png)


### **STEP 4:** Download the Autonmous Transaction Processing Database DB Wallet Zip File
<!-- **Remember:** All steps from now on are inside the instance (i.e. using the vnc viewer) -->

  Click the **Menu icon** in the upper left corner to open the navigation menu. Under the **Database** section, select **Autonomous Transaction Processing**.

  ![](images/100/027.png)

- Select the **AlphaOffice** Autonomus Transaction Processing Database.  Be sure to select the correct region, and the correct compartment.

  ![](images/100/028.png)

- Click **DB Connection**

  ![](images/100/029.png)

- Click **Download**

  ![](images/100/030.png)

- Enter the **Password** `a1phaOffice1_` and click **Download**

  **NOTE: There are TWO 1's in the Password... No L's...**

  ![](images/100/031.png)

- Select `Save File` and Click **OK**

  ![](images/100/032.png)

- The **Wallet_orcl4py.zip** file was Downloaded to the directory `/home/opc/Downloads/`

  ![](images/100/034.png)

- As information, the Autonomous Transaction Processing Database Wallet file **Wallet_orcl4py.zip** contains the following files

  ![](images/100/033.png)


### **STEP 5:** Paste wallet files in InstantClient

- Create new folder named wallet in Downloads directory.

  ![](images/100/wallet/27.png)

- Open the wallet zip by double click on it. 

  ![](images/100/wallet/28.png)

- Make sure to navigate to wallet folder and then click on Extract.

  ![](images/100/wallet/29.png)

- To confirm go to wallet folder and see the contents

  ![](images/100/wallet/30.png)

- Open the terminal by clicking on application, favorites and then click on Terminal.

  ![](images/100/wallet/31.png)

- Paste the command ```sudo cp -r /home/opc/Downloads/wallet/. /lib/oracle/18.5/client64/lib/network/admin``` to copy the contents of wallet folder to instantclient

  ![](images/100/wallet/32.png)


### **STEP 6:** Create SQL Developer Connection to the Autonomous Transaction Processing Database

- Open **SQL Developer** through the menu.

  ![](images/100/039.png)

- Right click on `Oracle Connections` and create new connection.

  ![](images/100/041.png)

- Enter/Select the following values, click **Test**. After a `Success` **Status**, click **Save**, then **Connect**

  - **Connection Name:**  `atp-AlphaOffice-Admin`
  - **Username:**  `admin`
  - **Password:**  `a1phaOffice1_`
  - Select `Save Password`
  - **Connection Type:**  `Cloud Wallet`
  - **Configuration File:**  The `Wallet_orc4py4.zip` you downloaded in the previous step

	![](images/100/042.png)


### **STEP 7:** Create Database User in the Autonomous Transaction Processing Database

- You should see the **SQL Developer Worksheet** open. Once opened execute the following SQL Statements to create the `alpha` database user.

  `create user alpha identified by a1phaOffice1_;`
  `grant dwrole to alpha;`

  ![](images/100/043.png)


### **STEP 8:** Create DBMS_CLOUD Credential

- In the same **SQL Developer Worksheet**, execute the following SQL Statements to create the **DBMS_CLOUD Credential** `py4dev_cred`.

	```
	begin
	  DBMS_CLOUD.create_credential(
		credential_name => 'py4dev_cred',
		username => '<your cloud userid>',
		password => '<Auth Token Generated in STEP 3>'
	  );
	end;
	/
	```

  ![](images/100/044.png)


### **STEP 9:** Add DBA View and an Autonomous Transaction Processing Database SQL Developer Connection

- In **SQL Developer**, click on the menu **View** and select **DBA**

  ![](images/100/045.png)

  ![](images/100/046.png)

- Next, you will add the **Connection** you created previously to the **DBA** pane. Click below **DBA**, select the Connection `atp-AlphaOffice-Admin` and click **OK**

  ![](images/100/047.png)

  ![](images/100/048.png)


### **STEP 10:** Import Data into the Autonomous Transaction Processing Database Instance using Data Pump Import Wizard

- On you instance browser, navigate to object storage and select the py4dev bucket.

  ![](images/100/051.png)
	
  ![](images/100/052.png)

- Then select the icon on the far right to retrieve details from `expdp_alpha.dmp`.

  ![](images/100/053.png)

- Copy the URI (don't download the object).

  ![](images/100/054.png)

- Inside the image in SQLDeveloper expand the `atp-AlphaOffice-Admin` connection under the **DBA** pane until the **Data Pump** section is expanded.

  ![](images/100/049.png)

- Right-click on **Import Jobs** and select **Data Pump Import Wizard...**

  ![](images/100/050.png)

- On **Step 1** of the **Import Wizard**, select and/or enter the following and click **Next**.  You will use the values you collected in the text editor at the end of **Step 4**.

  **Note:** Your values for **REGION** and **OBJECT_STORAGE_NAMESPACE** may/will be different.

  - **Type of Import:** `Full`
  - **Credentials or Directories:** `py4dev_cred` (Created in STEP 10)
  - **File Names or URI:** `<URI you copied above>` (Created in STEP 3)

  ![](images/100/055.png)

- Accept the rest of the defaults and click **Next** on **Step 2** of the **Import Wizard**. It can take a minute for the **Next** button to be enabled.  You will see a `Waiting...` message until the **Next** button is enabled.  Accept the remaining defaults by clicking `Next` until you get to the Summary Step and then click `Finish`.

  ![](images/100/056.png)

- The job should complete in a few seconds.

  ![](images/100/057.png)

- Right click on Oracle Connections and create a new one called `atp-alpha`.  Enter a password `a1phaOffice1_`.

  ![](images/100/058.png)

- Connect to user alpha.

  ![](images/100/059.png)

- Confirm the data has been imported by expending the connection and the tables.

  ![](images/100/060.png)

- Create spatial metadata for use in lab 300 and lab 400.  Enter the following in the SQL Developer Worksheet:

`insert into user_sdo_geom_metadata select * from sdo_geom_metadata;`
`commit;`

  ![](images/100/065.png)


### **STEP 11:** Create external table 

- Repeat Step 8 from above in this new user, and execute the following SQL Statements to create the DBMS_CLOUD Credential `py4dev_cred`.
  ```
	begin
	  DBMS_CLOUD.create_credential(
		credential_name => 'py4dev_cred',
		username => '<your cloud userid>',
		password => '<Auth Token Generated in STEP 4>'
	  );
	end;
	/
	```

- Obtain the URI of the credit_scoring_100k_pq file from object storage in your console.

  ![](images/100/061.png)

- Copy the URI path into SQL Developer (temporarily)

  ![](images/100/062.png)

- Copy the following code into SQL Developer below the above URI, and then replace with the uri from above and execute.

```
begin
    dbms_cloud.create_external_table (
       table_name =>'credit_scoring_100k_ext',
       credential_name =>'py4dev_cred',
       file_uri_list =>'https://objectstorage.us-ashburn-1.oraclecloud.com/n/idyofk9mgrls/b/py4dev/o/credit_scoring_100k_pq',
       format =>  '{"type":"parquet",  "schema": "first"}'
    );
end;
/
```
- SQL Dev:

  ![](images/100/063.png)

- Test the view by executing the following:

  `select * from credit_scoring_100k_ext;`

- SQL Dev:

  ![](images/100/064.png)


**This completes the Lab!**

**You are ready to proceed to [Lab 200](LabGuide200.md)**