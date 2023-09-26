<p style="text-align:center">
    <a href="https://skills.network" target="_blank">
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/assets/logos/SN_web_lightmode.png" width="200" alt="Skills Network Logo">
    </a>
</p>

<h1 align=center><font size = 5>Hands-on Lab: Accessing Your Database with RODBC</h1>


### Welcome!

In this hands-on lab, we will learn how to connect and discover metadata from database servers with R using RODBC.


<div class="alert alert-block alert-info" style="margin-top: 20px">
<h3>Tasks</h3>
<ol><ol><ol>
<li><a href="#ref6a">Pre-requisites</a></li>
<li><a href="#ref6b">Create an R notebook</a></li>
<li><a href="#ref6c">Load RODBC</a></li>
<li><a href="#ref6d">Connection information</a></li>
<li><a href="#ref6e">Create a database connection</a></li>
<li><a href="#ref6f">Connection Attributes</a></li>
<li><a href="#ref6g">Connection Metadata</a></li>
<li><a href="#ref6h">Supported Datatypes</a></li>
<li><a href="#ref6i">List of Tables</a></li>
<li><a href="#ref6j">Columns in a Table</a></li>
<li><a href="#ref6k">Dis-connect</a></li>
</ol></ol></ol>
<br>
Estimated Time Needed: <strong>15 min</strong>
</div>


<a id="ref6a"></a>

<h3>a. Pre-requisites</h3>


In this lab we will use Jupyter Notebooks within SN Labs to access data in a Db2 on Cloud database using RODBC.


<a id="ref6b"></a>

<h3>b. Create an R notebook</h3>

If required, set the notebook kernel to R by clicking on the kernel on the top right hand corner:

<img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-RP0103EN-SkillsNetwork/labs/Lab%20-%20Accessing%20Your%20Database%20with%20RODBC/kernel.png">


<a id="ref6c"></a>

<h3>c. Load RODBC</h3>


The RODBC package and the ODBC driver for Db2 are pre-installed on your workbench. Let’s load the RODBC package by clicking on the following cell and executing it (Shift+Enter):



```R
library(RODBC);
```

<a id="ref6d"></a>

<h3>d. Connection information</h3>


To connect to your Db2 instance, you require the following details:
* Driver class
* Database name
* Hostname
* Port number
* Protocol
* Username
* Password

We will be using different variables to store this information, so that we can use these values at a later point of time when required.

Replace the values for **hostname, port number, username and password** by copying them from Service Credentials in your DB2 instance. 

For instructions on accessing **Db2 Service Credentials**, go to <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/LAB-0v6_Create_Database_Credentials.md.html?origin=www.coursera.org">Hands-on Lab: Create Db2 Service Credentials.</a>

>Note:This is just an example screenshot of service credentials. However these values will vary with respect to the DB2 instance which you create.


<details>
<summary>Click here to view/hide hint</summary>
<p>


<img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-RP0103EN-SkillsNetwork/labs/Lab%20-%20Accessing%20Your%20Database%20using%20RJDBC/images/servicecredentials.png">
    
    
    
</details>



```R
#Enter the values for your database connection
dsn_driver = "com.ibm.db2.jcc.DB2Driver"
dsn_database = "bludb"            
dsn_hostname = "<125f9f61-9715-46f9-9399-c8177b21803b.c1ogj3sd0tgtu0lqde00.databases.appdomain.cloud:30426>"   #replace <yourhostname> with your hostname from the Service credentials
dsn_port = "30426"                     #replace with the port number from Service Credentials 
dsn_protocol = "TCPIP"            
dsn_uid = "<lyh00644>"            #replace <username> with your username from Service Credentials
dsn_pwd = "<1gnnv4swWVICcvBQ>"            #replace <password> with your password from Service Credentials
dsn_security = "ssl"
```

<a id="ref6e"></a>

<h3>e. Create a database connection</h3>


The next step is to create a connection string and connect to Db2 using odbcDriverConnect() function. **odbcDriverConnect()** takes this connection string as its parameter and returns a connection object.



```R
conn_path = paste("DRIVER=",dsn_driver,
                  ";DATABASE=",dsn_database,
                  ";HOSTNAME=",dsn_hostname,
                  ";PORT=",dsn_port,
                  ";PROTOCOL=",dsn_protocol,
                  ";UID=",dsn_uid,
                  ";PWD=",dsn_pwd,
                  ";SECURITY=",dsn_security,        
                    sep="")
conn = odbcDriverConnect(conn_path)
conn
```

    Warning message in odbcDriverConnect(conn_path, believeNRows = FALSE):
    “[RODBC] ERROR: state 01000, code 0, message [unixODBC][Driver Manager]Can't open lib 'com.ibm.db2.jcc.DB2Driver' : file not found”Warning message in odbcDriverConnect(conn_path, believeNRows = FALSE):
    “ODBC connection failed”


-1



```R
conn_path = paste("DRIVER={IBM DB2 ODBC DRIVER}",
                  ";DATABASE=",dsn_database,
                  ";HOSTNAME=",dsn_hostname,
                  ";PORT=",dsn_port,
                  ";PROTOCOL=",dsn_protocol,
                  ";UID=",dsn_uid,
                  ";PWD=",dsn_pwd,
                  ";SECURITY=",dsn_security,        
                    sep="")
conn = odbcDriverConnect(conn_path)
conn
```

    Warning message in odbcDriverConnect(conn_path):
    “[RODBC] ERROR: state 08001, code -1336, message [IBM][CLI Driver] SQL1336N  The remote host "<125f9f61-9715-46f9-9399-c8177b21803b.c1ogj3sd0tgtu0lqde00.databases.a" was not found.  SQLSTATE=08001”Warning message in odbcDriverConnect(conn_path):
    “ODBC connection failed”


-1


<a id="ref6f"></a>

<h3>f. Connection Attributes</h3>


Let’s examine the connection attributes using the attributes() function:



```R
attributes(conn)
```


    NULL


<a id="ref6g"></a>

<h3>g. Connection Metadata</h3>


And review the connection metadata using  the odbcGetInfo() function. This function will provide details about the database name, version and the version of the ODBC driver:



```R
conn.info = odbcGetInfo(conn)
conn.info["DBMS_Name"]
conn.info["DBMS_Ver"] 
conn.info["Driver_ODBC_Ver"]
```


    Error in odbcGetInfo(conn): argument is not an open RODBC channel
    Traceback:


    1. odbcGetInfo(conn)

    2. stop("argument is not an open RODBC channel")


<a id="ref6h"></a>

<h3>h. Supported Datatypes</h3>


Let’s now examine the datatypes supported by the database using sqlTypeInfo() function. This function will return a dataframe having information about the supported datatypes. The dataframe will have 4 columns such as Type_Name, Data Type and Column Size.



```R
sql.info = sqlTypeInfo(conn)
print(sql.info)
```


    Error in sqlTypeInfo(conn): first argument is not an open RODBC channel
    Traceback:


    1. sqlTypeInfo(conn)

    2. stop("first argument is not an open RODBC channel")


Let's print only the first and third column from the dataframe:



```R
print(sql.info[c(1,3)], row.names=FALSE)
```

<a id="ref6i"></a>

<h3>i. List of Tables</h3>


We will use the sqlTables() function to return a dataframe with information about table-like objects (i.e. TABLEs, VIEWs, ALIASes, etc.) in the Db2 system Schema **SYSIBM** and save it in a variable called tab.frame. 
We will get the count of the tables in the schema using **nrow()** function. 
We can then display their names using the TABLE_NAME column of the dataframe.




```R
tab.frame = sqlTables(conn, schema="<Enter Schema>") # e.g. "SYSIBM"
nrow(tab.frame)
tab.frame$TABLE_NAME
```

<a id="ref6j"></a>

<h3>j. Columns in a Table</h3>


Next, let’s look at column metadata for columns in the system catalog table **SYSSCHEMATA**. We will use **sqlColumns()** function which describes the column structure of tables on an ODBC database connection.



```R
tab.name <- "<Enter Table>" # e.g. "SYSSCHEMATA"
col.detail <- sqlColumns(conn, tab.name)
print(col.detail[c(2,3,4,6,7,9,18)], row.names=FALSE)
```

<a id="ref6k"></a>

<h3>k. Dis-connect</h3>


Finally, as a best practice we should close the database connection once we're done with it.



```R
odbcCloseAll()
```

### Practice exercises


##### 1. Provide the database credentials for your instance of **Db2**



```R
#write your code here
```

<details>
<summary>Click here to view/hide hint</summary>
<p>

```
# Fill in the following details
dsn_driver = "com.ibm.db2.jcc.DB2Driver"
dsn_database = "..."            
dsn_hostname = "<yourhostname>"  
dsn_port = "..."               
dsn_protocol = "..."           
dsn_uid = "<username>"        
dsn_pwd = "<password>"      
```

</details>


<details>
<summary>Click here to view/hide solution</summary>
<p>

```
#Enter the values for you database connection
dsn_driver = "com.ibm.db2.jcc.DB2Driver"
dsn_database = "bludb"            # e.g. "bludb"
dsn_hostname = "<yourhostname>"   # e.g. replace <yourhostname> with your hostname
dsn_port = ""                # e.g. "3273" 
dsn_protocol = "TCPIP"            # i.e. "TCPIP"
dsn_uid = "<username>"              # e.g. replace <username> with your userid
dsn_pwd = "<password>"            # e.g. replace <password> with your password
```

</details>


##### 2. Create a connection string and connect to Db2.



```R
#write your code here
```

<details>
<summary>Click here to view/hide hint</summary>
<p>

```
# Fill in the ...
conn_path <- paste("DRIVER=",...
                  ";DATABASE=",...
                  ";HOSTNAME=",...
                  ";PORT=",...
                  ";PROTOCOL=",...
                  ";UID=",...
                  ";PWD=",..."
                  ";SECURITY=",...")
conn <- ...(...)
```

</details>


<details>
<summary>Click here to view/hide solution</summary>
<p>

```
conn_path <- paste("DRIVER=",dsn_driver,
                  ";DATABASE=",dsn_database,
                  ";HOSTNAME=",dsn_hostname,
                  ";PORT=",dsn_port,
                  ";PROTOCOL=",dsn_protocol,
                  ";UID=",dsn_uid,
                  ";PWD=",dsn_pwd,
                  ";SECURITY=",dsn_security,        
                    sep="")
conn <- odbcDriverConnect(conn_path)
conn
```

</div>


##### 3. List of tables: Use the sqlTables() function to return a dataframe with information about table-like objects (i.e. TABLEs, VIEWs, ALIASes, etc.) in the Db2 system Schema **SYSIBM** and save it in a variable called tab. Display the count of the tables in the schema using **nrow()** function and their names using the TABLE_NAME column of the dataframe.



```R
#write your code here
```

<details>
<summary>Click here to view/hide hint</summary>
<p>

```
# Fill in the ...
tab <- sql...(conn, ...="<Enter Schema>")
nrow(tab....)
tab$...
```

</details>


<details>
<summary>Click here to view/hide solution</summary>
<p>

```
tab <- sqlTables(conn, schema="<Enter Schema>") # e.g. "SYSIBM"
nrow(tab)
tab$TABLE_NAME
```

</details>


##### 4. Display the column metadata for columns in the IBM system catalog table **SYSSTRINGS**



```R
#write your code here
```

<details>
<summary>Click here to view/hide hint</summary>
<p>

```
# Fill in the ...
tab.... <- "...."
col.detail <- sql...(conn, tab....)
print(....detail[c(...,...,7,...,...)], row....=FALSE)
```

</details>


<details>
<summary>Click here to view/hide solution</summary>
<p>

```
query = "SELECT * FROM SYSIBM.SYSSTRINGS";
rs = dbSendQuery(conn,query);
df = fetch(rs,20);
```
</p>
</details>


<a id="ref6o"></a>

<h3>Summary</h3>


In this lab you accessed data in a Db2 on Cloud database using RODBC connection from a R notebook in Jupyter, and discovered different metadata.


<hr>


#### Thank you for completing this lab on getting connected and querying databases using RODBC.


<hr>

## Authors

-   [Rav Ahuja](https://ca.linkedin.com/in/rav-ahuja-8aa4562a?cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-RP0103EN-SkillsNetwork-23619267&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ)
-   [Agatha Colangelo](https://www.linkedin.com/in/agathacolangelo?cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-RP0103EN-SkillsNetwork-23619267&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ)
-   [Sandip Saha Joy](https://www.linkedin.com/in/sandipsahajoy?cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-RP0103EN-SkillsNetwork-23619267&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ)
-   [Shreya Khurana](https://www.linkedin.com/in/shreya-khurana-437211237/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkRP0103ENSkillsNetwork896-2023-01-01)


## Changelog

| Date (YYYY-MM-DD) | Version | Changed By                   | Change Description                 |
| ----------------- | ------- | ---------------------------- | ---------------------------------- |
| 2023-07-20   | 2.2     | Shreya Khurana        | Created revised version of the lab|
| 2021-07-14        | 2.1    | Lakshmi Holla            | Added ssl changes |
| 2021-01-22        | 2.0     | Sandip Saha Joy              | Created revised version of the lab |
| 2017              | 1.0     | Rav Ahuja & Agatha Colangelo | Created initial version of the lab |

<hr>

<h2 align=center><font size = 5>Copyright &copy; IBM Corporation 2017-2021. All rights reserved.</h2>

