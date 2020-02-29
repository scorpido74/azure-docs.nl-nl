---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het patent platform van IamIP | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en IamIP patent platform.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190735"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het patent platform van IamIP

In deze zelf studie leert u hoe u IamIP patent platform integreert met Azure Active Directory (Azure AD). Wanneer u IamIP patent platform integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot het IamIP-patent platform.
* Stel in dat uw gebruikers automatisch worden aangemeld bij IamIP patent platform met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een IamIP-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="tutorial-description"></a>Beschrijving van zelf studie

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

IamIP patent platform ondersteunt door SP geïnitieerde en door IDP geïnitieerde SSO.

Nadat u het IamIP-patent platform hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>IamIP patent platform toevoegen uit de galerie

Als u de integratie van IamIP patent platform wilt configureren in azure AD, moet u IamIP patent platform toevoegen uit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een werk-of school account of met een persoonlijke Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** **IamIP patent platform** in het zoekvak.
1. Selecteer **IamIP patent platform** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Azure AD SSO configureren en testen voor IamIP patent platform

U gaat Azure AD SSO met IamIP patent platform configureren en testen met behulp van een test gebruiker met de naam B. Simon. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het IamIP-patent platform.

Als u Azure AD SSO wilt configureren en testen met IamIP patent platform, neemt u de volgende stappen op hoog niveau:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers de functie kunnen gebruiken.
    * **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen.
    * **[Verleen toegang aan de test gebruiker](#grant-access-to-the-test-user)** om de gebruiker in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.

1. **[CONFIGUREER SSO van IamIP patent platform](#configure-iamip-patent-platform-sso)** aan de kant van de toepassing.
    * **[Maak een test gebruiker voor een IamIP-patent platform](#create-iamip-patent-platform-test-user)** als een soort tegen hanger met de Azure AD-representatie van de gebruiker.

1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen in de Azure Portal:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **IamIP patent platform** Application Integration in de sectie **beheren** de optie **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de potlood knop voor de **eenvoudige configuratie van SAML** om de instellingen te bewerken:

   ![Knop Potlood voor eenvoudige SAML-configuratie](common/edit-urls.png)

1. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** als u een meta gegevensbestand voor de service provider hebt en SSO wilt configureren in de modus gestart door IDP:

    a. **META gegevensbestand voor uploaden**selecteren:

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Selecteer de knop map, selecteer het meta gegevensbestand en selecteer vervolgens **uploaden**:

    ![Mappen en upload knoppen](common/browse-upload-metadata.png)

    c. Nadat het bestand met meta gegevens is geüpload, worden de **id** -en **antwoord-URL** -waarden automatisch ingevuld in de sectie **basis configuratie van SAML** :

    ![Id-en antwoord-URL-waarden](common/idp-intiated.png)

    > [!Note]
    > Als de **id** -en **antwoord-URL** -waarden niet automatisch worden ingevuld, moet u de waarden hand matig opgeven volgens uw vereisten.

1. Selecteer **extra Url's instellen** en voer de volgende stap uit als u de toepassing in de door SP geïnitieerde modus wilt configureren:

    Voer in het vak **aanmeld-URL** https in **:\//patents.iamip.com/login-User**.

1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de **Download** koppeling voor het **certificaat (RAW)** om het certificaat te downloaden en op uw computer op te slaan:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer in de sectie **IamIP-patent platform instellen** de juiste URL of url's, op basis van uw vereisten:

    ![Configuratie-URL's kopiëren](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam B. Simon in de Azure Portal.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de Azure Portal. Selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het vak **naam** **B. Simon**in.  
   1. Voer in het vak **gebruikers naam** \<gebruikers naam > @\<companydomain > in.\<extensie >. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **wacht woord weer geven**en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Selecteer **Maken**.

### <a name="grant-access-to-the-test-user"></a>Toegang verlenen aan de test gebruiker

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door de gebruikers toegang te verlenen tot het IamIP-patent platform.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **IamIP patent platform**.
1. Op de overzichts pagina van de app in de sectie **beheren** selecteert u **gebruikers en groepen**:

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** :

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** in de lijst **gebruikers** en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

## <a name="configure-iamip-patent-platform-sso"></a>IamIP patent platform-SSO configureren

Als u eenmalige aanmelding wilt configureren voor het IamIP-patent platform, moet u het gedownloade onbewerkte certificaat en de juiste Url's die u hebt gekopieerd van Azure Portal naar het [IamIP patent platform ondersteunings team](mailto:info@iamip.com)verzenden. Ze configureren dat de SAML SSO-verbinding aan beide kanten juist is.

### <a name="create-iamip-patent-platform-test-user"></a>Test gebruiker voor IamIP-patent platform maken

Werk met het [ondersteunings team van IamIP patent platform](mailto:info@iamip.com) om een gebruiker toe te voegen met de naam B. Simon in het patent platform IamIP. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw Azure AD SSO-configuratie testen met behulp van het toegangs venster.

Wanneer u de tegel IamIP patent platform in het toegangs venster selecteert, wordt u automatisch aangemeld bij het exemplaar van het IamIP-patent platform waarvoor u SSO hebt ingesteld. Zie [Introduction to Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)(Engelstalig) voor meer informatie over het toegangs venster.

## <a name="additional-resources"></a>Aanvullende resources

- [Zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer IamIP patent platform met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
