---
title: 'Zelfstudie: Azure Active Directory-integratie met Jive | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccdab373be4bab876ef52ba478076b6a8b6e0845
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76291172"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Jive

In deze zelfstudie leert u hoe u Jive integreert met Azure Active Directory (Azure AD). Wanneer u Jive integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Jive.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Jive met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Jive single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Jive steunt **SP** geïnitieerde SSO
* Jive ondersteunt [ **geautomatiseerde** gebruikersinrichting](jive-provisioning-tutorial.md)
* Zodra u de Jive configureert, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Jive toevoegen vanuit de galerie

Als u de integratie van Jive in Azure AD wilt configureren, moet u Jive uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Jive** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Jive** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Azure AD-aanmelding voor Jive configureren en testen

Azure AD SSO configureren en testen met Jive met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Jive.

Als u Azure AD SSO met Jive wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Jive SSO](#configure-jive-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Jive-testgebruiker](#create-jive-test-user)** - om een tegenhanger van B.Simon in Jive te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Jive-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<instance name>.jivecustom.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<instance name>.jiveon.com`

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Jive Client support team](https://www.jivesoftware.com/services-support/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Jive instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Jive.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Jive**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-jive-sso"></a>Jive SSO configureren

1. Als u eenmalige aanmelding aan **jive-zijde** wilt configureren, meldt u zich aan bij uw Jive-tenant als beheerder.

1. Klik in het menu bovenaan op **SAML**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecteer **Ingeschakeld** onder het tabblad **Algemeen.**

    b. Klik op de knop **ALLE SAML-INSTELLINGEN OPSLAAN.**

1. Navigeer naar het tabblad **IDP METADATA.**

    ![Eenmalige aanmelding aan app-zijde configureren](./media/jive-tutorial/tutorial_jive_003.png)

    a. Kopieer de inhoud van het xml-bestand met metagegevens met aalmetten en plak het vervolgens in het tekstvak **metagegevens (Identity Provider) Metadata.**

    b. Klik op de knop **ALLE SAML-INSTELLINGEN OPSLAAN.**

1. Selecteer tabblad **TOEWIJZING VAN GEBRUIKERSKENMERKEN.**

    ![Eenmalige aanmelding aan app-zijde configureren](./media/jive-tutorial/tutorial_jive_004.png)

    a. Kopieer en plak in het **tekstvak E-mail** de kenmerknaam van **de e-mailwaarde.**

    b. Kopieer en plak in het tekstvak **Voornaam** de kenmerknaam van de waarde van **de voornaam.**

    c. Kopieer en plak in het tekstvak **Achternaam** de naam **van** achternaamwaarde.

### <a name="create-jive-test-user"></a>Jive-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in Jive. Jive ondersteunt automatische gebruikersinrichting, die standaard is ingeschakeld. U kunt [hier](jive-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

Als u handmatig een gebruiker wilt maken, werkt u samen met [jive-ondersteuningsteam](https://www.jivesoftware.com/services-support/) om de gebruikers toe te voegen aan het Jive-platform.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Jive in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Jive waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Jive met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Gebruikersinrichting configureren](jive-provisioning-tutorial.md)

- [Hoe jive te beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
