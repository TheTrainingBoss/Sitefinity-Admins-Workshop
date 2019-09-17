Azure Deployment
----------------

Publishing to the cloud makes your site instantly more scalable and
reliable. Need failover capability or to suddenly expand capacity? You
can take your Sitefinity site as it is now and push it up to Azure.

![](../media/image95.jpeg)

The technologies used in this example are Sitefinity 9, Visual Studio
2015 Update 2, SQL Server Management Studio 2014 and the [Azure
portal](https://portal.azure.com/) The Sitefinity
website and Sitefinity database are already created on the development
server. We'll be creating the following elements in Azure during this
walk-through: Resource Group, SQL Server, SQL Database, a Storage
account and the App Service for the Sitefinity website.

![](../media/image97.png)

#### Prepare the Sitefinity Website and Database

On your local development machine, if you haven't already, create a
new Sitefinity website, and use MS SQL as the database. You should be
able to login to the Sitefinity backend.

#### Create the Azure SQL Server

In the Azure portal, create a new SQL Server. Make a note of the
server path, the user name and password. Create a new Resource group
to contain all the elements of your website. Be sure that the *Allow
Azure Services to Access the Server* checkbox is enabled.

![](../media/image98.jpeg)

#### Note: 
If you use the resource group for everything related to the
site, you can move the entire site to another account later if the
website changes hands or is bought.

Open the SQL Server *Settings* blade, select *Firewall*, and click the
*Add client IP* link. Make sure the *Allow access to Azure services*
switch is turned on. Save the Firewall settings.

![](../media/image99.png)

This should work fine in Enterprise environments with static IPs. If
you're experimenting with this at home with dynamic IPs, you'll have
to add new entries whenever your IP expires.

#### Create the Azure SQL Database

Next, create a SQL database that will hold the Sitefinity data. You
can leave the source as *Blank database* -- it will be populated later
from imported data.

![](../media/image101.jpeg)

#### Publish the Database

The simplest way to publish the database is from Microsoft SQL Server
Management Studio 2014 cumulative update 6 or later. This route allows
you to use the *Deploy Database to Microsoft Azure SQL Database...*
option directly to an Azure Database.

![](../media/image103.jpeg)

Click through the Introduction page to reach the Deployment Settings.

![](../media/image105.jpeg)

In Deployment Settings, click the *Connect* button.

![](../media/image107.jpeg)

In the Connect to Server dialog, enter the Server name, Login and
Password you saved when you [[created the Azure
server]{.underline}.](#Create_the_Azure_SQL_Server) Click the
*Connect* button to return to Deployment settings.

![](../media/image109.jpeg)

The database name will carry over from your original database
instance. Use the *Microsoft Azure SQL Database Settings* to adjust to
resources for the target database. We're using the Standard edition
here, with a maximum database size of 250 GB. The Temporary file name
points to a location that stores a "bacpac" file, but, other than
making sure we have space at that location, we don't need to worry
about it. Clicking the *Next* button, navigates you to a summary page.

![](../media/image111.jpeg)

At the Summary, click the *Finish* button.

![](../media/image113.jpeg)

Depending on the size of the database, connection speed and Azure
resources, the import will process. This example Sitefinity database
took about 4 or 5 minutes to complete.

![](../media/image115.jpeg)

#### Prepare the Sitefinity Application for Publication

On your local development machine, open the Sitefinity site in Visual
Studio. In the Solution Explorer, click the *Show All Files* button,
then open *App\_Data\\Sitefinity\\*. Find your *Sitefinity.lic* file,
right-click and select *Include in Project* from the context menu.

Move down to the *App\_Data\\Sitefinity\\Configuration* directory and
add any config files you want included in the project.

![](../media/image117.png)

Open the *DataConfig.config* file and copy the connection string for
the Azure database into the *connectionString* attribute. Also, change
the value of the *dbType* attribute to *SqlAzure*.

![](../media/image118.jpeg)

Open the project's *web.config* file for editing. Locate the
*\<sectionGroup name="telerik"*

*...\>* tag and ***uncomment*** the section. Then find the
*\<telerik\>* tag and uncomment that section as well.

![](../media/image120.png)

Database storage is the suggested way to go for configuration files.
It allows scaling out more easily, as opposed to copying config files
across multiple instances. If you think you might need to scale-out,
go with the database storage up-front.

At the time of this writing, the 2.7 version of the Azure SDK is
required, even if you're on a later version of the Azure SDK. In the
web.config *\<runtime\>\<assemblyBinding\>* section, add a
*dependentAssembly* entry to ensure that the 2.7 version of Azure is
used.

\<dependentAssembly\>

\<assemblyIdentity name=\"Microsoft.WindowsAzure.ServiceRuntime\"
publicKeyToken=\"31bf3856ad364e35\"/\>

\<bindingRedirect oldVersion=\"0.0.0.0-2.6.0.0\"
newVersion=\"2.7.0.0\"/\>

\</dependentAssembly\>

Here is a listing of the entire \<runtime\> tag in our running example for reference.

\<runtime\>

\<assemblyBinding xmlns=\"urn:schemas-microsoft-com:asm.v1\"\>

\<dependentAssembly\>

\<assemblyIdentity name=\"Newtonsoft.Json\"
publicKeyToken=\"30ad4fe6b2a6aeed\" culture=\"neutral\" /\>

\<bindingRedirect oldVersion=\"0.0.0.0-6.0.0.0\"
newVersion=\"6.0.0.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Web.Helpers\"
publicKeyToken=\"31bf3856ad364e35\"

/\>

\<bindingRedirect oldVersion=\"1.0.0.0-3.0.0.0\"
newVersion=\"3.0.0.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Web.WebPages\"
publicKeyToken=\"31bf3856ad364e35\" /\>

\<bindingRedirect oldVersion=\"0.0.0.0-3.0.0.0\"
newVersion=\"3.0.0.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Web.Mvc\"
publicKeyToken=\"31bf3856ad364e35\" /\>

\<bindingRedirect oldVersion=\"0.0.0.0-5.2.3.0\"
newVersion=\"5.2.3.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Web.Razor\"
publicKeyToken=\"31bf3856ad364e35\" culture=\"neutral\" /\>

\<bindingRedirect oldVersion=\"0.0.0.0-3.0.0.0\"
newVersion=\"3.0.0.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Web.WebPages.Razor\"
publicKeyToken=\"31bf3856ad364e35\" culture=\"neutral\" /\>

\<bindingRedirect oldVersion=\"0.0.0.0-3.0.0.0\"
newVersion=\"3.0.0.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"Telerik.Sitefinity.Mvc\"
publicKeyToken=\"b28c218413bdf563\" culture=\"neutral\" /\>

\<bindingRedirect oldVersion=\"1.3.350.0\" newVersion=\"1.4.360.0\"
/\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Threading.Tasks\"
publicKeyToken=\"b03f5f7f11d50a3a\" culture=\"neutral\" /\>

\<bindingRedirect oldVersion=\"0.0.0.0-2.6.8.0\"
newVersion=\"2.6.8.0\" /\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"Microsoft.WindowsAzure.ServiceRuntime\"
publicKeyToken=\"31bf3856ad364e35\"/\>

\<bindingRedirect oldVersion=\"0.0.0.0-2.6.0.0\"
newVersion=\"2.7.0.0\"/\>

\</dependentAssembly\>

\<dependentAssembly\>

\<assemblyIdentity name=\"System.Web.Cors\"
publicKeyToken=\"31bf3856ad364e35\" /\>

\<bindingRedirect oldVersion=\"1.0.0.0-5.2.0.0\"
newVersion=\"5.2.0.0\" /\>

\</dependentAssembly\>

\</assemblyBinding\>

\</runtime\>

#### Publish to Azure

In the Solution Explorer, right-click the Visual Studio project and
select *Publish* from the context menu. In the Publish Web dialog,
select Microsoft Azure App Service.

![](../media/image122.png)

#### Note: 
You will need to have the Azure SDK installed to get the
Microsoft Azure App Service publish target.

Click the *New...* button to create a new App Service.

![](../media/image124.png)

Enter a Web App Name or leave the automatic defaults. Use the Resource
Group created earlier to keep all the website elements in one
container. The App Service plan is defined by your Resource Group, so
you can leave the default. Click the *Create* button.

![](../media/image125.jpeg)

Your deploy settings should download automatically. You can do a quick
verification using the *Validate Connection* button, then click the
*Publish* button.

![](../media/image127.jpeg)

The Visual Studio Output window will show the build progress. Then
Visual Studio will kick off a browser to display the Sitefinity --
Azure website.

![](../media/image129.jpeg)

And finally, the backend screen at its new Azure web address:

![](../media/image131.jpeg)

**Next Topic**
[Alternative Publishing](../Alternative%20Publishing/readme.md)