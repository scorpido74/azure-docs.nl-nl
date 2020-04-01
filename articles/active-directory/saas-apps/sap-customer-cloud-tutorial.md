---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met SAP Cloud voor de klant | Microsoft Documenten'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72264062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SAP Cloud for Customer

In deze zelfstudie leert u hoe u SAP Cloud for Customer integreert met Azure Active Directory (Azure AD). Wanneer u SAP Cloud for Customer integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot SAP Cloud voor de klant.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Cloud voor klanten met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SAP Cloud for Customer single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SAP Cloud for Customer ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP Cloud for Customer uit de galerie toevoegen

Voor het configureren van de integratie van SAP Cloud for Customer met Azure AD moet u SAP Cloud for Customer uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SAP Cloud voor klant in** het zoekvak in de sectie Toevoegen in de **galerie.**
1. Selecteer **SAP Cloud voor klant** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Azure AD-singlesign-on configureren en testen voor SAP Cloud voor klant

Azure AD SSO configureren en testen met SAP Cloud voor klant met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Cloud for Customer.

Als u Azure AD SSO wilt configureren en testen met SAP Cloud voor de klant, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer SAP Cloud voor Klant SSO](#configure-sap-cloud-for-customer-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[SAP Cloud for Customer test user](#create-sap-cloud-for-customer-test-user)** - to have a counterpart of B.Simon in SAP Cloud for Customer that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SAP Cloud for Customer-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server name>.crm.ondemand.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [SAP Cloud for Customer-ondersteuningsteam](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. SAP Cloud for Customer-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Voer in de sectie **Gebruikerskenmerken** in het dialoogvenster **Gebruikerskenmerken en claims** de volgende stappen uit:

    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![installatiekopie](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecteer **Transformatie** als **bron**.

    c. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    d. Selecteer in de lijst **Parameter 1** het gebruikerskenmerk dat u wilt gebruiken voor uw implementatie.
    Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    e. Klik op **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **SAP Cloud for Customer instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SAP Cloud voor de klant.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer SAP Cloud **for Customer**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP Cloud configureren voor Klant SSO

1. Open een nieuw webbrowservenster en meld u aan bij uw SAP Cloud for Customer-bedrijfssite als beheerder.

2. Klik links van het menu op **Identity Providers** > **Corporate Identity Providers** > **Toevoegen** en voeg in de pop-up de naam van de identiteitsprovider toe zoals **Azure AD**, klik op **Opslaan** en klik vervolgens op **SAML 2.0 Configuration**.

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure01.png)

3. Voer in de sectie **SAML 2.0-configuratie** de volgende stappen uit:

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klik **op Bladeren** om het XML-bestand Met aalmetten van federatiemetagegevens te uploaden, dat u hebt gedownload van azure-portal.

    b. Zodra het XML-bestand is geüpload, worden de onderstaande waarden automatisch ingevuld en klikt u op **Opslaan**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Een SAP Cloud for Customer-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij SAP Cloud for Customer, moeten ze worden ingericht in SAP Cloud for Customer. In SAP Cloud for Customer is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij SAP Cloud voor klant als beveiligingsbeheerder.

2. Klik aan de linkerkant van het menu op **Gebruikers & Autorisaties** > **Gebruikersbeheer Gebruikersbeheer** > **toevoegen.**

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure03.png)

3. Voer in de sectie **Nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Voer in het tekstvak **Voornaam** de naam van de gebruiker in zoals **B**.

    b. Voer in het tekstvak **Achternaam** de naam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** `B.Simon@contoso.com`de e-mail van de gebruiker in, zoals .

    d. Voer in het tekstvak **Inlognaam** de naam van de gebruiker in, zoals **B.Simon.**

    e. Selecteer **Gebruikerstype** volgens uw vereiste.

    f. Selecteer de optie **Accountactivering** volgens uw vereiste.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Cloud for Customer klikt in het toegangsvenster, wordt u automatisch aangemeld bij het exemplaar van SAP Cloud for Customer waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Sap Cloud voor klant uitproberen met Azure AD](https://aad.portal.azure.com/)

