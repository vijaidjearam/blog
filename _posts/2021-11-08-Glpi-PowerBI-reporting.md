---
layout: post
date: 2021-11-08 09:11:00
title: Glpi-PowerBI-Reporting
category: Glpi
tags: glpi inventory powerbi
---
1. Install [Power BI Desktop](https://www.microsoft.com/en-us/download/details.aspx?id=58494) on a machine
2. Download the ODBC connector for MariaDB from the [official site](https://dlm.mariadb.com/browse/odbc_connector/87/1186/) (Same principle for MySQL) 
    * Note- Please install version : [mariadb-connector-odbc-3.1.13-win64.msi](https://dlm.mariadb.com/1671860/Connectors/odbc/connector-odbc-3.1.13/mariadb-connector-odbc-3.1.13-win64.msi)  
    * :smiling_imp: The latest version: mariadb-connector-odbc-3.1.14-win64.msi has a bug - It crashes the ODBC Data Source utility while connection.
3. Installation classic of mariadb-connector-odbc-3.1.13-win64.msi.
4. Click the start button and search "ODBC Data Source (64 bit)" and lauch it with admin privilege.
5. click add and in the Create New Data Source window select the MariaDB ODBC 3.1 Driver and click on Finish button
	 * ![image](https://user-images.githubusercontent.com/1507737/140706917-8f6c2dc4-8ed3-4600-a2e6-50f9bc69f6f7.png)
	 * Connection Name : glpi-test
6. In the Next window  Enter the requested information (in my case)
	* Server Name: IP of the server
	* Port :3306
	* User name
	* Password
 * ![image](https://user-images.githubusercontent.com/1507737/140708528-5d640aa1-25f0-4f38-bdae-2795d6976cf2.png)
7. click on the button TestDSN ,If the test is successful, a message tells you so and you can choose your database -> choose Glpi from the dropdown.
 * ![image](https://user-images.githubusercontent.com/1507737/140708624-5f1c272b-ab38-4a04-8f79-403f0a959631.png)
8. Open the PowerBI file and click on the *refresh* button on the ribbon menu, a window pops up and demands for the database password. Please fill in the respective info and thats-it.
9. Enjoy!:smiley:
| cvcxbvx | xcvxv | xcvxvx | cxvxvx | xcvxcvcx |
|---------|-------|--------|--------|----------|
|         |       |        |        |          |
|         |       |        |        |          |
|         |       |        |        |          |
