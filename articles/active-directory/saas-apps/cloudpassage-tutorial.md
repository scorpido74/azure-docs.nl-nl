---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met CloudPassage | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0bcf0b4781e7ecb4301672f511a9f01f7fd2082
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met CloudPassage

In deze zelfstudie leert u hoe u CloudPassage integreren met Azure Active Directory (Azure AD). Wanneer u CloudPassage integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot CloudPassage.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij CloudPassage met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* CloudPassage single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* CloudPassage ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage toevoegen vanuit de galerie

Om de integratie van CloudPassage in Azure AD te configureren, moet u CloudPassage vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **CloudPassage** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **CloudPassage** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Azure AD-aanmelding voor CloudPassage configureren en testen

Azure AD SSO configureren en testen met CloudPassage met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in CloudPassage.

Als u Azure AD SSO wilt configureren en testen met CloudPassage, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[CloudPassage SSO configureren](#configure-cloudpassage-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[CloudPassage-testgebruiker maken](#create-cloudpassage-test-user)** - om een tegenhanger van B.Simon in CloudPassage te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **CloudPassage-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

     a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://portal.cloudpassage.com/saml/init/accountid`

    b. In het tekstvak **Antwoord-URL** typt u een URL en gebruikt u het volgende patroon: `https://portal.cloudpassage.com/saml/consume/accountid`. U kunt de waarde voor dit kenmerk opvragen door te klikken op **SSO Setup documentation** in het gedeelte **Single Sign-on Settings** van de CloudPassage-portal.

    ![Eenmalige aanmelding configureren](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [klantondersteuningsteam van CloudPassage](https://www.cloudpassage.com/company/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. CloudPassage-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, CloudPassage applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | e-mail |user.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **CloudPassage instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot CloudPassage.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **CloudPassage** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-cloudpassage-sso"></a>CloudPassage SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van CloudPassage.

1. Klik in de menubalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Eenmalige aanmelding configureren][12]

1. Klik op het tabblad **Authentication Settings**. 
   
    ![Eenmalige aanmelding configureren][13]

1. Voer de volgende stappen uit in het gedeelte **Single Sign-on Settings**: 
   
    ![Eenmalige aanmelding configureren][14]

    a. Schakel het selectievakje **Enable Single Sign-on (SSO) (SSO Setup Documentation)** in.
    
    b. Plak **Azure AD-id** in het **URL-tekstvak van de SAML-uitgever.**
  
    c. Plak de **aanmeldings-URL** in het tekstvak **SAML endpoint URL**.
  
    d. Plak de **afmeldings-URL** in het tekstvak **Logout landing page**.
  
    e. Open het gedownloade certificaat in Kladblok, kopieer de inhoud van het certificaat naar het Klembord en plak de inhoud vervolgens in het tekstvak **x.509 certificate**.
  
    f. Klik op **Opslaan**.

### <a name="create-cloudpassage-test-user"></a>Een testgebruiker voor CloudPassage maken

Het doel van deze sectie is het maken van een gebruiker genaamd B.Simon in CloudPassage.

**Voer de volgende stappen uit om een gebruiker met de naam B.Simon in CloudPassage te maken:**

1. Meld u als beheerder aan bij de bedrijfssite van **CloudPassage**. 

1. Klik in de werkbalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Een testgebruiker voor CloudPassage maken][22] 

1. Klik op het tabblad **Users** en klik vervolgens op **Add New User**. 
   
    ![Een testgebruiker voor CloudPassage maken][23]

1. Voer in het gedeelte **Add New User** de volgende stappen uit: 
   
    ![Een testgebruiker voor CloudPassage maken][24]
    
    a. Typ Britta in het tekstvak **First Name**. 
  
    b. Typ Simon in het tekstvak **Last Name**.
  
    c. Typ in de tekstvakken **Username**, **Email** en **Retype Email** de gebruikersnaam van Britta uit Azure AD.
  
    d. Selecteer **Enable Halo Portal Access** bij **Access Type**.
  
    e. Klik op**toevoegen**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van CloudPassage klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van CloudPassage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [CloudPassage uitproberen met Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

