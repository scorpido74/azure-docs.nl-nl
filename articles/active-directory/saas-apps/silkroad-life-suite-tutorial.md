---
title: 'Zelf studie: integratie met SilkRoad Life Suite Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090828"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Zelf studie: integratie Azure Active Directory met SilkRoad-levenscyclus pakket

In deze zelf studie leert u hoe u SilkRoad Life Suite integreert met Azure Active Directory (Azure AD).
Het integreren van SilkRoad-levenscyclus pakket met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SilkRoad Life Suite.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SilkRoad Life suite (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SilkRoad Life Suite wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding voor SilkRoad Life Suite

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SilkRoad Life suite ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>SilkRoad-levenscyclus pakket toevoegen uit de galerie

Als u de integratie van SilkRoad-levenscyclus pakket wilt configureren in azure AD, moet u SilkRoad levenscyclus pakket toevoegen uit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SilkRoad levenscyclus pakket toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Silkroad Life Suite**in het zoekvak, selecteer **Silkroad levenscyclus pakket** uit het paneel resultaten en klik vervolgens op **toevoegen** knop om de toepassing toe te voegen.

    ![SilkRoad-levenscyclus pakket in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met SilkRoad-levenscyclus pakket op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SilkRoad levenscyclus pakket tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SilkRoad Life Suite, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van de Silkroad](#configure-silkroad-life-suite-single-sign-on)** -app configureren voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Silkroad-test gebruiker voor het maken van een levens duur Suite](#create-silkroad-life-suite-test-user)** , zodat er een equivalent van Julia Simon in Silkroad Life Suite is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SilkRoad Life Suite:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Silkroad Life Suite** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    > [!NOTE]
    > U krijgt het **META gegevensbestand van de service provider** , dat verderop in deze zelf studie wordt uitgelegd.

    a. Klik op **Metagegevensbestand uploaden**.

    ![installatiekopie](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![installatiekopie](common/browse-upload-metadata.png)

    c. Zodra het meta gegevensbestand is geüpload, worden de waarden voor de **id** en de **antwoord-URL** automatisch ingevuld in de basis configuratie sectie voor SAML:

    ![installatiekopie](common/sp-identifier-reply.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

    d. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<subdomain>.silkroad-eng.com/Authentication/`

5. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** als u geen **bestand met meta gegevens van de service provider**hebt:

    ![Informatie over eenmalige aanmelding van het domein en de Url's voor SilkRoad Life Suite](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Silkroad Life Suite-client](https://www.silkroad.com/locations/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer op de sectie **Silkroad-levenscyclus pakket instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Eenmalige aanmelding voor de SilkRoad Life Suite configureren

1. Meld u als beheerder aan bij de SilkRoad-bedrijfs site.

    > [!NOTE]
    > Neem contact op met SilkRoad-ondersteuning of uw SilkRoad Services-vertegenwoordiger om toegang te krijgen tot de SilkRoad Life Suite-verificatie toepassing voor het configureren van Federatie met Microsoft Azure AD.

1. Ga naar **service provider**en klik vervolgens op **Federation-Details**.

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klik op **federatieve meta gegevens downloaden**en sla het meta gegevensbestand op uw computer op. Gedownloade federatieve meta gegevens als een **META gegevensbestand van een service provider** gebruiken in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Klik in uw **Silkroad** -toepassing op **verificatie bronnen**.

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klik op **verificatie bron toevoegen**.

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Voer de volgende stappen uit in de sectie **verificatie bron toevoegen** :

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Klik onder **optie 2-Meta gegevensbestand**op **Bladeren** om het gedownloade meta gegevensbestand te uploaden van Azure Portal.
  
    b. Klik op **ID-provider maken met behulp van bestands gegevens**.

1. Klik in de sectie **verificatie bronnen** op **bewerken**.

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Voer de volgende stappen uit in het dialoog venster **verificatie bron bewerken** :

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Als **ingeschakeld**, selecteert u **Ja**.

    b. Plak in het tekstvak **EntityId** de waarde van de **Azure ad-id** die u van Azure Portal hebt gekopieerd.

    c. Typ in het tekstvak **Beschrijving van IDP** een beschrijving voor uw configuratie (bijvoorbeeld: *Azure AD SSO*).

    d. Upload in het tekstvak **META gegevensbestand** het **META gegevensbestand** dat u hebt gedownload van Azure Portal.
  
    e. Typ in het tekstvak **IDP name** een naam die specifiek is voor uw configuratie (bijvoorbeeld: *Azure SP*).
  
    f. Plak in het tekstvak de URL van de **Afmeldings service** de waarde van de **afmeldings-URL** die u van Azure Portal hebt gekopieerd.

    g. Plak in het tekstvak **URL voor de aanmeldings service** de waarde van de **aanmeldings-URL** die u van Azure Portal hebt gekopieerd.

    h. Klik op **Opslaan**.

1. Schakel alle andere verificatie bronnen uit.

    ![Eenmalige aanmelding voor Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot de SilkRoad-levens Suite.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Silkroad Life Suite**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Silkroad Life Suite**.

    ![De koppeling SilkRoad Life Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-silkroad-life-suite-test-user"></a>Test gebruiker voor SilkRoad Life Suite maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in SilkRoad Life Suite. Werk samen met het [ondersteunings team van Silkroad Life Suite](https://www.silkroad.com/locations/) om de gebruikers toe te voegen in het platform van de Silkroad-levens Suite. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SilkRoad levenscyclus pakket in het toegangs venster klikt, moet u automatisch worden aangemeld bij het SilkRoad-levens duur pakket waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
