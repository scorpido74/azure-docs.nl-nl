---
title: 'Tutorial: Azure Active Directory integration with New Relic | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233523"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Azure Active Directory integration with New Relic

In this tutorial, you learn how to integrate New Relic with Azure Active Directory (Azure AD).
Integrating New Relic with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to New Relic.
* You can enable your users to be automatically signed-in to New Relic (Single Sign-On) with their Azure AD accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

To configure Azure AD integration with New Relic, you need the following items:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* New Relic single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* New Relic supports **SP** initiated SSO

## <a name="adding-new-relic-from-the-gallery"></a>Adding New Relic from the gallery

To configure the integration of New Relic into Azure AD, you need to add New Relic from the gallery to your list of managed SaaS apps.

**To add New Relic from the gallery, perform the following steps:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In the search box, type **New Relic**, select **New Relic** from result panel then click **Add** button to add the application.

     ![New Relic in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In this section, you configure and test Azure AD single sign-on with New Relic based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in New Relic needs to be established.

To configure and test Azure AD single sign-on with New Relic, you need to complete the following building blocks:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configure New Relic Single Sign-On](#configure-new-relic-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Create New Relic test user](#create-new-relic-test-user)** - to have a counterpart of Britta Simon in New Relic that is linked to the Azure AD representation of user.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

To configure Azure AD single sign-on with New Relic, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **New Relic** application integration page, select **Single sign-on**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![New Relic Domain and URLs single sign-on information](common/sp-identifier.png)

    a. In the **Sign on URL** text box, type a URL using the following pattern: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` - Be sure to substitute your own New Relic Account ID.

    b. Typ een URL in het vak **Id (Entiteits-id)** : `rpm.newrelic.com`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. On the **Set up New Relic** section, copy the appropriate URL(s) as per your requirement.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-new-relic-single-sign-on"></a>Configure New Relic Single Sign-On

1. In a different web browser window, sign on to your **New Relic** company site as administrator.

2. In the menu on the top, click **Account Settings**.
   
    ![Account Settings](./media/new-relic-tutorial/ic797036.png "Account Settings")

3. Click the **Security and authentication** tab, and then click the **Single sign on** tab.
   
    ![Single Sign-On](./media/new-relic-tutorial/ic797037.png "Eenmalige aanmelding")

4. On the SAML dialog page, perform the following steps:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Click **Choose File** to upload your downloaded Azure Active Directory certificate.

    b. In the **Remote login URL** textbox,  paste the value of **Login URL**, which you have copied from Azure portal.
   
    c. In the **Logout landing URL** textbox, paste the value of **Logout URL**, which you have copied from Azure portal.

    d. Click **Save my changes**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In the **User name** field type **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In this section, you enable Britta Simon to use Azure single sign-on by granting access to New Relic.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **New Relic**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. In the applications list, select **New Relic**.

    ![The New Relic link in the Applications list](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-new-relic-test-user"></a>Create New Relic test user

In order to enable Azure Active Directory users to log in to New Relic, they must be provisioned into New Relic. In the case of New Relic, provisioning is a manual task.

**To provision a user account to New Relic, perform the following steps:**

1. Log in to your **New Relic** company site as administrator.

2. In the menu on the top, click **Account Settings**.
   
    ![Account Settings](./media/new-relic-tutorial/ic797040.png "Account Settings")

3. In the **Account** pane on the left side, click **Summary**, and then click **Add user**.
   
    ![Account Settings](./media/new-relic-tutorial/ic797041.png "Account Settings")

4. On the **Active users** dialog, perform the following steps:
   
    ![Active Users](./media/new-relic-tutorial/ic797042.png "Actieve gebruikers")
   
    a. In the **Email** textbox, type the email address of a valid Azure Active Directory user you want to provision.

    b. As **Role** select **User**.

    c. Click **Add this user**.

>[!NOTE]
>You can use any other New Relic user account creation tools or APIs provided by New Relic to provision Azure AD user accounts.
> 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

When you click the New Relic tile in the Access Panel, you should be automatically signed in to the New Relic for which you set up SSO. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

