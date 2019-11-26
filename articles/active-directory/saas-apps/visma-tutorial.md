---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Visma | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Visma.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9de1cfb-5611-485e-ad9b-16be094afde6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4c4e71da2a0e736c989e52d2fcb6f9f8636551
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485583"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visma"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Visma

In this tutorial, you'll learn how to integrate Visma with Azure Active Directory (Azure AD). When you integrate Visma with Azure AD, you can:

* Control in Azure AD who has access to Visma.
* Enable your users to be automatically signed-in to Visma with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Vereisten

To get started, you need the following items:

* Een Azure AD-abonnement If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Visma single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeschrijving

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Visma supports **SP and IDP** initiated SSO
* Visma supports **Just In Time** user provisioning

## <a name="adding-visma-from-the-gallery"></a>Adding Visma from the gallery

To configure the integration of Visma into Azure AD, you need to add Visma from the gallery to your list of managed SaaS apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Visma** in the search box.
1. Select **Visma** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-visma"></a>Configure and test Azure AD single sign-on for Visma

Configure and test Azure AD SSO with Visma using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Visma.

To configure and test Azure AD SSO with Visma, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Visma SSO](#configure-visma-sso)** - to configure the single sign-on settings on application side.
    * **[Create Visma test user](#create-visma-test-user)** - to have a counterpart of B.Simon in Visma that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Visma** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.my.connect.visma.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.my.connect.visma.com/saml/acs`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.my.connect.visma.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Contact [Visma Client support team](https://www.visma.com/contact) to get these values. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. On the **Set up single sign-on with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Visma.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Visma**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-visma-sso"></a>Configure Visma SSO

To configure single sign-on on **Visma** side, you need to send the **App Federation Metadata Url** to [Visma support team](https://www.visma.com/contact). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-visma-test-user"></a>Create Visma test user

In this section, a user called B.Simon is created in Visma. Visma supports just-in-time user provisioning, which is enabled by default. Er is geen actie-item voor u in deze sectie. If a user doesn't already exist in Visma, a new one is created after authentication.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

When you click the Visma tile in the Access Panel, you should be automatically signed in to the Visma for which you set up SSO. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Visma with Azure AD](https://aad.portal.azure.com/)