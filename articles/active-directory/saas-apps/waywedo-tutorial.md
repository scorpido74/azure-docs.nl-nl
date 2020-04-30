---
title: 'Zelf studie: Azure Active Directory integratie met de manier waarop we | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en hoe we dat doen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67310410"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Zelf studie: de manier waarop we met Azure Active Directory worden geïntegreerd

In deze zelf studie leert u hoe u kunt integreren met Azure Active Directory (Azure AD). Wanneer u de manier integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de manier waarop we dit doen.
* Stel uw gebruikers in staat om automatisch te worden aangemeld met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Hoe we het abonnement voor eenmalige aanmelding (SSO) hebben ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* De manier waarop we de door **SP** geïnitieerde SSO ondersteunen
* De manier waarop we de **just-in-time** -gebruikers inrichting ondersteunen

## <a name="adding-way-we-do-from-the-gallery"></a>De manier waarop we vanuit de galerie werken toevoegen

Als u de integratie van de manier van Azure AD wilt configureren, moet u de manier waarop we vanuit de galerie werken, toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie in **de galerie toevoegen** **hoe we dit doen** in het zoekvak.
1. Selecteer **hoe we** het resultaten paneel doen en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

U kunt Azure AD SSO configureren en testen met een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker zoals we doen.

Als u Azure AD SSO wilt configureren en testen met de manier waarop we dit doen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Configureren van de manier waarop we SSO uitvoeren](#configure-way-we-do-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een manier](#create-way-we-do-test-user)** om de gebruiker te testen, zodat we een tegen hanger hebben van Julia Simon dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor het beheren **van toepassingen en** Zoek het gedeelte **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team](mailto:support@waywedo.com) van de client om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **certificaat (RAW)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Op de sectie **instellen hoe we dit doen** , kopieert u de juiste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Configureren van de manier waarop we SSO doen

1. Als u de configuratie wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op de **manier** waarop we de toepassing gaan uitvoeren. Geef de beheerders referenties op om u aan te melden zoals we dat doen. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan als beheerder en voert u de volgende stappen uit:

1. Klik in de rechter bovenhoek van een wille keurige pagina op het **persoons pictogram** en klik vervolgens op **account** in de vervolg keuzelijst.

    ![De manier waarop we het account uitvoeren](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klik op het **menu pictogram** om het menu Push navigatie te openen en klik op **eenmalige aanmelding**.

    ![De manier waarop we één doen](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Voer de volgende stappen uit op de pagina **instellingen voor eenmalige aanmelding** :

    ![De manier waarop we besparen](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klik op de **Schakel optie eenmalige aanmelding in-of uitschakelen** op **Ja** om eenmalige aanmelding in te scha kelen.

    b. Voer uw naam in het tekstvak **naam van eenmalige aanmelding** in.

    c. Plak in het tekstvak **Entiteits-ID** de waarde van de **Azure ad-id**, die u hebt gekopieerd van de Azure Portal.

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Upload het certificaat door te klikken op de knop **selecteren** naast **certificaat**.

    f. **Optionele instellingen** -
    
    * Wacht woorden inschakelen: wanneer deze optie is uitgeschakeld, worden de reguliere wachtwoord functies voor de manier gebruikt, zodat gebruikers alleen eenmalige aanmelding kunnen gebruiken.

    * Automatische inrichting inschakelen: wanneer deze optie is ingeschakeld, wordt het e-mail adres dat is gebruikt om u aan te melden, automatisch vergeleken met de lijst met gebruikers zoals we dat doen. Als het e-mail adres niet overeenkomt met een actieve gebruiker zoals we dit doen, voegt het automatisch een nieuw gebruikers account toe voor de persoon die zich aanmeldt, waarbij de ontbrekende gegevens worden opgevraagd.

      > [!NOTE]
      > Gebruikers die zijn toegevoegd via eenmalige aanmelding, worden toegevoegd als algemene gebruikers en er is geen rol toegewezen in het systeem. Een beheerder kan de beveiligingsrol door lopen en wijzigen als een editor of beheerder en kan ook een of meer rollen van een organigram toewijzen.

    g. Klik op **Opslaan** om uw instellingen te behouden.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot de manier waarop we dat doen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de **manier waarop we dat doen**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-way-we-do-test-user"></a>Maken van een manier waarop we gebruikers testen

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt zoals we dat doen. De manier waarop we de just-in-time-gebruikers inrichting ondersteunen, is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al op de werk wijze wordt uitgevoerd, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team](mailto:support@waywedo.com)van de client.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel in het toegangs venster selecteert, wordt u automatisch aangemeld bij de manier waarop u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)