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
ms.openlocfilehash: 25dd638c15fecbef787e4ceabea9ae7cb4359582
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76120329"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Adobe Creative Cloud

> [!NOTE]
> In dit artikel wordt de aangepaste op SAML gebaseerde configuratie van de Adobe-beheer console voor Azure Active Directory (Azure AD) beschreven. Voor nieuwe configuraties kunt u het beste de [Azure AD-connector](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html)gebruiken. De Azure AD-connector kan in enkele minuten worden ingesteld en verkort het proces van de domein claim, de installatie van eenmalige aanmelding en de gebruikers synchronisatie.

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

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
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

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Adobe Creative Cloud** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://adobe.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteuningsteam van Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Adobe Creative Cloud toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Adobe Creative Cloud toepassing verwacht niet alleen nog maar weinig kenmerken om te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Naam | Bronkenmerk|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-mail | user.mail |

    > [!NOTE]
    > De waarde voor de claim Email kan alleen worden gevuld in het SAML-antwoord als gebruikers beschikken over een geldige licentie voor Office 365 ExO.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar de sectie SAML **-** **handtekening certificaat** en selecteer vervolgens **downloaden** om het XML-bestand met meta gegevens te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **Adobe Creative Cloud instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

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
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud SSO configureren

1. Meld u in een ander webbrowser venster aan bij [Adobe admin console](https://adminconsole.adobe.com) als systeem beheerder.

1. Ga naar **instellingen** op de bovenste navigatie balk en kies vervolgens **identiteit**. De lijst met mappen wordt geopend. Selecteer de gewenste federatieve map.

1. Selecteer op de pagina **Directory details** de optie **configureren**.

1. Kopieer de entiteit-ID en de ACS-URL (URL of antwoord-URL van de Assertion Consumer-Service). Voer de Url's in op de juiste velden in de Azure Portal.

    ![Eenmalige aanmelding configureren aan de kant van de app](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Gebruik de entiteit-ID-waarde die Adobe u heeft verschaft voor **id** in het dialoog venster **app-instellingen configureren** .

    b. Gebruik de ACS URL (assertion Consumer Service-URL) die u hebt ontvangen voor **antwoord-URL** in het dialoog venster app- **instellingen configureren** .

1. Upload het **XML-bestand met federatieve gegevens** dat u hebt gedownload van de Azure portal aan de onderkant van de pagina. 

    ![XML-bestand met federatieve gegevens](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP XML voor meta gegevens")

1. Selecteer **Opslaan**.


### <a name="create-adobe-creative-cloud-test-user"></a>Een testgebruiker voor Adobe Creative Cloud definiëren

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Adobe Creative Cloud, moeten ze worden ingericht in Adobe Creative Cloud. In het geval van Adobe Creative Cloud moet dit handmatig gebeuren.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als een beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Voeg de gebruiker toe als een federatieve id en wijs een productprofiel toe aan de gebruiker. Zie [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) voor uitgebreide informatie over het toevoegen van gebruikers. 

3. Op dit punt typt u uw e-mail adres/UPN in het Adobe-aanmeld formulier, drukt u op tab en moet u zich weer aanmelden bij Azure AD:
   * Webtoegang: www\.-Adobe.com >-aanmelding
   * In het hulpprogramma voor desktop-app > aanmelden
   * In de toepassing > help > aanmelden

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Adobe Creative Cloud klikt in het toegangsvenster, wordt u als het goed is automatisch aangemeld bij het exemplaar van Adobe Creative Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Adobe Creative Cloud met Azure AD](https://aad.portal.azure.com/)

- [Een identiteit instellen (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Configure Microsoft Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

