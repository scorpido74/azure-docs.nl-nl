---
title: 'Zelf studie: integratie Azure Active Directory met Lucidchart | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc91d51d49ac1a317b88835e4d2e51773216020
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159509"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Zelf studie: integratie Azure Active Directory met Lucidchart

In deze zelf studie leert u hoe u Lucidchart integreert met Azure Active Directory (Azure AD).
Het integreren van Lucidchart met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Lucidchart.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Lucidchart (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Lucidchart wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding Lucidchart ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Lucidchart ondersteunt door **SP** GEÏNITIEERDe SSO
* Lucidchart ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-lucidchart-from-the-gallery"></a>Lucidchart toevoegen uit de galerie

Als u de integratie van Lucidchart in azure AD wilt configureren, moet u Lucidchart uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Lucidchart toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Lucidchart**in het zoekvak, selecteer **Lucidchart** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Lucidchart in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Lucidchart op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Lucidchart tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Lucidchart, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Lucidchart eenmalige aanmelding configureren](#configure-lucidchart-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Lucidchart-test gebruiker](#create-lucidchart-test-user)** -om een equivalent van Julia Simon in Lucidchart te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Lucidchart:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Lucidchart** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Lucidchart domein en Url's](common/sp-signonurl.png)

    Typ in het tekstvak **URL voor aanmelding** een URL als: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **Lucidchart instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-lucidchart-single-sign-on"></a>Eenmalige aanmelding voor Lucidchart configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de Lucidchart-bedrijfs site.

2. Klik in het menu aan de bovenkant op **team**.

    ![Stellen](./media/lucidchart-tutorial/ic791190.png "Stellen")

3. Klik op **toepassingen \> SAML beheren**.

    ![SAML beheren](./media/lucidchart-tutorial/ic791191.png "SAML beheren")

4. Voer de volgende stappen uit op de pagina **instellingen voor SAML-verificatie** :

    a. Selecteer **SAML-verificatie inschakelen**en klik vervolgens op **optioneel**.

    ![SAML-verificatie-instellingen](./media/lucidchart-tutorial/ic791192.png "SAML-verificatie-instellingen")

    b. Typ uw domein in het tekstvak **domein** en klik vervolgens op **certificaat wijzigen**.

    ![Certificaat wijzigen](./media/lucidchart-tutorial/ic791193.png "Certificaat wijzigen")

    c. Open het gedownloade meta gegevensbestand, kopieer de inhoud en plak het in het tekstvak voor het **uploaden van meta gegevens** .

    ![Meta gegevens uploaden](./media/lucidchart-tutorial/ic791194.png "Meta gegevens uploaden")

    d. Selecteer **automatisch nieuwe gebruikers toevoegen aan het team**en klik vervolgens op **wijzigingen opslaan**.

    ![Wijzigingen opslaan](./media/lucidchart-tutorial/ic791195.png "Wijzigingen opslaan")

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Lucidchart.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Lucidchart**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Lucidchart**.

    ![De koppeling Lucidchart in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-lucidchart-test-user"></a>Lucidchart-test gebruiker maken

Er is geen actie-item voor het configureren van de gebruikers inrichting voor Lucidchart.  Wanneer een toegewezen gebruiker zich probeert aan te melden bij Lucidchart via het toegangs venster, controleert Lucidchart of de gebruiker bestaat.  

Als er nog geen gebruikers account beschikbaar is, wordt deze automatisch gemaakt door Lucidchart.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Lucidchart in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Lucidchart waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
