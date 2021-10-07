# Tutorial: Custom policies for Phone Auth Azure Active Directory B2C For Microsoft Community Training

[Custom policies](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-policy-overview) are configuration files that define the behavior of your Azure Active Directory B2C (Azure AD B2C) tenant.
## Prerequisites

- If you don't have one already, [create an Azure AD B2C tenant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant) that is linked to your Azure subscription.
- [Register a web application](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga), and [enable ID token implicit grant](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga#enable-id-token-implicit-grant).

## Add signing and encryption keys

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directories + subscriptions** icon in the portal toolbar.
1. On the **Portal settings | Directories + subscriptions** page, find your Azure AD B2C directory in the **Directory name** list, and then select **Switch**.
1. In the Azure portal, search for and select **Azure AD B2C**.
1. On the overview page, under **Policies**, select **Identity Experience Framework**.

### Create the signing key

1. Select **Policy Keys** and then select **Add**.
1. For **Options**, choose `Generate`.
1. In **Name**, enter `TokenSigningKeyContainer`. The prefix `B2C_1A_` might be added automatically.
1. For **Key type**, select **RSA**.
1. For **Key usage**, select **Signature**.
1. Select **Create**.

### Create the encryption key

1. Select **Policy Keys** and then select **Add**.
1. For **Options**, choose `Generate`.
1. In **Name**, enter `TokenEncryptionKeyContainer`. The prefix `B2C_1A`_ might be added automatically.
1. For **Key type**, select **RSA**.
1. For **Key usage**, select **Encryption**.
1. Select **Create**.

## Register Identity Experience Framework applications

Azure AD B2C requires you to register two applications that it uses to sign up and sign in users with local accounts: *IdentityExperienceFramework*, a web API, and *ProxyIdentityExperienceFramework*, a native app with delegated permission to the IdentityExperienceFramework app. Your users can sign up with an email address or username and a password to access your tenant-registered applications, which creates a "local account." Local accounts exist only in your Azure AD B2C tenant.

You need to register these two applications in your Azure AD B2C tenant only once.

### Register the IdentityExperienceFramework application

To register an application in your Azure AD B2C tenant, you can use the **App registrations** experience.

1. Select **App registrations**, and then select **New registration**.
1. For **Name**, enter `IdentityExperienceFramework`.
1. Under **Supported account types**, select **Accounts in this organizational directory only**.
1. Under **Redirect URI**, select **Web**, and then enter `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, where `your-tenant-name` is your Azure AD B2C tenant domain name.
1. Under **Permissions**, select the *Grant admin consent to openid and offline_access permissions* check box.
1. Select **Register**.
1. Record the **Application (client) ID** for use in a later step.

Next, expose the API by adding a scope:

1. In the left menu, under **Manage**, select **Expose an API**.
1. Select **Add a scope**, then select **Save and continue** to accept the default application ID URI.
1. Enter the following values to create a scope that allows custom policy execution in your Azure AD B2C tenant:
    * **Scope name**: `user_impersonation`
    * **Admin consent display name**: `Access IdentityExperienceFramework`
    * **Admin consent description**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Select **Add scope**

* * *

### Register the ProxyIdentityExperienceFramework application

1. Select **App registrations**, and then select **New registration**.
1. For **Name**, enter `ProxyIdentityExperienceFramework`.
1. Under **Supported account types**, select **Accounts in this organizational directory only**.
1. Under **Redirect URI**, use the drop-down to select **Public client/native (mobile & desktop)**.
1. For **Redirect URI**, enter `myapp://auth`.
1. Under **Permissions**, select the *Grant admin consent to openid and offline_access permissions* check box.
1. Select **Register**.
1. Record the **Application (client) ID** for use in a later step.

Next, specify that the application should be treated as a public client:

1. In the left menu, under **Manage**, select **Authentication**.
1. Under **Advanced settings**, in the **Allow public client flows** section, set **Enable the following mobile and desktop flows** to **Yes**. Ensure that **"allowPublicClient": true** is set in the application manifest. 
1. Select **Save**.

Now, grant permissions to the API scope you exposed earlier in the *IdentityExperienceFramework* registration:

1. In the left menu, under **Manage**, select **API permissions**.
1. Under **Configured permissions**, select **Add a permission**.
1. Select the **My APIs** tab, then select the **IdentityExperienceFramework** application.
1. Under **Permission**, select the **user_impersonation** scope that you defined earlier.
1. Select **Add permissions**. As directed, wait a few minutes before proceeding to the next step.
1. Select **Grant admin consent for (your tenant name)**.
1. Select your currently signed-in administrator account, or sign in with an account in your Azure AD B2C tenant that's been assigned at least the *Cloud application administrator* role.
1. Select **Accept**.
1. Select **Refresh**, and then verify that "Granted for ..." appears under **Status** for the scopes - offline_access, openid and user_impersonation. It might take a few minutes for the permissions to propagate.

* * *

## Custom policy

1. [Download the .zip file](https://github.com/Indrasen-Singh/Native_ADB2C_PhoneAuth_MCT/archive/refs/heads/main.zip) or clone the repository:

    ```console
    git clone https://github.com/Indrasen-Singh/Native_ADB2C_PhoneAuth_MCT.git
    ```

1. In all of the files in the directory, replace the string `yourtenant` with the name of your Azure AD B2C tenant.

    For example, if the name of your B2C tenant is *contosotenant*, all instances of `yourtenant.onmicrosoft.com` become `contosotenant.onmicrosoft.com`.

### Add application IDs to the custom policy

Add the application IDs to the extensions file *Phone_Email_Base.xml*.

1. Open **`Phone_Email_Base.xml`** and find the element `<TechnicalProfile Id="login-NonInteractive">`.
1. Replace both instances of `ProxyIdentityExperienceFrameworkAppId` with the application ID of the ProxyIdentityExperienceFramework application that you created earlier.
1. Replace both instances of `IdentityExperienceFrameworkAppId` with the application ID of the IdentityExperienceFramework application that you created earlier.
1. In **`Phone_Email_Base.xml`**, replace `{insert your privacy statement URL}` and `{insert your terms and conditions URL}` with the appropriate URLs. Alternatively, delete the lines containing this text if you do not want these links shown on your phone signup/signin pages.
1. Save the file.

## Upload the policies

1. Select the **Identity Experience Framework** menu item in your B2C tenant in the Azure portal.
1. Select **Upload custom policy**.
1. In this order, upload the policy files:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ChangePhoneNumber.xml*

As you upload the files, Azure adds the prefix `B2C_1A_` to each.

## Test the custom policy

1. Under **Custom policies**, select **B2C_1A_SignUpOrSignInWithPhone**.
1. For **Select application** on the overview page of the custom policy, select the web application named *webapp1* that you previously registered.
1. Make sure that the **Reply URL** is `https://jwt.ms`.
1. Select **Run now**.
1. Sign up using phone number.
1. Select **Run now** again.
1. Sign in with the same account to confirm that you have the correct configuration.

## Reference

1. [Tutorial - Create user flows and custom policies - Azure Active Directory B2C | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-user-flows?pivots=b2c-custom-policy)

1. [Phone Number Passwordless](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)
## Next steps

You can also learn more in the [Azure AD B2C Architecture Deep Dive Series](https://www.youtube.com/playlist?list=PLOPotgzC07IKXXCTZcrpuLWbVe3y51kfm).