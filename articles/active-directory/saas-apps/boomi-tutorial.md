---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Boomi | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Boomi configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2469745edb5b8b3696478603cfe874bcabc8c1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231957"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Boomi

In this tutorial, you'll learn how to integrate Boomi with Azure Active Directory (Azure AD). When you integrate Boomi with Azure AD, you can:

* Control in Azure AD who has access to Boomi.
* Enable your users to be automatically signed-in to Boomi with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Vereisten

To get started, you need the following items:

* Een Azure AD-abonnement If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Boomi single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeschrijving

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Boomi biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-boomi-from-the-gallery"></a>Boomi toevoegen vanuit de galerie

Om de integratie van Boomi te configureren in Azure AD, moet u Boomi vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Boomi** in the search box.
1. Select **Boomi** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configure and test Azure AD single sign-on for Boomi

Configure and test Azure AD SSO with Boomi using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Boomi.

To configure and test Azure AD SSO with Boomi, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Boomi SSO](#configure-boomi-sso)** - to configure the single sign-on settings on application side.
    * **[Create Boomi test user](#create-boomi-test-user)** - to have a counterpart of B.Simon in Boomi that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Boomi** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. On the **Set up single sign-on with SAML** page, enter the values for the following fields:

    a. In het tekstvak **Id** typt u een URL: `https://platform.boomi.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van Boomi](https://boomi.com/company/contact/) om deze waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Boomi application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. In addition to above, Boomi application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.

    | Naam |  Bronkenmerk|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. On the **Set up Boomi** section, copy the appropriate URL(s) based on your requirement.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Boomi.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. Selecteer **Boomi** in de lijst met toepassingen.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-boomi-sso"></a>Configure Boomi SSO

1. In a different web browser window, sign in to your Boomi company site as an administrator.

1. Ga naar **Company Name** en daarna naar **Set up**.

1. Klik op het tabblad **SSO Options** en voer de onderstaande stappen uit.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Schakel het selectievakje **Enable SAML Single Sign-On** in.

    b. Klik op **Import** om het gedownloade certificaat van Azure AD te uploaden naar **Identity Provider Certificate**.

    c. Plak in het tekstvak **Identity Provider Login URL** de waarde van **Aanmeldings-URL** uit het configuratievenster van de toepassing in Azure AD.

    d. Schakel bij **Federation Id Location** het keuzerondje **Federation Id is in FEDERATION_ID Attribute element** in.

    e. Klik op de knop **Save**.

### <a name="create-boomi-test-user"></a>Een testgebruiker maken voor Boomi

In order to enable Azure AD users to sign in to Boomi, they must be provisioned into Boomi. In het geval van Boomi is dat een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Sign in to your Boomi company site as an administrator.

1. Ga na het aanmelden naar **User Management** en **Users**.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_001.png "Gebruikers")

1. Klik op het pictogram **+** om het dialoogvenster **Add / Maintain User Roles** te openen.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_002.png "Gebruikers")

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_003.png "Gebruikers")

    a. Typ in het tekstvak **User e-mail address** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. In the **First name** textbox, type the First name of user like B.

    c. Typ in het tekstvak **Last name** de achternaam van de gebruiker, zoals Simon.

    d. Typ een waarde voor de gebruiker in het vak **Federation ID**. Elke gebruiker moet een federatie-id hebben die de gebruiker uniek identificeert binnen het account.

    e. Wijs de rol **Standard User** toe aan de gebruiker. Do not assign the Administrator role because that would give them normal Atmosphere access as well as single sign-on access.

    f. Klik op **OK**.

    > [!NOTE]
    > The user will not receive a welcome notification email containing a password that can be used to log in to the AtomSphere account because their password is managed through the identity provider. You may use any other Boomi user account creation tools or APIs provided by Boomi to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Boomi klikt, wordt u automatisch aangemeld bij de instantie van Boomi waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Boomi with Azure AD](https://aad.portal.azure.com/)