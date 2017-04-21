# HOL 1 Voting app - Simple Continuous Deployment in Azure

### Duration
This HOL should take ~ 9 minutes

### Goal
Setup simple Continuous Deployment for the Voting Application. 

### Prerequisites
* An active Visual Studio Team Services (VSTS) account
* An Active Azure subscription
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v2.2.0-September2016)
* An Internet connection

### Tasks Overview ###

In this HOL, you will perform the following:

**Set up your Visual Studio Team Services account**

This step helps you download the source code, and then push it to your own Visual Studio Team Services account.

**Provision the environment**
    
Provision the App Service Plan & Web App using an ARM template & Azure PowerShell script.

**Configure simple continuous deployment**

This step configures the actual CD process in the Azure Portal 


# HOL

## Task# 1: Set up your Visual Studio Team Services account

**NOTE:** If you are running Linux on your local workstation, install git:

 `sudo apt-get install git`
 
**NOTE:** If you are running Windows, you have to install the git client from here:

[http://git-scm.com/download](https://git-scm.com/download/win)
> We want to push the application code to your Visual Studio Team Services account.

0. First, navigate to your Visual Studio Team Services account by typing into a browser: 

 `https://{VSTS instance name}.visualstudio.com`

0. Once at your Visual Studio Team Services account, create a new *VotingApp* team project by clicking on the **New** button under **Recent projects & teams**. Type in the project name as *VotingApp* and select *Git* as the version control, then click on **Create project**.

 ![](<media/create_team_project.png>)

0. After the wizard creates your new team project, navigate to the *VotingApp* team project and click on the **Code** tab on the upper-left. 

 ![](<media/navigate_to_code.png>)

0. The *VotingApp* Git repository will be empty, so copy the Clone URL of the VSTS repository to your clipboard and paste it into a text document for use later. 

 ![](<media/copy_vsts_repo_url.png>)

0. Clone the **VotingApp** git repository onto your local machine, open your command-line tool  (perhaps in your "Documents" folder) and type :
 `git clone <CloneURL> VotingApp`

 ![](<../../SETUP_Continuous-Deployment/media/clone_mrp.png>)

0. Change directory

 `cd VotingApp`

0. Copy the file *PartsUnlimitedMRP.zip* (found under the Resources folder with these presentation materials) into this folder, open it, select all the files/folders in it, then copy the contents into this VotingApp folder.

![](<../../SETUP_Continuous-Deployment/media/extracted_files_in_reposirory_folder.png>)
 
0. Back at the command line, add/stage all files into the git repository, commit them and push to VSTS

 ```
 git add . --force

 git commit -m "Initial commit"

 git push origin
 ```

 [This step can take several minutes (1-3 minutes) depending on your connection speed.]

 ![](<media/push_to_vsts.png>)

 > If it's the first time that you are using your Visual Studio Team Services subscription, it will ask you to be authenticated.

 ![](<../../SETUP_Continuous-Deployment/media/vsts_auth.png>)

0. Refresh your **CODE** section page

> If we refresh the page now, Visual Studio Team Services account should now have a copy of the application

 ![](<media/votingapp_in_vsts.png>)

## Task# 2: **Provision the environment**

> Instead of manually creating the Web Application in Azure, we are going to use a PowerShell script & an Azure Resource Management (ARM) template. 

[Make sure you have Azure PowerShell installed from the machine running the script (see [prerequisites](#prerequisites)).]

0. Open a Windows PowerShell Windows

0. Log into your Azure account by executing the following command:

 `Login-AzureRmAccount`

 ![](<media/login-azurermaccount.png>)

 You should get back a response like this:

 ```
 Environment           : AzureCloud
 Account               : ############################
 TenantId              : ########-####-####-####-############
 SubscriptionId        : ########-####-####-####-############
 SubscriptionName      : ############################
 CurrentStorageAccount :
 ```

0. List your subscriptions using the following command:

 `Get-AzureRmSubscription`

 You should get back one (1) or many subscriptions in a response like this: 

 ```
 SubscriptionName : ##########
 SubscriptionId   : ########-####-####-####-############
 TenantId         : ########-####-####-####-############
 State            : Enabled
 
 SubscriptionName : ##########
 SubscriptionId   : ########-####-####-####-############
 TenantId         : ########-####-####-####-############
 State            : Disabled
 
 SubscriptionName : ##########
 SubscriptionId   : ########-####-####-####-############
 TenantId         : ########-####-####-####-############
 State            : Enabled
 ```

0. Identify the desired subscription and copy the `SubscriptionId`

0. Launch the deployment script (replace `<YourAzureSubscriptionId>` with your real Azure SubscriptionId)

 ```
 .\deploy\votingapp\setup_env.ps1 -SubscriptionId <YourAzureSubscriptionId>
 ```
 
 You should get back a response like this:

 ```
 Selecting subscription '########-####-####-####-############'
 
 
 Environment           : AzureCloud
 Account               : stephane.lapointe@gsoft.com
 TenantId              : ########-####-####-####-############
 SubscriptionId        : ########-####-####-####-############
 SubscriptionName      : ###########
 CurrentStorageAccount :
 
 Creating resource group 'voting-app-hol'
 Starting deployment...
 VERBOSE: Performing the operation "Creating Deployment" on target "voting-app-hol".
 VERBOSE: 11:22:51 AM - Template is valid.
 VERBOSE: 11:22:52 AM - Create template deployment 'template'
 VERBOSE: 11:22:52 AM - Checking deployment status in 5 seconds
 VERBOSE: 11:22:57 AM - Resource Microsoft.Web/serverfarms 'voting-app' provisioning status is succeeded
 VERBOSE: 11:22:57 AM - Checking deployment status in 10 seconds
 VERBOSE: 11:23:07 AM - Checking deployment status in 15 seconds
 VERBOSE: 11:23:22 AM - Resource Microsoft.Web/sites 'voting-app-#########' provisioning status is succeeded
 
 DeploymentName          : template
 CorrelationId           : ########-####-####-####-############
 ResourceGroupName       : voting-app-hol
 ProvisioningState       : Succeeded
 Timestamp               : ################
 Mode                    : Incremental
 TemplateLink            :
 TemplateLinkString      :
 DeploymentDebugLogLevel :
 Parameters              : {[baseName, Microsoft.Azure.Commands.ResourceManager.Cmdlets.SdkModels.DeploymentVariable]}
 ParametersString        :
                           Name             Type                       Value
                           ===============  =========================  ==========
                           baseName         String                     voting-app
 
 Outputs                 :
 OutputsString           : 
 ```

## Task# 3: Configure simple continuous deployment

0. Log into the Azure Portal at: [https://portal.azure.com](https://portal.azure.com)

0. Search for `voting-app` and choose the entry of type *App Service*

 ![](<media/azure_portal_search_votingapp.png>)

0. Click the link to the **URL** of the voting app

 ![](<media/click_web_app_url_not_deployed.png>)

 This will open the web app

 ![](<media/web_app_not_deployed.png>)

 There is nothing yet to show since no deployment has been performed

0. Return to the Azure portal

0. Still in the Voting App web app, click **Application settings**

0. In the App Settings, enter a new entry with the following information:

 ```
 Key:  PROJECT
 Value: `src/Session 3/SimpleVoting/SimpleVoting\SimpleVoting.csproj`
 ``` 

0. Click the **Save** button

 ![](<media/save_project_appsettings.png>)

0. Click **Deployment options**, then **Choose Source** and **Visual Studio Team Services**

 ![](<media/select_vsts_deployment_option.png>)

0. Select your VSTS Account

0. Select `VotingApp` project

0. Select `master` branch

0. Click the `OK` button

 ![](<media/vsts_deployment_options.png>)

 The deployment source configuration will be running for a couple of seconds

 ![](<media/setting_up_deployment_source.png>)


 > When deploying a git repository via Kudu, the rules for picking a specific project are as follows:
 > 
 > 0. The default deployment behavior is to lookup for a custom .deployment file (You can use this method to invoke a script of your own .cmd or PowerShell).
 > 0. If none is present it will scan for solution files, if there are multiple solutions, it will fail. if there's 1 solution file, 
 >  it will pick the first website or WAP in that solution and deploy it. If there's none, then fail.
 > 0. If no solutions/project is found, then Xcopy deploy all the repository files (excluding .git and .hg) to the web root. 
 >
 > One downside of using a .deployment file is that it is committed to the repo, and sometimes you want to make that selection outside of the repo.
 > Let's say that you have one repo that contains two different ASP.NET projects (possibly in the same solution), which you want to deploy to different sites. You couldn't use a .deployment file here, as it can only point to one project.
  
 More information here: [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)
 
0. Click on **Overview** and click back on **Deployment Options**
 
 After a couple of seconds you should see the deployment being started

 ![](<media/refresh_deployment_options.png>)
  
 After a minute or two, you should have a successful deployment

0. Click the deployment - **Initial Commit** 

 > This will show you the high level steps that were performed 

 ![](<media/deployment_details.png>)

0. Click the **View Log** link on *Running deployment command...*

 ![](<media/deployment_details_view_deployment_command_logs.png>)

 ![](<media/deployment_activity_logs.png>)

> The deployment will take the application project (csproj), compile it and deploy it to the web app because we have
> configured the *PROJECT* app setting
 
## Teardown
0. In the Azure portal in the `voting-app-hol` resource group, click the **Delete** button.

 ![](<media/delete_resource_group.png>)

0. Confirm the deletion

---------------------

**!! Don't do the following last step if you want to do the HOL#2 right after as it prevents you from setting up the VotingApp project in VSTS again !!**
---------------------

0. Browse to `https://{VSTS instance name}.visualstudio.com/_admin`

0. Click the **Overview** section, select the **VotingApp** team project, click the ellipsis and click **Delete**

 ![](<media/delete_votingapp_vsts_project.png>)

0. Confirm deletion

##Next steps
In this lab, you learned how to create a Continuous Deployment process between an Azure App Service Web Application 
and VSTS that runs when new commits are pushed to the master branch. This allows you to automatically deploy your application 
if there are no breaking syntax changes during the compilation phase.

[HOL#2 - Full Continuous Integration Deployment](../HOL2_VotingApp-Full-Continuous-Integration-Deployment/README.md)

## Complementary information

* [DevOps Fundamentals](https://channel9.msdn.com/Series/DevOps-Fundamentals)
