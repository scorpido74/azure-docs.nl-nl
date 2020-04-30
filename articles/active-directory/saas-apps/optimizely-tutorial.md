---
title: 'Zelf studie: integratie Azure Active Directory met optimaliseren | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Optima Lise ring.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68943957"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Zelf studie: integratie met optimale Azure Active Directory

In deze zelf studie leert u hoe u optimaliseert met Azure Active Directory (Azure AD).
Het integreren van optimaliseert met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft om te optimaliseren.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor een optimale (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met optimalisatie wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding optimaliseren

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Optimaliseert ondersteuning voor door **SP** GEÏNITIEERDe SSO

## <a name="adding-optimizely-from-the-gallery"></a>Optimaliseren toevoegen vanuit de galerie

Als u de integratie van optimaliseert naar Azure AD wilt configureren, moet u optimaliseren vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om een optimalisatie van de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **optimaliseren**, selecteer **optimaliseren** uit het paneel resultaten en klik vervolgens op **toevoegen** om de toepassing toe te voegen.

     ![Optimaliseer in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met optimalisatie op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker worden gemaakt.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met optimalisatie, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer eenmalige aanmelding configureren](#configure-optimizely-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Gebruikers met een geoptimaliseerde test maken](#create-optimizely-test-user)** : als u een koppeling van Julia Simon in optimalisatie wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met optimalisatie:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **optimalisatie** van de toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over geoptimaliseerde domein-en URL-eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://app.optimizely.net/<instance name>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Dit zijn niet de echte waarden. U werkt de waarde bij met de werkelijke aanmeldings-URL en-id, die verderop in de zelf studie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Uw optimalisatie toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op pictogram **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

6. In aanvulling op de bovenstaande, verwacht een geoptimaliseerde toepassing nog maar weinig kenmerken om te worden door gegeven in het SAML-antwoord. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk |
    | ---------------| --------------- |
    | e-mail | user.mail |
    
    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **optimaliseerbaar instellen** de juiste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-optimizely-single-sign-on"></a>Eenmalige aanmelding configureren

1. Voor het configureren van eenmalige aanmelding op een **geoptimaliseerde** zijde, neemt u contact op met uw optimalisatie account manager en verstrekt u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's.

2. Als reactie op uw e-mail adres biedt optimaliseert u de aanmeldings-URL (door SP geïnitieerde SSO) en de id-waarden (service provider entiteit-ID).

    a. Kopieer de door de **SP geïnitieerde SSO-URL** die u hebt gemaakt door optimalisatie en plak deze in het tekstvak **voor de aanmeldings-URL** in het gedeelte basis-SAML- **configuratie** op Azure Portal.

    b. Kopieer de **entiteits-id van de service provider** die u hebt gekregen door optimalisatie en plak deze in het tekstvak **id** in de sectie **basis configuratie van SAML** op Azure Portal.

3. Meld u in een ander browser venster aan bij uw geoptimaliseerde toepassing.

4. Klik in de rechter bovenhoek op account naam en vervolgens op **account instellingen**.

    ![Eenmalige aanmelding voor Azure AD](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Schakel op het tabblad account het selectie vakje **SSO inschakelen** onder eenmalige aanmelding in het gedeelte **overzicht** in.
  
    ![Eenmalige aanmelding voor Azure AD](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klik op **Opslaan**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door de toegang te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **optimaliseren**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **optimaliseren**.

    ![De koppeling optimaliseren in de lijst toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-optimizely-test-user"></a>Gebruiker voor geoptimaliseerde test maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in optimaliseert.

1. Selecteer op de start pagina tabblad **samen** werkers.

2. Klik op **nieuwe samen werker**om een nieuwe samen werker aan het project toe te voegen.
   
    ![Een Azure AD-testgebruiker maken](./media/optimizely-tutorial/create_aaduser_10.png)

3. Vul het e-mail adres in en wijs hieraan een rol toe. Klik op **Uitnodigen**.

    ![Een Azure AD-testgebruiker maken](./media/optimizely-tutorial/create_aaduser_11.png)

4. Ze ontvangen een uitnodiging voor e-mail. Wanneer u het e-mail adres gebruikt, moet u zich bij een optimale aanmelding aanmelden.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel optimaliseren in het toegangs venster klikt, wordt u automatisch aangemeld bij de optimalisatie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

