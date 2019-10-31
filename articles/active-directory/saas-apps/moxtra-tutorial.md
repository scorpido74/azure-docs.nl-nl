---
title: 'Zelf studie: integratie Azure Active Directory met Moxtra | Microsoft Docs'
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
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: fb8262a75b688f754c26fd82e3ca7f06851e5453
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161300"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Zelf studie: integratie Azure Active Directory met Moxtra

In deze zelf studie leert u hoe u Moxtra integreert met Azure Active Directory (Azure AD).
Het integreren van Moxtra met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Moxtra.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Moxtra (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Moxtra wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding Moxtra ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Moxtra ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-moxtra-from-the-gallery"></a>Moxtra toevoegen uit de galerie

Als u de integratie van Moxtra in azure AD wilt configureren, moet u Moxtra uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Moxtra toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Moxtra**in het zoekvak, selecteer **Moxtra** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Moxtra in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Moxtra op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Moxtra tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Moxtra, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Moxtra eenmalige aanmelding configureren](#configure-moxtra-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Moxtra-test gebruiker](#create-moxtra-test-user)** -om een equivalent van Julia Simon in Moxtra te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Moxtra:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Moxtra** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Moxtra domein en Url's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.moxtra.com/service/#login`

5. De Moxtra-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op pictogram **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![image](common/edit-attribute.png)

6. Daarnaast verwacht Moxtra toepassing nog enkele kenmerken die in het SAML-antwoord weer worden door gegeven. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel: 

    | Naam | Bronkenmerk|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Azure AD-id >

    > [!Note]
    > De waarde van het kenmerk **idpid** is niet reëel. U kunt de daad werkelijke waarde ophalen uit de sectie **Moxtra instellen** uit stap 8. 

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. Kopieer op de sectie **Moxtra instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-moxtra-single-sign-on"></a>Eenmalige aanmelding voor Moxtra configureren

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

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Moxtra.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Moxtra**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Moxtra**.

    ![De koppeling Moxtra in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-moxtra-test-user"></a>Moxtra-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in Moxtra.

**Als u een gebruiker met de naam Julia Simon in Moxtra wilt maken, voert u de volgende stappen uit:**

1. Meld u aan bij de Moxtra-bedrijfs site als beheerder.

1. Klik in de werk balk aan de linkerkant op **beheer Console > gebruikers beheer**en vervolgens **gebruiker toevoegen**.
   
    ![Eenmalige aanmelding configureren](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Voer in het dialoogvenster **Add User** de volgende stappen uit:
  
    a. Typ **Britta** in het tekstvak **First Name**.
  
    b. Typ **Simon** in het tekstvak **Last Name**.
  
    c. Typ in het tekstvak **e-mail** het e-mail adres van Julia zoals op Azure Portal.
  
    d. Typ **dev**in het tekstvak **deling** .
  
    e. **Typ in het tekstvak** **afdeling** .
  
    f. Selecteer **beheerder**.
  
    g. Klik op **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Moxtra in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Moxtra waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

