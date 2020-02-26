# Hybrid Identity - Password single sign-on for an Azure AD gallery application


## Lab Concepts	

In this lab you are going to publish an application (Twitter) from the Azure AD Application Gallery to select users and configure password-based single sign-on. Azure AD will securely store and replay the  usernames and passwords for Twitter.  

After this lab, you should understand how to:
1. Add an enterprise application to an AAD tenant
2. Implement B2C SSO for 3rd party applications


## Exercise 1 - Add some test users

We need to create some users in order to learn about application provisioning to users and groups.

1. From the Azure Portal, Click **Azure Active Directory**.  Ensure that you **DO NOT** have your default directory selected but rather the directory that has *.onmicrosoft.com in the namespace.
2. Click **Groups**, then **+New Group**, and enter the following:
    * Group Name: Twitter Authorized
    * Group Description: Users allowed to have SSO with Twitter
    * Click **Create**
3. Click your **Azure Active Directory**, **Users**, and then **+New User**, and enter the following:
    * User name: NoTweet
    * Name: TwitterNo
    * Password: Select **Let me create the password** and enter `Temp.Password`
    * Click **Create**
4. Click  **+New User** and enter the following:
    * User name: YesTweet
    * Name: TwitterYes
    * Password: Select **Let me create the password** and enter `Temp.Password`
    * Under Groups click **0 groups selected**.  Highlight and select  **Twitter Authorized** and then click **Select**.
    * Click **Create**

## Exercise 2 - Add Twitter from the Azure AD gallery

To add Twitter from the Azure AD gallery, follow these steps:

1. Click your **Azure Active Directory**, **Enterprise Applications**, **+New  Application**, and enter the following:
    * Enter **Twitter** in the **Add from the gallery** search box.
    * Select **Twitter** and then select **Add** to add the application. After a short period, you can see the application’s configuration pane.

## Exercise 3 - Configure Twitter for password single sign-on

To configure single sign-on for Twitter, follow these steps:

1. Select **Single sign-on** from the left menu under **Manage**.
2. Click on **Password-based** mode and then click **Save**.

## Exercise 4 - Assign an application to a group directly

In order to assign an application to a group we need change the version of Azure AD from the free version to Premium P2.

### Change Azure AD Version

1. Click on your **Azure Active Directory** in the Azure Portal.
2. Select **Getting started** and then **Get a free trial for Azure AD Premium**.
3. Expand **Free trial** under **AZURE AD PREMIUM P2** and then click **Activate**.
4. Select **Overview** and then refresh your browser,  Ensure that your Azure AD version reports as **Azure AD Premium P2** before continuing.  It may take several minutes for the upgrade to go active.

### Assign Groups

To assign one or more groups to an application directly, follow these steps:

1. Select **Enterprise Applications** under **Manage** and then select **Twitter**.
2. Click on **Users and Groups** from the menu on the left and select the **+Add user** button at the top of the pane.
3. If you see the message ***Groups are not available for assignment due to your Active Directory plan level*** then click on the error message and activate **Azure AD Premium P2**.  It may take 5-8 minutes for the upgrade to go live.
4. Select **Users and groups**  from the Add Assignment pane and then select **Twitter Authorized**.
5. When you're finished selecting groups, use the **Select** button to add them to the list of users and groups to be assigned to the application.
6. Select **Assign** to assign the application to the selected groups. After a short period, the users you've selected should be able to start these applications from the Access Panel.

## Exercise 5 - Test Access to Twitter

You are now going to logon as TwitterYes to configure your SSO credentials to validate functionality.  You will then logon as TwitterNo to see the difference.

### Confirm SSO Functionality

1. Open a web browser with an InPrivate or InCognito tab and go to <https://myapps.microsoft.com>, the My Apps portal.
2. Logon as TwitterYes (yestweet@XXXX.onmicrosoft.com).  You will be prompted to change your temporary password.  Use `Complex.Password`.
3. Upon logon you should see Twitter listed.  Click on **Twitter**.
4. Install the MyApps Secure Sign-in Extension.  This extension helps you start any of your organization's cloud apps that require you to use a single sign-on process.  **Install now** the extension when prompted.
5. Click **Twitter** again and enter your Twitter credentials.  Azure AD will securely store and replay your username and password for Twitter moving forward.  This is a one-time-only entry of your credentials.  Click **Sign In** when done.
6. Close and then re-open a web browser with an InPrivate or InCognito tab and go to <https://myapps.microsoft.com>.  
7. Click **Twitter** and validate your logon if necessary.  Notice that you are taken directly to Twitter without having to enter your Twitter credentials.
8. Close the Twitter tab and on the MyApps portal page logout.

### Validate Application Restrictions

1. Logon to <https://myapps.microsoft.com>, the My Apps portal, as TwitterNo (notweet@XXXX.onmicrosoft.com).
2. You will be prompted to change your temporary password.  Use *Complex.Password*.
3. Notice that Twitter is not presented to the user.

<br></br>
[Back to Table of Contents](./index.md#2-identity)