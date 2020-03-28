---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Moxtra | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74889546"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Moxtra

In deze zelfstudie leert u hoe u Moxtra integreren met Azure Active Directory (Azure AD). Wanneer u Moxtra integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot Moxtra.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Moxtra met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Moxtra single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Moxtra steunt **DOOR SP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-moxtra-from-the-gallery"></a>Moxtra toevoegen vanuit de galerie

Als u de integratie van Moxtra in Azure AD wilt configureren, moet u Moxtra vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Moxtra** in de sectie **Toevoegen in de galerie** in het zoekvak.
1. Selecteer **Moxtra** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Azure AD-aanmelding voor Moxtra configureren en testen

Azure AD SSO configureren en testen met Moxtra met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Moxtra.

Als u Azure AD SSO wilt configureren en testen met Moxtra, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Moxtra SSO configureren](#configure-moxtra-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Moxtra-testgebruiker maken](#create-moxtra-test-user)** - om een tegenhanger van B.Simon in Moxtra te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Moxtra-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://www.moxtra.com/service/#login`

1. Moxtra-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Moxtra applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. In de sectie Gebruikersclaims in het dialoogvenster Gebruikerskenmerken voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Name | Bronkenmerk|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid idpid    | < Azure AD-id >

    > [!Note]
    > De waarde van **idpid** attribuut is niet echt. U de werkelijke waarde ophalen van de sectie **Moxtra instellen** vanaf stap#8. 

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer Bron bij **Kenmerk**.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Klik op **OK**.

    1. Klik op **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Moxtra instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Moxtra.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Moxtra**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-moxtra-sso"></a>Moxtra SSO configureren

1. Meld u in een ander browservenster aan op uw Moxtra-bedrijfssite als beheerder.

2. Klik op de werkbalk aan de linkerkant op **Admin Console > SAML Single Sign-on**en klik vervolgens op **Nieuw**.
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Voer op de **SAML-pagina** de volgende stappen uit:
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Typ in het tekstvak **Naam** een naam voor uw configuratie (bijvoorbeeld: *SAML*). 
  
    b. Plak in het tekstvak **Id-entiteit-id** de waarde van **azure AD-id** die u hebt gekopieerd uit azure-portal. 
 
    c. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal. 
 
    d. Typ in het tekstvak **AuthnContextClassRef** **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Typ in het tekstvak **NameID-indeling** **urn:oasis:names:tc:SAML:1.1:nameid-notatie:emailAddress**. 
 
    f. Open het certificaat dat u hebt gedownload van de Azure-portal in kladblok, kopieer de inhoud en plak het vervolgens in het tekstvak **Certificaat.**    
 
    g. Typ uw SAML-e-maildomein in het tekstvak SAML-e-mail.    
  
    >[!NOTE]
    >Als u de stappen wilt zien om het domein te verifiëren, klikt u hieronder op **"i".**

    h. Klik op **Update**.

### <a name="create-moxtra-test-user"></a>Moxtra-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.simon in Moxtra.

**Voer de volgende stappen uit om een gebruiker genaamd B.simon in Moxtra te maken:**

1. Meld u aan op uw Moxtra-bedrijfssite als beheerder.

1. Klik op de werkbalk aan de linkerkant op **Beheerconsole > gebruikersbeheer**en voeg vervolgens **Gebruiker toe**.
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Voer in het dialoogvenster **Add User** de volgende stappen uit:
  
    a. Typ **B**in het tekstvak **Voornaam** .
  
    b. Typ **Simon**in het tekstvak **Achternaam** .
  
    c. Typ in het tekstvak **E-mail** het e-mailadres van B.simon, hetzelfde als op azure-portal.
  
    d. Typ **Dev**in het tekstvak **Divisie** .
  
    e. Typ **IT**in het tekstvak **Van de afdeling** .
  
    f. Selecteer **Beheerder**.
  
    g. Klik op**toevoegen**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Moxtra-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Moxtra waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Moxtra met Azure AD](https://aad.portal.azure.com/)

