---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met workday | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fe63ba810724216b1b356896b621f1e5b021bbf
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met workday

In deze zelf studie leert u hoe u workday integreert met Azure Active Directory (Azure AD). Wanneer u workday integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot workday.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij workday met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding voor workday (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Workday ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-workday-from-the-gallery"></a>Workday toevoegen vanuit de galerie

Als u de integratie van workday wilt configureren in azure AD, moet u workday uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **werkdag** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **workday** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Eenmalige aanmelding voor Azure AD voor workday configureren en testen

Azure AD SSO met workday configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in workday.

Als u Azure AD SSO wilt configureren en testen met workday, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
2. **[Configureer workday](#configure-workday)** om de SSO-instellingen aan de kant van de toepassing te configureren.
    1. Een **[test gebruiker voor de werkdag maken](#create-workday-test-user)** die is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina werk van werk **dagen** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `http://www.workday.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Deze waarden bijwerken met de werkelijke aanmeldings-URL en antwoord-URL. Uw antwoord-URL moet een subdomein hebben, bijvoorbeeld: www, WD2, WD3, WD3-impl, wd5, wd5-impl).
    > Het gebruiken van `http://www.myworkday.com` iets zoals `http://myworkday.com` Works, maar dit is niet het geval. Neem contact op met het [werkdag-client ondersteunings team](https://www.workday.com/en-us/partners-services/services/support.html) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Uw workday-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Workday-toepassing verwacht **nameidentifier** te worden toegewezen aan **gebruiker. mail**, **UPN**, enzovoort. u moet dus de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Hier hebben we de naam-ID met UPN (User. userPrincipalName) als standaard toegewezen. U moet de naam-ID met de werkelijke gebruikers-ID in uw workday-account (uw e-mail adres, UPN, enz.) toewijzen voor een succes volle werking van SSO.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Als u de **handtekening** opties wilt wijzigen volgens uw vereiste, klikt u op de knop **bewerken** om het dialoog venster **SAML-handtekening certificaat** te openen.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Selecteer **SAML-respons ondertekenen en bevestiging** voor **ondertekening optie**.

    b. Klik op **Opslaan**

1. Op de sectie werk ruimte **instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang tot workday te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **werkdag**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workday"></a>Dag configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de bedrijfs site van uw werkdag.

2. Zoek in het zoekvak de naam **Edit Tenant Setup – beveiliging** aan de rechter kant van de start pagina.

    ![Tenant beveiliging bewerken](./media/workday-tutorial/IC782925.png "Tenant beveiliging bewerken")

3. Voer de volgende stappen uit in de sectie omleidings- **url's** :

    Omleidings- ![url's] Omleidings- (./media/workday-tutorial/IC7829581.png "url's")

    a. Klik op **rij toevoegen**.

    b. Plak de **aanmeldings-URL** die u hebt gekopieerd in de sectie werk **dagen instellen** van Azure Portal in de omleidings- **URL voor aanmelding**, **time-out** en URL voor omleidings- **url's** .

    c. Plak in het tekstvak **URL** voor afmelden de afmeldings- **URL** die u hebt gekopieerd uit de sectie werk **dagen instellen** van Azure Portal.

    d. Selecteer in **gebruik voor tekstvak omgevingen** de naam van de omgeving.  

   > [!NOTE]
   > De waarde van het omgevings kenmerk is gekoppeld aan de waarde van de Tenant-URL:  
   > -Als de domein naam van de werkdag-Tenant-URL begint met impl bijvoorbeeld: *https\/:/\<impl.workday.com/\>Tenant/login-saml2.Flex*), moet het **omgevings** kenmerk worden ingesteld op implementatie.  
   > -Als de domein naam begint met iets anders, neemt u contact op met het [werkdag client ondersteunings team](https://www.workday.com/en-us/partners-services/services/support.html) om de overeenkomende **omgevings** waarde te verkrijgen.

4. Voer in de sectie **SAML Setup** de volgende stappen uit:

    ![SAML Setup](./media/workday-tutorial/IC782926.png "SAML Setup")

    a.  Selecteer **SAML-verificatie inschakelen**.

    b.  Klik op **rij toevoegen**.

5. Voer de volgende stappen uit in de sectie **SAML-id-providers** :

    ![SAML-id-providers](./media/workday-tutorial/IC7829271.png "SAML-id-providers")

    a. Typ in het tekstvak naam van de **identiteits provider** een provider naam (bijvoorbeeld: *SPInitiatedSSO*).

    b. Kopieer in het Azure Portal, in de sectie **set up workday** , de waarde van de **Azure ad-id** en plak deze in het tekstvak voor de **certificaat verlener** .

    ![SAML-id-providers](./media/workday-tutorial/IC7829272.png "SAML-id-providers")

    c. Kopieer in het Azure Portal, in de sectie **set up workday** , de waarde van de afmeldings- **URL** en plak deze in het tekstvak **antwoord-URL** voor afmelden.

    d. Kopieer in het Azure Portal, in de sectie werk **dagen instellen** , de waarde voor de **aanmeldings-URL** en plak deze in het tekstvak URL van de **IDP SSO-service** .

    e. Selecteer in **gebruik voor tekstvak omgevingen** de naam van de omgeving.

    f. Klik op **certificaat voor open bare sleutel**van de identiteits provider en klik vervolgens op **maken**.

    ![Maken](./media/workday-tutorial/IC782928.png "Maken")

    g. Klik op **X.509 open bare sleutel maken**.

    ![Maken](./media/workday-tutorial/IC782929.png "Maken")

6. Voer de volgende stappen uit in de sectie **x509 open bare sleutel weer geven** :

    ![X509 open bare sleutel weer geven](./media/workday-tutorial/IC782930.png "X509 open bare sleutel weer geven")

    a. Typ in het tekstvak **naam** een naam voor het certificaat (bijvoorbeeld: *PPE\_SP*).

    b. Typ in het tekstvak **geldig van** de waarde geldig van kenmerk van het certificaat.

    c.  Typ in het tekstvak **geldig naar** de waarde geldig tot kenmerk van het certificaat.

    > [!NOTE]
    > U kunt de datum geldig vanaf en de geldig tot-datum uit het gedownloade certificaat ophalen door erop te dubbel klikken.  De datums worden weer gegeven op het tabblad **Details** .
    >
    >

    d.  Open uw met base 64 versleutelde certificaat in Klad blok en kopieer de inhoud ervan.

    e.  Plak de inhoud van het klem bord in het tekstvak **certificaat** .

    f.  Klik op **OK**.

7. Voer de volgende stappen uit:

    ![Configuratie van eenmalige aanmelding](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuratie van eenmalige aanmelding")

    a.  **Typ http://www.workday.com** in het tekstvak **service provider-id** .

    b. Selecteer **geen door SP geïnitieerde verificatie aanvraag**.

    c. Selecteer **sha256**als **handtekening methode voor verificatie aanvragen**.

    ![Handtekening methode voor verificatie aanvraag](./media/workday-tutorial/WorkdaySSOConfiguration.png "Handtekening methode voor verificatie aanvraag") 

    d. Klik op **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Zorg ervoor dat u eenmalige aanmelding op de juiste wijze hebt ingesteld. Als u eenmalige aanmelding met onjuiste instellingen inschakelt, is het mogelijk dat u de toepassing niet kunt invoeren met uw referenties en hoe u deze kunt vergren delen. In deze situatie biedt workday een back-upaanmeld-URL waar gebruikers zich kunnen aanmelden met hun normale gebruikers naam en wacht woord in de volgende indeling: [uw workday URL]/login.Flex? redirect = n

### <a name="create-workday-test-user"></a>Test gebruiker voor workday maken

In deze sectie maakt u een gebruiker met de naam B. Simon in workday. Werk samen met het [werkdag-client ondersteunings team](https://www.workday.com/en-us/partners-services/services/support.html) om de gebruikers toe te voegen in het workday-platform. Gebruikers moeten worden gemaakt en worden geactiveerd voordat u eenmalige aanmelding gebruiken.

## <a name="test-sso"></a>SSO testen

Wanneer u de tegel werkdag selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de werkdag waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Workday proberen met Azure AD](https://aad.portal.azure.com)
