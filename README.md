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


## Custom policy

1. [Download the .zip file](https://github.com/Indrasen-Singh/Native_ADB2C_PhoneAuth_MCT/archive/refs/heads/main.zip) or clone the repository:

    ```console
    git clone https://github.com/Indrasen-Singh/Native_ADB2C_PhoneAuth_MCT.git
    ```

1. In all of the files in the directory, replace the string `yourtenant` with the name of your Azure AD B2C tenant.

    For example, if the name of your B2C tenant is *contosotenant*, all instances of `yourtenant.onmicrosoft.com` become `contosotenant.onmicrosoft.com`.

1. In **`Phone_Email_Base.xml`**, replace `{insert your privacy statement URL}` and `{insert your terms and conditions URL}` with the appropriate URLs. Alternatively, delete the lines containing this text if you do not want these links shown on your phone signup/signin pages.

1. Save the file.

## Upload the policies

1. Select the **Identity Experience Framework** menu item in your B2C tenant in the Azure portal.
1. Select **Upload custom policy**.
1. In this order, upload the policy files:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*


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