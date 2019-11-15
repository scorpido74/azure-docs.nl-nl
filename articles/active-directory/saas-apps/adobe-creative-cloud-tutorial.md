---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Adobe Creative Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081990"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Adobe Creative Cloud

In deze zelf studie leert u hoe u Adobe Creative Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Adobe Creative Cloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Adobe Creative Cloud.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Adobe Creative Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Adobe Creative Cloud abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Adobe Creative Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud toevoegen vanuit de galerie

Om de integratie van Adobe Creative Cloud met Azure AD te configureren, moet u Adobe Creative Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Adobe Creative Cloud** in het zoekvak.
1. Selecteer **Adobe Creative Cloud** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Eenmalige aanmelding voor Azure AD voor Adobe Creative Cloud configureren en testen

Azure AD SSO configureren en testen met Adobe Creative Cloud met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Adobe Creative Cloud.

Als u Azure AD SSO wilt configureren en testen met Adobe Creative Cloud, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Adobe Creative Cloud SSO configureren](#configure-adobe-creative-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Adobe Creative Cloud test gebruiker](#create-adobe-creative-cloud-test-user)** : als u een equivalent van B. Simon in Adobe Creative Cloud wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Adobe Creative Cloud** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://adobe.com`

    b. In het tekstvak **Id (entiteits-id)** typt u een URL met het volgende patroon: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteuningsteam van Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Adobe Creative Cloud toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Adobe Creative Cloud toepassing verwacht niet alleen nog maar weinig kenmerken om te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Naam | Bronkenmerk|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > De waarde voor de claim Email kan alleen worden gevuld in het SAML-antwoord als gebruikers beschikken over een geldige licentie voor Office 365 ExO.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **Adobe Creative Cloud instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Adobe Creative Cloud.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Adobe Creative Cloud** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Ga naar **Settings** in de bovenste navigatiebalk en kies **Identity**. De lijst met domeinen wordt geopend. Klik op de koppeling **Configure** voor uw domein. Voer de volgende stappen uit in het gedeelte **Single Sign On Configuration Required**. Zie [Set up domains](https://helpx.adobe.com/enterprise/using/set-up-domain.html) voor meer informatie.

    ![Instellingen](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Instellingen")

    a. Klik op **Browse** om het gedownloade certificaat van Azure AD te uploaden naar **IDP Certificate**.

    b. Plak in het tekstvak **IDP-Uitgever** de waarde van de **Azure ad-id** die u hebt gekopieerd uit Azure Portal.

    c. Plak in het tekstvak **IDP aanmeld-URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Selecteer **HTTP - Redirect** bij **IDP Binding**.

    e. Selecteer **Email** bij **User login setting**.

    f. Klik op de knop **Save**.

3. In het dashboard wordt nu het XML-bestand **Download Metadata** weergegeven. Dit bestand bevat de EntityDescriptor-URL en AssertionConsumerService-URL van Adobe. Open het bestand en configureer de URL's in de Azure AD-toepassing.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Gebruik de waarde voor de EntityDescriptor die u van Adobe hebt ontvangen voor **Id** in het dialoogvenster **App-instellingen configureren**.

    b. Gebruik de waarde voor de AssertionConsumerService die u van Adobe hebt ontvangen voor **Antwoord-URL** in het dialoogvenster **App-instellingen configureren**.

### <a name="create-adobe-creative-cloud-test-user"></a>Een testgebruiker voor Adobe Creative Cloud definiëren

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Adobe Creative Cloud, moeten ze worden ingericht in Adobe Creative Cloud. In het geval van Adobe Creative Cloud moet dit handmatig gebeuren.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als een beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Voeg de gebruiker toe als een federatieve id en wijs een productprofiel toe aan de gebruiker. Zie [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) voor uitgebreide informatie over het toevoegen van gebruikers. 

3. Op dit punt typt u uw e-mail adres/UPN in het Adobe-aanmeld formulier, drukt u op tab en moet u zich weer aanmelden bij Azure AD:
   * Webtoegang: www\.adobe.com >-aanmelding
   * In het hulpprogramma voor desktop-app > aanmelden
   * In de toepassing > help > aanmelden

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Adobe Creative Cloud klikt in het toegangsvenster, wordt u als het goed is automatisch aangemeld bij het exemplaar van Adobe Creative Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Adobe Creative Cloud met Azure AD](https://aad.portal.azure.com/)

- [Set up domains (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configure Microsoft Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

