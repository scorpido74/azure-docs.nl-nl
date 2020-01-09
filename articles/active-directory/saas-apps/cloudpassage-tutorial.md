---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met CloudPassage | Microsoft Docs'
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
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met CloudPassage

In deze zelf studie leert u hoe u CloudPassage integreert met Azure Active Directory (Azure AD). Wanneer u CloudPassage integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot CloudPassage.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij CloudPassage met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* CloudPassage-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* CloudPassage ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage toevoegen vanuit de galerie

Om de integratie van CloudPassage in Azure AD te configureren, moet u CloudPassage vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **CloudPassage** in het zoekvak.
1. Selecteer **CloudPassage** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor CloudPassage

Azure AD SSO met CloudPassage configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CloudPassage.

Als u Azure AD SSO wilt configureren en testen met CloudPassage, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CLOUDPASSAGE SSO configureren](#configure-cloudpassage-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een CloudPassage-test gebruiker](#create-cloudpassage-test-user)** -om een equivalent van B. Simon in CloudPassage te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **CloudPassage** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

     a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://portal.cloudpassage.com/saml/init/accountid`

    b. In het tekstvak **Antwoord-URL** typt u een URL en gebruikt u het volgende patroon: `https://portal.cloudpassage.com/saml/consume/accountid`. U kunt de waarde voor dit kenmerk opvragen door te klikken op **SSO Setup documentation** in het gedeelte **Single Sign-on Settings** van de CloudPassage-portal.

    ![Eenmalige aanmelding configureren](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [klantondersteuningsteam van CloudPassage](https://www.cloudpassage.com/company/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De CloudPassage-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Daarnaast verwacht CloudPassage toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | e-mail |user.mail |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **CloudPassage instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan CloudPassage.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **CloudPassage** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cloudpassage-sso"></a>CloudPassage SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van CloudPassage.

1. Klik in de menubalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Eenmalige aanmelding configureren][12]

1. Klik op het tabblad **Authentication Settings**. 
   
    ![Eenmalige aanmelding configureren][13]

1. Voer de volgende stappen uit in het gedeelte **Single Sign-on Settings**: 
   
    ![Eenmalige aanmelding configureren][14]

    a. Schakel het selectievakje **Enable Single Sign-on (SSO) (SSO Setup Documentation)** in.
    
    b. Plak de **Azure ad-id** in het tekstvak URL van de **SAML-Uitgever** .
  
    c. Plak de **aanmeldings-URL** in het tekstvak **SAML endpoint URL**.
  
    d. Plak de **afmeldings-URL** in het tekstvak **Logout landing page**.
  
    e. Open het gedownloade certificaat in Kladblok, kopieer de inhoud van het certificaat naar het Klembord en plak de inhoud vervolgens in het tekstvak **x.509 certificate**.
  
    f. Klik op **Opslaan**.

### <a name="create-cloudpassage-test-user"></a>Een testgebruiker voor CloudPassage maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in CloudPassage.

**Voer de volgende stappen uit om een gebruiker te maken met de naam B. Simon in CloudPassage:**

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
  
    e. Klik op **Add**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van CloudPassage klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van CloudPassage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer CloudPassage met Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

