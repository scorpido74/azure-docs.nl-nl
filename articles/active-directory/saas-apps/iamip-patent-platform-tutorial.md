---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met IamIP Patent Platform | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en IamIP Patent Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190735"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met IamIP Patent Platform

In deze zelfstudie leert u hoe u het IamIP-octrooiplatform integreren met Azure Active Directory (Azure AD). Wanneer u IamIP Patent Platform integreert met Azure AD, u het:

* Gebruik Azure AD om te bepalen wie toegang heeft tot het IamIP Patent Platform.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij IamIP Patent Platform met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een IamIP Patent Platform abonnement met single sign-on (SSO) ingeschakeld.

## <a name="tutorial-description"></a>Beschrijving van zelfstudie

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

IamIP Patent Platform ondersteunt door SP geïnitieerde en IDP-geïnitieerde SSO.

Nadat u het IamIP Patent Platform hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>IamIP Patent Platform toevoegen vanuit de galerij

Om de integratie van het IamIP Patent Platform in Azure AD te configureren, moet u IamIP Patent Platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Enterprise-toepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **IamIP Patent Platform** in het zoekvak in de sectie **Toevoegen vanuit de galerij.**
1. Selecteer **IamIP Patent Platform** in het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Azure AD SSO configureren en testen voor IamIP-patentplatform

U configureert en test Azure AD SSO met IamIP Patent Platform met behulp van een testgebruiker genaamd B.Simon. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in het IamIP-octrooiplatform.

Als u Azure AD SSO wilt configureren en testen met iamIP-patentplatform, neemt u de volgende stappen op hoog niveau:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers de functie kunnen gebruiken.
    * **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
    * **[Geef toegang tot de testgebruiker](#grant-access-to-the-test-user)** om de gebruiker in staat te stellen Azure AD-enkele aanmelding te gebruiken.

1. **[IamIP Patent Platform SSO configureren](#configure-iamip-patent-platform-sso)** aan de toepassingszijde.
    * **[Maak een IamIP Patent Platform testgebruiker](#create-iamip-patent-platform-test-user)** als tegenhanger van de Azure AD-weergave van de gebruiker.

1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg de volgende stappen om Azure AD SSO in te schakelen in de Azure-portal:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **IamIP Patent** Platform-toepassingsintegratie in de sectie **Beheren** de optie **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** de potloodknop voor **BasisSAML-configuratie** om de instellingen te bewerken:

   ![Potloodknop voor basisSAML-configuratie](common/edit-urls.png)

1. Neem in de sectie **BasisSAML-configuratie** de volgende stappen als u een metagegevensbestand van serviceprovider hebt en SSO in de idp-modus wilt configureren:

    a. Selecteer **Metagegevensbestand uploaden:**

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Selecteer de mapknop, selecteer het metagegevensbestand en selecteer **Uploaden:**

    ![Knoppen Map en Upload](common/browse-upload-metadata.png)

    c. Nadat het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in de sectie **BasisSAML-configuratie:**

    ![URL-waarden voor id en antwoord](common/idp-intiated.png)

    > [!Note]
    > Als de **URL-waarden Id** en **Antwoord** niet automatisch worden ingevuld, geeft u de waarden handmatig op basis van uw vereisten.

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door SP gestarte modus wilt configureren:

    Voer in het vak **AANmeldings-URL** **https in:\//patents.iamip.com/login-user**.

1. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** voor **certificaat (Raw)** om het certificaat te downloaden en op te slaan op uw computer:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer in de sectie **IamIP Patent Platform instellen** de juiste URL of URL's op basis van uw vereisten:

    ![Configuratie-URL's kopiëren](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam B.Simon in de Azure-portal.

1. Selecteer Azure Active Directory in het linkerdeelvenster van de **Azure-portal**. Selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer **in** het vak Naam **B.Simon in.**  
   1. Voer **User name** in het vak \<Gebruikersnaam\<gebruikersnaam>@ companydomain> in. \<uitbreiding>. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **Wachtwoord weergeven**en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="grant-access-to-the-test-user"></a>Toegang verlenen aan de testgebruiker

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door die gebruiker toegang te verlenen tot IamIP Patent Platform.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **iamIP-octrooiplatform**in de lijst met toepassingen.
1. Selecteer op de overzichtspagina van de app in de sectie **Beheren** de optie **Gebruikers en groepen:**

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen:**

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer **B.Simon** in de lijst **Gebruikers** **en groepen in** het dialoogvenster Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen.**

## <a name="configure-iamip-patent-platform-sso"></a>IamIP-patentplatform SSO configureren

Als u eenmalige aanmelding wilt configureren aan de kant van het IamIP-octrooiplatform, moet u het gedownloade raw-certificaat en de juiste URL's die u hebt gekopieerd van Azure-portal naar het ondersteuningsteam van het [IamIP Patent Platform](mailto:info@iamip.com)verzenden. Ze configureren de SAML SSO-verbinding om aan beide zijden correct te zijn.

### <a name="create-iamip-patent-platform-test-user"></a>IamIP Patent Platform testgebruiker maken

Werk samen met het [ondersteuningsteam van het IamIP Patent Platform](mailto:info@iamip.com) om een gebruiker genaamd B.Simon toe te voegen in IamIP Patent Platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO

In deze sectie test u uw Azure AD SSO-configuratie met behulp van Access Panel.

Wanneer u de iamIP-patentplatformtegel in het Access-paneel selecteert, moet u automatisch worden aangemeld bij het IamIP Patent Platform, waarvoor u SSO hebt ingesteld. Zie Inleiding tot het [toegangspaneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over het toegangspaneel .

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer IamIP Patent Platform met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
