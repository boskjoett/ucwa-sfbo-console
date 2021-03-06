# Create Skype for Business Online Apps and Meetings using the UCWA API

This example demonstrates how to create Skype for Business Online (SfBO) apps and meetings using UCWA - Microsoft’s Unified Communications Web API

  * Using the Skype for Business Online AppSDK? We'll show you how to create Meeting URLs that you can provide to the iOS and Android AppSDK

Many of you have created Skype for Business On-Premise integrations using UCWA.  This interactive, console-based .NET application will demonstrate how you can do the same using Skype for Business Online.  You'll also see how you can accept user credentials using a platform-specific, pre-built dialog box.  Deploying to device without a web browser?  No problem!  You can show a message that tells users to visit a website on another device where they can enter a unique code to begin sign in.  Direct username and password entry to a console is demonstrated as well.

Skype for Business Online works with Azure Active Directory to perform user authentication.  We'll be using the methods described [here](http://www.cloudidentity.com/blog/2014/07/08/using-adal-net-to-authenticate-users-via-usernamepassword/) to perform direct username and password authentication using the [ADAL library](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), which also provides access to dialog box based-authentication as well as [device code](http://www.cloudidentity.com/blog/2015/12/02/new-adal-3-x-previewdevice-profile-linux-and-os-x-sample/) initiated sign-in.

#### Breaking Change 3/20/2017

An instance of System.Net.Http.HttpClient no longer needs to be passed to the methods in UcwaSfbo (i.e. UcwaApplications.CreateUcwaApps). Instead, there is a new shared instance (Helpers.SharedHttpClient) residing in Utils.cs as that they all now reference.  

## UCWA for Skype for Business Online Demistified

Our sample application and code will demonstrate how you can perform the following operations using UCWA and Skype for Business Online.

* Authentication and Autodiscovery: The inputs and results of Azure AD and Skype for Business Online UCWA backend calls will be displayed so you can understand the flow. 
* Application Creation: We'll automatically create a SfB Online application on your behalf, which will serve as the jump point for making UCWA API calls related to Online Meetings, Contact Management, and Presence
* Online Meetings: Create / List / Delete an online meeting
* Contact Management: List all contacts, and add and delete a contact
* Presence: Make a user available with a specific presence value.

Future versions of this example may provide a related nuget package.  Watch us for updates!   In the meantime, use our example to begin migrating your Skype for Business On-Premise apps to Skype for Business Online.

## Setup a development environment

* Clone this GitHub repository.
* Install Visual Studio 2015.  Don't have it?  Download the free [Visual Studio Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)

## Identify a test user account

* Sign in to your Office 365 environment as an administrator at [https://portal.office.com/admin/default.aspx](https://portal.office.com/admin/default.aspx)
* Find a user whose account you'd like to use with this example
  * Ensure the user's "Product licenses" gives them access to SfBO.  
  * Disable any multi-factor authentication for this account if you're using direct username and password authentication.
  * If you're resetting a user's password, make sure you un-select "Make this user change their password ..."
  * If you're creating a new user or assigning a license to an existing user, you'll need to wait an hour before they can sign in to SfBO (and thus, this example).

## Create a SfBO Application in Azure Active Directory

* Sign in to your Azure Management Portal at https://portal.azure.com
* Select Active Directory -> App registrations -> New application registration
    * Name: SfboUcwa (anything will work)
    * Application type: Native
    * Redirect URI: http://demo-sfbo-ucwa (anything will work)
* Once Azure has created your app, copy your Client ID and give your application access to SfBO.  
   * Click your app's name (i.e. SfboUcwa) from the list of applications
   * Copy the Application ID (Client ID)
   * Click All settings -> Required Permissions
     * Click Add 
     * Select an API -> Skype for Business Online (Microsoft.Lync) -> Select
     * Select permissions -> Select all Delegated Permissions
       * Initiate conversations and join meetings
       * Create Skype Meetings
       * Read/write Skype user information (preview)
       * Receive conversation invites (preview)
       * Read/write Skype user contacts and groups
   * Click Select
   * Click Done
* Take note of your tenant name, which is typically in the form of your-domain.onmicrosoft.com. You'll need to supply this when building or running the app.   
* Applications built using the UCWA API for Skype for Business Online may require administrative consent before users can sign in - which fortunately, you'll only need to do once. If you have trouble signing in:
  * Whenever you successfully launch UcwaSfboConsole, we'll show you the URL you can visit to provide admin consent.  Sign in as the admin for the O365 tenant you've configured UcwaSfboConsole to work with. 
* Alternatively, you can perform the steps here as an admin to enable users to sign in:
  * https://msdn.microsoft.com/en-us/skype/websdk/docs/developwebsdkappsforsfbonline#tenant-administrator-consent-flow
* Note: if you've configured the re-direct URL to be the same value as we've shown you on this page (i.e. http://demo-sfbo-ucwa), you'll be sent to an invalid page after successfully signing in.  
  
## Build and Run UcwaSfboConsole

* Open the cloned code from this repository in Visual Studio
* Update Program.cs in the UcwaSfboConsole folder with your Tenant name (tenant) and Application Id / Client ID (clientId) 
  * Or, leave these values empty and provide them at the command line like so: 
    * UcwaSfboConsole mytenant.onmicrosoft.com my-alphanumeric-client-id
* Optionally, you can hard code the username (i.e. username@your-domain.onmicrosoft.com) and password you want to use in the variables called hardcodedUsername and hardcodedPassword
* Optionally, if you want to try dialog box authentication, update the redirectUri variable to be the value of Redirect URI you provided to Azure AD (i.e. http://demo-sfo-ucwa)
  * Or, you can provide the Redirect URI as a third command line parameter, like so:
    * UcwaSfboConsole mytenant.onmicrosoft.com my-alphanumeric-client-id http://demo-sfo-ucwa
* Build and run the app: UcwaSfboConsole

## Using UcwaSfboConsole
 
You'll be presented with a number of options.  Begin with "login".  Choose if you want to enter credentials of the user you identified earlier in "console" or "dialog" mode.  Enter "code" if you want to tell the user to visit a web page and type in the code shown before providing their credentials.

After you successfully login, you'll see the output of the UCWA Autodiscovery flow.
 
Then, you can type "meeting" to see the flow related to creating, listing, and deleting a meeting using UCWA's MyOnlineMeeting resource.  "Presence" will first make a call to UCWA's MakeMeAvailable resource and, if necessary, Presence to set your presence to one of the displayed values.  "Contact" will list your contacts, and give you the option to add or delete a contact.

## Questions and comments

We'd love to get your feedback about this sample. You can send your questions and suggestions to us in the Issues section of this repository.

Questions about Skype for Business development in general should be posted to [Stack Overflow](http://stackoverflow.com/questions/tagged/skype-for-business). Make sure that your questions or comments are tagged with [skype-for-business].

## Additional resources

* [Developing UCWA applications for Skype for Business Online](https://msdn.microsoft.com/en-us/skype/ucwa/developingucwaapplicationsforsfbonline)
* [Using ADAL .NET to Authenticate Users via Username/Password](http://www.cloudidentity.com/blog/2014/07/08/using-adal-net-to-authenticate-users-via-usernamepassword/)
* [How to get an Azure Active Directory tenant](https://azure.microsoft.com/en-us/documentation/articles/active-directory-howto-tenant/)
* [Skype Developer Platform](https://dev.office.com/skype)
* [Geting Started - Skype for Business Online](https://dev.office.com/skype/getting-started)

## Copyright

Copyright (c) 2017 Tam Huynh. All rights reserved. 


### Disclaimer ###
**THIS CODE IS PROVIDED *AS IS* WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING ANY IMPLIED WARRANTIES OF FITNESS FOR A PARTICULAR PURPOSE, MERCHANTABILITY, OR NON-INFRINGEMENT.**
