---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Single Sign-on voor Skytap | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Single Sign-on voor Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565771"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Single Sign-on voor Skytap

In deze zelfstudie leert u hoe u Single Sign-on voor Skytap integreert met Azure Active Directory (Azure AD). Wanneer u Single Sign-on voor Skytap integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Single Sign-on voor Skytap.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Single Sign-on voor Skytap met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Eenmalige aanmelding voor Skytap-abonnement (Single Sign-on( SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Single Sign-on voor Skytap ondersteunt SP en IDP heeft SSO geïnitieerd.
* Nadat u Single Sign-on voor Skytap hebt geconfigureerd, u sessiebeheer afdwingen. Dit beschermt exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Eén aanmelding toevoegen voor Skytap vanuit de galerie

Als u de integratie van Single Sign-on voor Skytap in Azure AD wilt configureren, moet u Single Sign-on voor Skytap vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Ga naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen in de galerie** één **aanmelding voor Skytap** in het zoekvak.
1. Selecteer **Eenmalig aanmelden voor Skytap** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Azure AD-aanmelding configureren en testen voor eenmalig aanmelden voor Skytap

Azure AD SSO configureren en testen met Single Sign-on voor Skytap met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een gekoppelde relatie aanmaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Single Sign-on voor Skytap.

Hier volgen de algemene stappen om Azure AD SSO te configureren en te testen met Single Sign-on voor Skytap:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.

    a. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.

    b. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Stel single sign-on voor Skytap SSO](#configure-single-sign-on-for-skytap-sso)** in om de instellingen voor één aanmelding aan de toepassingszijde te configureren.

    a. **[Maak een single sign-on voor Skytap test gebruiker](#create-single-sign-on-for-skytap-test-user)** om een tegenhanger van B.Simon in Single Sign-on voor Skytap hebben. Deze tegenhanger is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Single Sign-on for Skytap-toepassingsintegratie** de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL die het volgende patroon gebruikt:`http://pingone.com/<custom EntityID>`

    b. Typ in het tekstvak **URL beantwoorden** een URL die het volgende patroon gebruikt:`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Selecteer **Extra URL's instellen**en voer de volgende stappen uit als u de toepassing wilt configureren in de gestarte **SP-modus:**

    a. Typ in het tekstvak **AANmeldings-URL** een URL die het volgende patroon gebruikt:`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Typ in het tekstvak **Relaystatus** een URL die het volgende patroon gebruikt:`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de URL van het antwoord, de URL van Sign-on en de relaystatus. Neem contact op met [het ondersteuningsteam voor skytap-klanten](mailto:support@skytap.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de xml **met aalmetagegevens**van federatie . Selecteer **Downloaden** om het metagegevensbestand te downloaden en op te slaan op uw computer.

    ![Schermafbeelding van de koppeling certificaatdownload](common/metadataxml.png)

1. Kopieer in de sectie **Eén aanmelding instellen voor Skytap** de juiste URL of URL's op basis van uw vereiste.

    ![Schermafbeelding van URL's voor kopieerconfiguratie](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Single Sign-on voor Skytap.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer in de lijst met toepassingen de optie **Eén aanmelding voor Skytap**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de gebruikerslijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Eén aanmelding configureren voor Skytap SSO

Als u eenmalige aanmelding wilt configureren aan de kant van de interne aanmelding voor Skytap, moet u de gedownloade **XML met federatiemetagegevens**en de juiste gekopieerde URL's van de Azure-portal naar het [ondersteuningsteam voor één aanmelding voor Skytap-client](mailto:support@skytap.com)verzenden. Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Eén aanmelding maken voor Skytap-testgebruiker

In deze sectie maakt u een gebruiker genaamd B.Simon in Single Sign-on voor Skytap. Werk samen met het [single sign-on voor Skytap Client support team](mailto:support@skytap.com) om de gebruikers toe te voegen in het Single Sign-on voor Skytap-platform. U geen enkele aanmelding gebruiken totdat u gebruikers maakt en activeert.

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw azure AD-configuratie voor eenmalige aanmelding met access panel.

Wanneer u de tegel Single Sign-on voor Skytap selecteert in het access-paneel, moet u automatisch zijn aangemeld bij de single sign-on voor Skytap waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangspaneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie .

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Slack uitproberen met Azure AD](https://aad.portal.azure.com/)

