---
title: 'Zelf studie: integratie Azure Active Directory met replicon | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67092773"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Zelf studie: replicon integreren met Azure Active Directory

In deze zelf studie leert u hoe u replicon integreert met Azure Active Directory (Azure AD). Wanneer u replicon integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot replicon.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij replicon met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Replicon-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Replicon ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-replicon-from-the-gallery"></a>Replicon toevoegen uit de galerie

Als u de integratie van replicon in azure AD wilt configureren, moet u replicon uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **replicon** in het zoekvak.
1. Selecteer **replicon** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met replicon configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in replicon.

Als u Azure AD SSO wilt configureren en testen met replicon, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[REPLICON SSO configureren](#configure-replicon-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een replicon-test gebruiker](#create-replicon-test-user)** -om een equivalent van B. Simon in replicon te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **replicon** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    1. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://global.replicon.com/!/saml2/<client name>`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van replicon-clients](https://www.replicon.com/customerzone/contact-support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op het pictogram bewerken/pen voor het **SAML-handtekening certificaat** om de instellingen te bewerken.

    ![Handtekening algoritme](common/signing-algorithm.png)

    1. Selecteer **SAML-bevestiging ondertekenen** als de **optie ondertekenen**.

    1. Selecteer **SHA-256** als het **handtekening algoritme**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replicon SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw replicon-bedrijfs site.

2. Als u SAML 2,0 wilt configureren, voert u de volgende stappen uit:

    ![SAML-verificatie inschakelen](./media/replicon-tutorial/ic777805.png "SAML-verificatie inschakelen")

    a. Als u het dialoog venster **EnableSAML Authentication2** wilt weer geven, voegt u het volgende toe aan uw URL na uw bedrijfs sleutel:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Hieronder ziet u het schema van de volledige URL:`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klik op **+** de om de sectie **v20Configuration** uit te vouwen.

   c. Klik op **+** de om de sectie **metaDataConfiguration** uit te vouwen.

   d. Selecteer **sha256** voor xmlSignatureAlgorithm

   e. Klik op **bestand kiezen**om het XML-bestand met de meta gegevens van uw identiteits provider te selecteren en klik op **verzenden**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan replicon.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **replicon**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-replicon-test-user"></a>Replicon-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in replicon.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u in een webbrowser venster aan bij uw replicon-bedrijfs site als beheerder.

2. Ga naar **beheer \> gebruikers**.

    ![Gebruikers](./media/replicon-tutorial/ic777806.png "Gebruikers")

3. Klik op **+ gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/replicon-tutorial/ic777807.png "Gebruiker toevoegen")

4. Voer de volgende stappen uit in de sectie **gebruikers profiel** :

    ![Gebruikersprofiel](./media/replicon-tutorial/ic777808.png "Gebruikersprofiel")

    a. Typ in het tekstvak **aanmeldings naam** het e-mail adres van Azure AD van de Azure AD-gebruiker die u `B.Simon@contoso.com`wilt inrichten, zoals.

    > [!NOTE]
    > De aanmeldings naam moet overeenkomen met het e-mail adres van de gebruiker in azure AD

    b. Selecteer **SSO**als **verificatie type**.

    c. Stel de verificatie-ID in op dezelfde waarde als de aanmeldings naam (het e-mail adres van Azure AD van de gebruiker)

    d. Typ in het tekstvak **afdeling** de afdeling van de gebruiker.

    e. Selecteer **beheerder**als **werknemers type**.

    f. Klik op **gebruikers profiel opslaan**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van replicon-gebruikers accounts of Api's die worden geleverd door replicon, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel replicon in het toegangs venster selecteert, wordt u automatisch aangemeld bij de replicon waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)