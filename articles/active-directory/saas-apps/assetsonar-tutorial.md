---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met AssetSonar | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en AssetSonar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cf45a1b-30ce-420d-97c7-6bc36db07b1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b2f10a7d784fa7e089484afd5558c4c1db2275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74896694"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-assetsonar"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met AssetSonar

In deze zelfstudie leert u hoe u AssetSonar integreren met Azure Active Directory (Azure AD). Wanneer u AssetSonar integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot AssetSonar.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij AssetSonar met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* AssetSonar single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* AssetSonar ondersteunt **door SP** geïnitieerde SSO
* AssetSonar ondersteunt **Just In Time** gebruikersinrichting

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-assetsonar-from-the-gallery"></a>AssetSonar toevoegen vanuit de galerie

Als u de integratie van AssetSonar in Azure AD wilt configureren, moet u AssetSonar vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **AssetSonar** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **AssetSonar** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-assetsonar"></a>Azure AD-aanmelding voor AssetSonar configureren en testen

Azure AD SSO configureren en testen met AssetSonar met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in AssetSonar.

Als u Azure AD SSO wilt configureren en testen met AssetSonar, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[AssetSonar SSO configureren](#configure-assetsonar-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[AssetSonar-testgebruiker maken](#create-assetsonar-test-user)** - om een tegenhanger van B.Simon in AssetSonar te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **AssetSonar-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.assetsonar.com/users/sign_in`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam van AssetSonar Client](mailto:support@assetsonar.com) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. AssetSonar-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, AssetSonar applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **AssetSonar instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot AssetSonar.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **AssetSonar**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-assetsonar-sso"></a>AssetSonar SSO configureren

Als u eenmalige aanmelding aan **de Kant van AssetSonar** wilt configureren, moet u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam van AssetSonar](mailto:support@assetsonar.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-assetsonar-test-user"></a>AssetSonar-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in AssetSonar. AssetSonar ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in AssetSonar, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de assetsonar-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de AssetSonar waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer AssetSonar met Azure AD](https://aad.portal.azure.com/)