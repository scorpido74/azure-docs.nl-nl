---
title: 'Tutorial: Azure Active Directory integration with Adaptive Insights | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232127"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integrate Adaptive Insights with Azure Active Directory

In this tutorial, you'll learn how to integrate Adaptive Insights with Azure Active Directory (Azure AD). When you integrate Adaptive Insights with Azure AD, you can:

* Control in Azure AD who has access to Adaptive Insights.
* Enable your users to be automatically signed-in to Adaptive Insights with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Vereisten

To get started, you need the following items:

* Een Azure AD-abonnement If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Adaptive Insights single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeschrijving

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Adaptive Insights supports **IDP** initiated SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adding Adaptive Insights from the gallery

To configure the integration of Adaptive Insights into Azure AD, you need to add Adaptive Insights from the gallery to your list of managed SaaS apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Adaptive Insights** in the search box.
1. Select **Adaptive Insights** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configure and test Azure AD SSO with Adaptive Insights using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Adaptive Insights.

To configure and test Azure AD SSO with Adaptive Insights, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
2. **[Configure Adaptive Insights SSO](#configure-adaptive-insights-sso)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
5. **[Create Adaptive Insights test user](#create-adaptive-insights-test-user)** - to have a counterpart of B.Simon in Adaptive Insights that is linked to the Azure AD representation of user.
6. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Adaptive Insights** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > You can get Identifier(Entity ID) and Reply URL values from the Adaptive Insights’s **SAML SSO Settings** page.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. On the **Set up Adaptive Insights** section, copy the appropriate URL(s) based on your requirement.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Configure Adaptive Insights SSO

1. In a different web browser window, sign in to your Adaptive Insights company site as an administrator.

2. Ga naar **Administration**.

    ![Admin](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. In the **Users and Roles** section, click **SAML SSO Settings**.

    ![Manage SAML SSO Settings](./media/adaptivesuite-tutorial/ic805645.png "Manage SAML SSO Settings")

4. On the **SAML SSO Settings** page, perform the following steps:

    ![SAML SSO Settings](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO Settings")

    a. In the **Identity provider name** textbox, type a name for your configuration.

    b. Paste the **Azure AD Identifier** value copied from Azure portal into the **Identity provider Entity ID** textbox.

    c. Paste the **Login URL** value copied from Azure portal into the **Identity provider SSO URL** textbox.

    d. Paste the **Logout URL** value copied from Azure portal into the **Custom logout URL** textbox.

    e. To upload your downloaded certificate, click **Choose file**.

    f. Select the following, for:

     * **SAML user id**, select **User’s Adaptive Insights user name**.

     * **SAML user id location**, select **User id in NameID of Subject**.

     * **SAML NameID format**, select **Email address**.

     * **Enable SAML**, select **Allow SAML SSO and direct Adaptive Insights login**.

    g. Copy **Adaptive Insights SSO URL** and paste into the **Identifier(Entity ID)** and **Reply URL** textboxes in the **Basic SAML Configuration** section in the Azure portal.

    h. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. In the **User** properties, follow these steps:
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. In the **User name** field, enter the username@companydomain.extension. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Adaptive Insights.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Adaptive Insights**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-adaptive-insights-test-user"></a>Create Adaptive Insights test user

To enable Azure AD users to sign in to Adaptive Insights, they must be provisioned into Adaptive Insights. In the case of Adaptive Insights, provisioning is a manual task.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Sign in to your **Adaptive Insights** company site as an administrator.

2. Ga naar **Administration**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. In the **Users and Roles** section, click **Users**.

   ![Add User](./media/adaptivesuite-tutorial/IC805648.png "Gebruiker toevoegen")

4. In the **New User** section, perform the following steps:

   ![Verzenden](./media/adaptivesuite-tutorial/IC805649.png "Verstuur")

   a. Type the **Name**, **Username**, **Email**, **Password** of a valid Azure Active Directory user you want to provision into the related textboxes.

   b. Select a **Role**.

   c. Klik op **Submit**

> [!NOTE]
> You can use any other Adaptive Insights user account creation tools or APIs provided by Adaptive Insights to provision Azure AD user accounts.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

When you click the Adaptive Insights tile in the Access Panel, you should be automatically signed in to the Adaptive Insights for which you set up SSO. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

