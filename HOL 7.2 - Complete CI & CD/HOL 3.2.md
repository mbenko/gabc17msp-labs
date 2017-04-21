# HOL 2 Voting app - Complete Continuous Integration & Continuous Deployment

### Duration
This HOL should take ~ 15 minutes (~19 minutes with Build trigger extra)

### Goal
Setup Complete Continuous Integration & Continuous Deployment for the Voting Application. 

### Prerequisites
* An active Visual Studio Team Services (VSTS) account
* An Active Azure subscription
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v2.2.0-September2016)
* An Internet connection

### Tasks Overview ###

In this HOL, you will perform the following:

**Set up your Visual Studio Team Services account**

This step help you download the source code, and then push it to your own Visual Studio Team Services account.

**Provision the environment**
    
Provision the App Service Plan & Web App using an ARM template & Azure PowerShell script.

**Configure Continuous Integration & Continuous Deployment in VSTS**

This step configure the actual CD process in the Azure Portal 

---------------------------------
*NOTE: If you already performed HOL#1 you can skip Task#1 and jump directly to Task#2*
---------------------------------

## Task# 1: Set up your Visual Studio Team Services account

**NOTE:** If you are running Linux on your local workstation, install git:

 `sudo apt-get install git`
 
**NOTE:** If you are running Windows, you have to install the git client from here:

 [http://git-scm.com/download](https://git-scm.com/download/win)
 > We want to push the application code to your Visual Studio Team Services account.

0. First, navigate to your Visual Studio Team Services account by typing into a browser: 

 `https://{VSTS instance name}.visualstudio.com`

0. Once at your Visual Studio Team Services account, create a new *VotingApp* team project by clicking on the **New** button under **Recent projects & teams**. Type in the project name as *VotingApp* and select *Git* as the version control, then click on **Create project**.

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/create_team_project.png>)

0. After the wizard creates your new team project, navigate to the *VotingApp* team project and click on the **Code** tab on the upper-left. 

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/navigate_to_code.png>)

0. The *VotingApp* Git repository will be empty, so copy the Clone URL of the VSTS repository to your clipboard and paste it into a text document for use later. 

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/copy_vsts_repo_url.png>)

0. Clone the **VotingApp** git repository onto your local machine, open your command-line tool (perhaps in your "Documents" folder) and type :
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

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/push_to_vsts.png>)

 > If it's the first time that you are using your Visual Studio Team Services subscription, it will ask you to be authenticated.

 ![](<../../SETUP_Continuous-Deployment/media/vsts_auth.png>)

0. Refresh your **CODE** section page

> If we refresh the page now, Visual Studio Team Services account should now have a copy of the application

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/votingapp_in_vsts.png>)

## Task# 2: **Provision the environment**

> Instead of manually creating the Web Application in Azure, we are going to use a PowerShell script & an Azure Resource Management (ARM) template. 

[Make sure you have Azure PowerShell installed from the machine running the script (see [prerequisites](#prerequisites)).]

0. Open a Windows PowerShell Windows

0. Log into your Azure account by executing the following command:

 `Login-AzureRmAccount`

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/login-azurermaccount.png>)

 You shoud get back a response like this:

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
 Account               : ###########
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

## Task# 3: Configure Continuous Integration & Continuous Deployment in VSTS

0. Return to your VSTS project at: `https://{VSTS instance name}.visualstudio.com/_git/VotingApp`

0. Once on the project’s homepage, click on the **Build & Release** hub at the top of the page, then on **Builds**

 ![](<media/builds_tab.png>)

0. Click the **+ New Definition** button (or the **green “plus” sign** or the **+ New** link), select **Empty**, and then click **Next >**.

 ![](<media/new_empty_build.png>)

0. Ensure the Team Project is selected as the **Repository source**, the appropriate repository (created in the previous step), select "Hosted" as the **Default agent queue**, and click the checkbox **Continuous integration (build whenever this branch is updated)**, then click **Create**.

 ![](<media/new_ci_build_definition.png>)

0. Click on the **Build** tab, click **Add build step...**

0. Click the **Add** button for the following build steps:
 
 0. Package/Nuget Installer
 0. Build/Visual Studio Build
 0. Test/Visual Studio Test
 0. Build/Index Sources & Publish Symbols
 0. Utility/Publish Build Artifacts

 ![](<media/build_add_steps.png>)

0. Select the **NuGet Installer** build step, and fill in the input values with the following:

 ```
 Name: NuGet restore
 Path to solution or packages.config: **\*.sln
 Installation type: Restore
 ```

 ![](<media/ci_build_step_nuget_restore.png>)

0. Select the **Visual Studio Build** build step, and fill in the input values with the following:

 ```
 Name: Build solution
 Path to solution or packages.config: **\*.sln
 MSBuild Arguments: /p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactstagingdirectory)\\"
 Platform: $(BuildPlatform)
 Configuration: $(BuildConfiguration)
 Clean: False
 Visual Studio Version: Visual Studio 2015
 ```

 ![](<media/ci_build_step_vs_build.png>)

0. Select the **Visual Studio Test** build step, and fill in the input values with the following:

 ```
 Name: Test Assemblies
 Execution Options/Test Assembly: **\$(BuildConfiguration)\*test*.dll;-:**\obj\**
 Advanced Execution Options/VSTest: Version
 Advanced Execution Options/VSTest Version: Visual Studio 2015
 ```

 ![](<media/ci_build_step_vs_test.png>)

0. Select the **Index Sources & Publish Symbols** build step, and fill in the input values with the following:

 ```
 Name: Publish symbols path
 Search pattern: **\bin\**\*.pdb
 Control Options/Continue on error: True
 ```

 ![](<media/ci_build_step_publish_symbols.png>)

0. Select the **Publish Build Artifacts** build step, and fill in the input values with the following:

 ```
 Name: Publish Artifact
 Path to Publish: $(build.artifactstagingdirectory)
 Artifact Name: drop
 Artifact Type: Server
 Control Options/Always run: True
 ```
 
 ![](<media/ci_build_step_publish_artifact.png>)

0. Click on the **Variables** tab, and fill in the input values with the following:

 ```
 system.debug: false                 [x] Allow at Queue Time
 BuildConfiguration: release         [x] Allow at Queue Time
 BuildPlatform: any cpu              [x] Allow at Queue Time
 ```

 ![](<media/ci_variables.png>)

0. Click on the **Save** button, name the build `VotingAppCI`, then click **OK**
  
 ![](<media/ci_save_dialog.png>)


## Release Build

0. Click on the **Releases** button

0. Click the **+ New Definition** button (or the **green “plus” sign** or the **+ New** link), select **Empty**, and then click **Next >**.

 ![](<media/new_empty_cd_build.png>)

0. Keep the artifacts as **Build**, select the CI build definition that you used in the previous step ("VotingAppCI"), check the checkbox to enable the **Continuous Deployment trigger**, and choose "Hosted" as the  **agent queue**.

 ![](<media/new_release_definition.png>)

0. Click on the **Environment 1** keyword and rename the environment to be **"Production"**.

0. Click on the pencil icon on the top of the definition and rename it to be `VotinAppCD`. 

 ![](<media/change_environment_name.png>)

0. Click **Add tasks**

0. Click the **Add** button for the following build tasks:
 
 0. Deploy/Azure App Service Deployment: ARM
 0. Test/Cloud-based Web Performance Test

 ![](<media/cd_build_add_steps.png>)

0. Select the **Azure App Service Deployment: ARM** build task

0. Click the **Manage** link next to the *AzureRM Subscription* field

 ![](<media/manage_azurerm_subscription_link.png>)

0. Click the **New Service Endpoint** menu, then click **Azure Resource Manager**

 ![](<media/new_azurerm_subscription_endpoint.png>)

0. Fill in the input values with the following, then click **OK**:

 ```
 Connection Name: VotingAppCD-AzureRM
 Subscription: {Choose your target Azure Subscription}
 ```

 ![](<media/add_azurerm_service_endpoint_dialog.png>)

0. Return to the CD build definition, click the **Refresh Icon** left of the *Manage* link and fill in the input values with the following:

 ```
 Name: Deploy Azure App Service
 AzureRM Subscription: VotingAppCD-AzureRM
 App Service Name: voting-app-*************
 Deploy to Slot: False
 Package or Folder: $(System.DefaultWorkingDirectory)\**\*.zip
 Additional Deployment Options/Publish using Web Deploy: True
 Output/Web App URL: $(WebAppUrl)
 ```

 ![](<media/cd_build_task_deploy_arm_app_service.png>)

0. Select the **Cloud-based Web Performance Test** build task

0. Click the **Manage** link next to the *Registered connection* field

 ![](<media/manage_vsts_endpoint_link.png>)

0. Click your Avatar in the upper right corner, then click **Security**

 ![](<media/user_security.png>)

0. Click **Personal access tokens**, then click **Add**

 ![](<media/personal_access_tokens.png>)

0. Enter `VotingAppCD-HOL` for the **Description**, ensure **All scopes** is selected, then click **Create Token**

 ![](<media/create_cd_build_personal_access_token.png>)

0. **Copy** the token value (DO NOT CLOSE THE WINDOW/TAB for the moment)

 ![](<media/copy_personal_access_token.png>)

0. Return to the CD build definition, and click the **Manage** link (again) next to the *Registered connection* field

0. Click the **New Service Endpoint** menu, then click **Generic**

 ![](<media/new_generic_service_endpoint.png>)

 ```
 Connection Name: VSTS
 Server URL: https://{VSTS instance name}.visualstudio.com
 User name: {your email address}
 Password/Token Key: {Paste the token you just created}
 ```

 ![](<media/add_new_generic_connection.png>)

0. Click **OK**

0. Return to the CD build definition, click the **Refresh Icon** left of the *Manage* link and fill in the input values with the following:

 ```
 Name: Quick Web Performance Test
 Registered connection: VSTS
 Website Url: $(WebAppUrl)
 Test Name: VotingApp LoadTest
 User Load: 25
 Run Duration (sec): 60
 Load Location: Default
 Run load test using: Automatically provisioned agents
 ```

 ![](<media/cd_build_task_load_test.png>)

0. Open **a new browser tab** and log into the Azure Portal at: [https://portal.azure.com](https://portal.azure.com)

0. Search for `voting-app` and choose the entry of type *App Service*

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/azure_portal_search_votingapp.png>)

0. **Copy** the link to the **URL** of the voting app

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/click_web_app_url_not_deployed.png>)
 

0. Return to VSTS

0. Click the ellipsis button for the **Production** environment, then click **Configure Variables...**

 ![](<media/open_environment_variables.png>)


0. Create a variable of name `WebAppUrl` and for the value, *paste the url of your Azure web application*, then click **OK**

 ![](<media/configure_cd_build_production_variables.png>)

0. Click the **Artifacts** tab for the release definition, then click **Link to an artifact source**

 ![](<media/cd_artifacts.png>)

0. Fill in the input values with the following, then click **Link**:

 ```
 Type: Build
 Project: VotingApp
 Source (Build definition): VotingAppCI
 Source alias: VotingAppCI
 ```
 ![](<media/link_an_artifact_source.png>)

0. Click the **Triggers** tab for the release definition

0. Ensure the **Continuous Deployment** checkbox is checked and set *Set trigger on artifact source* to **VotingAppCI**

 ![](<media/cd_triggers.png>)

0. Click the **Save** button on the release definition 

## [Optional Extra] Task# 4: Trigger a build 

0. Click on **Builds** at the top left of the page

0. Click the ellipsis for the *VotingAppCI* build, then click **Queue new build**

 ![](<media/queue_new_ci_build.png>)

0. Leave all default values and click **OK**

 ![](<media/trigger_new_ci_build.png>)

0. Look around the **Summary** section of your CI build once it is finised (~2 minutes). You have a ton of informations like Build details, Tests Results, Deployments (Release to *Production* environment)

0. Click the **Production** link in the *Deployments* section in the lower right section of CI Build
 ![](<media/ci_build_result.png>)

 [The CD build, because of the performance test can take around 3 minutes.]

0. After the CD build is completed, click the **Test** hub, then click **Load Test**

 ![](<media/after_cd_load_tests.png>)

0. Click **VotingApp LoadTest**, Select the entry and click **Open**

 ![](<media/open_load_test_results.png>)
 
0. See the results of the run

0. Copy the web site URL at the bottom of the load test run

 ![](<media/load_test_results.png>)

0. Browse to your web site url: (ie: http://voting-app-*************.azurewebsites.net/)

> You can see that the release pipeline we demoed do the following: Compile, Test Code, Deploy & Load Test 
 
## Teardown
0. In the Azure portal in the `voting-app-hol` resource group, click the **Delete** button.

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/delete_resource_group.png>)

0. Confirm the deletion

0. Browse to `https://{VSTS instance name}.visualstudio.com/_admin`

0. Click the **Overview** section, select the **VotingApp** team project, click the ellipsis and click **Delete**

 ![](<../HOL1_VotingApp-Simple-Continuous-Deployment/media/delete_votingapp_vsts_project.png>)

0. Confirm deletion

## Complementary information

* [DevOps Fundamentals](https://channel9.msdn.com/Series/DevOps-Fundamentals)