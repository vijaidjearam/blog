---
layout: post
date: 2021-11-08 09:11:00
title: Glpi-PowerBI-Reporting
category: Glpi
tags: Glpi Invnetory PowerBI
---
1. Install [Power BI Desktop](https://www.microsoft.com/en-us/download/details.aspx?id=58494) on a machine
2. Download the ODBC connector for MariaDB from the [official site](https://dlm.mariadb.com/browse/odbc_connector/87/1186/) (Same principle for MySQL) 
    * Note- Please install version : [mariadb-connector-odbc-3.1.13-win64.msi](https://dlm.mariadb.com/1671860/Connectors/odbc/connector-odbc-3.1.13/mariadb-connector-odbc-3.1.13-win64.msi)  
    * :smiling_imp: The latest version: mariadb-connector-odbc-3.1.14-win64.msi has a bug - It crashes the ODBC Data Source utility while connection.
4. Launch the "ODBC Data utility Source "from Windows
5. Select the connector in question 
6. Enter the requested information (in my case)
     * Connection Name : glpi-test 
     * Server Name : IP of the server
     * Port : 3306
     * User name:
     * Password: 
 * ![image](https://user-images.githubusercontent.com/1507737/140706917-8f6c2dc4-8ed3-4600-a2e6-50f9bc69f6f7.png)
 * ![image](https://user-images.githubusercontent.com/1507737/140708528-5d640aa1-25f0-4f38-bdae-2795d6976cf2.png)
 * If the test is successful, a message tells you so and you can choose your database 
 * ![image](https://user-images.githubusercontent.com/1507737/140708624-5f1c272b-ab38-4a04-8f79-403f0a959631.png)
6. Start Power Bi Desktop 
7. Then choose "ODBC" in the "Get Data" menu. A window opens and you just have to select your connector that you have just created. 
 * ![image](https://user-images.githubusercontent.com/1507737/140708662-bed85654-f644-4476-9e68-de4c5e9b7975.png)
8. Enjoy!
