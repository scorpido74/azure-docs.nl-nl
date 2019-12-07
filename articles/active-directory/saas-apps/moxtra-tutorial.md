---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Moxtra | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889546"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Moxtra

In deze zelf studie leert u hoe u Moxtra integreert met Azure Active Directory (Azure AD). Wanneer u Moxtra integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Moxtra.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Moxtra met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Moxtra-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Moxtra ondersteunt door **SP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-moxtra-from-the-gallery"></a>Moxtra toevoegen uit de galerie

Als u de integratie van Moxtra in azure AD wilt configureren, moet u Moxtra uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Moxtra** in het zoekvak.
1. Selecteer **Moxtra** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Moxtra

Azure AD SSO met Moxtra configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Moxtra.

Als u Azure AD SSO wilt configureren en testen met Moxtra, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[MOXTRA SSO configureren](#configure-moxtra-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Moxtra-test gebruiker](#create-moxtra-test-user)** -om een equivalent van B. Simon in Moxtra te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Moxtra** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://www.moxtra.com/service/#login`

1. De Moxtra-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

1. Daarnaast verwacht Moxtra toepassing nog enkele kenmerken die in het SAML-antwoord weer worden door gegeven. Voer in de sectie gebruikers claims van het dialoog venster gebruikers kenmerken de volgende stappen uit om het SAML-token kenmerk toe te voegen, zoals wordt weer gegeven in de onderstaande tabel:

    | Naam | Bronkenmerk|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Azure AD Identifier >

    > [!Note]
    > De waarde van het kenmerk **idpid** is niet reëel. U kunt de daad werkelijke waarde ophalen uit de sectie **Moxtra instellen** uit stap 8. 

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer Bron bij **Kenmerk**.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Klik op **OK**.

    1. Klik op **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Moxtra instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Moxtra.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Moxtra**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-moxtra-sso"></a>Moxtra SSO configureren

1. Meld u in een ander browser venster aan bij de Moxtra-bedrijfs site als beheerder.

2. Klik in de werk balk aan de linkerkant op **beheer Console > eenmalige SAML-aanmelding**en klik vervolgens op **Nieuw**.
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Voer op de pagina **SAML** de volgende stappen uit:
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Typ in het tekstvak **naam** een naam voor uw configuratie (bijvoorbeeld: *SAML*). 
  
    b. Plak in het tekstvak **IDP entiteit-id** de waarde van de **Azure ad-id** die u van Azure Portal hebt gekopieerd. 
 
    c. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal. 
 
    d. Typ in het tekstvak **AuthnContextClassRef** de tekst **urn: Oasis: names: TC: SAML: 2.0: AC: classes: password**. 
 
    e. Typ in het tekstvak **NameID-indeling** **urn: Oasis: names: TC: SAML: 1.1: NameID-indeling: emailAddress**. 
 
    f. Open het certificaat dat u hebt gedownload van Azure Portal in Klad blok, kopieer de inhoud en plak deze in het tekstvak **certificaat** .    
 
    g. Typ uw SAML-e-mail domein in het tekstvak SAML-e-mail domein.    
  
    >[!NOTE]
    >Klik op '**i**' hieronder voor een overzicht van de stappen om het domein te verifiëren.

    h. Klik op **Update**.

### <a name="create-moxtra-test-user"></a>Moxtra-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in Moxtra.

**Voer de volgende stappen uit om een gebruiker te maken met de naam B. Simon in Moxtra:**

1. Meld u aan bij de Moxtra-bedrijfs site als beheerder.

1. Klik in de werk balk aan de linkerkant op **beheer Console > gebruikers beheer**en vervolgens **gebruiker toevoegen**.
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Voer in het dialoogvenster **Add User** de volgende stappen uit:
  
    a. Typ **B**in het tekstvak **voor de voor naam** .
  
    b. Typ **Simon** in het tekstvak **Last Name**.
  
    c. Typ in het tekstvak **E-mail** B. het e-mail adres van Simon is hetzelfde als op Azure Portal.
  
    d. Typ **dev**in het tekstvak **deling** .
  
    e. **Typ in het tekstvak** **afdeling** .
  
    f. Selecteer **beheerder**.
  
    g. Klik op **Add**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Moxtra in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Moxtra waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Moxtra met Azure AD](https://aad.portal.azure.com/)

