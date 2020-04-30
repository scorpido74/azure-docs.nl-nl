---
title: 'Zelf studie: integratie Azure Active Directory met HR2day door merces | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en HR2day door merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 5b9de6c2b8d9f7b11b686e820aab54fdd8e8a02f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81866805"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Zelf studie: integratie Azure Active Directory met HR2day door merces

In deze zelf studie leert u hoe u HR2day kunt integreren met behulp van merces met Azure Active Directory (Azure AD).
Het integreren van HR2day door merces met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot HR2day door merces.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij HR2day door merces (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met HR2day door merces wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* HR2day door merces-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* HR2day door merces ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>HR2day toevoegen door merces uit de galerie

Als u de integratie van HR2day door merces wilt configureren in azure AD, moet u HR2day toevoegen door merces vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om HR2day toe te voegen door merces uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **HR2day by merces** **in het** zoekvak en klik op de knop **toevoegen** om de toepassing toe te voegen.

     ![HR2day door merces in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met HR2day door merces op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in HR2day door merces tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met HR2day door merces, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[HR2day configureren met merces eenmalige aanmelding](#configure-hr2day-by-merces-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak HR2day door merces-test gebruiker](#create-hr2day-by-merces-test-user)** : als u een equivalent van Julia Simon in HR2day wilt hebben door merces dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met HR2day door merces te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **HR2day by merces** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![HR2day door merces domein en Url's gegevens voor eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.force.com/<instancename>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://hr2day.force.com/<companyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [HR2day door merces-client ondersteunings team](mailto:servicedesk@merces.nl) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Uw HR2day door merces-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op pictogram **bewerken** om het dialoog venster **gebruikers kenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

    > [!NOTE]
    > Voordat u de SAML-bevestiging kunt configureren, moet u contact opnemen met het [HR2day door merces-client ondersteunings team](mailto:servicedesk@merces.nl) en de waarde van het kenmerk unieke id voor uw Tenant aanvragen. U hebt deze waarde nodig om de stappen in de volgende sectie te volt ooien.

6. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:

    | Naam |  Bronkenmerk |
    | ---------- | ----------- |
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    | | |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. Kopieer op de sectie **HR2day by merces instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-hr2day-by-merces-single-sign-on"></a>HR2day configureren met merces-eenmalige aanmelding

Als u eenmalige aanmelding wilt configureren voor **HR2day door merces** , moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar [HR2day door merces-ondersteunings team](mailto:servicedesk@merces.nl). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

> [!NOTE]
> Vermeld het merces-team dat voor deze integratie de entiteit-ID moet worden ingesteld met het patroon **https\/:/hr2day.Force.com/INSTANCENAME**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan HR2day door merces.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **HR2day by merces**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **HR2day by merces**.

    ![De koppeling HR2day by merces in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-hr2day-by-merces-test-user"></a>HR2day maken door merces test gebruiker

In deze sectie maakt u een gebruiker met de naam Julia Simon in HR2day by merces. Werk samen met [HR2day door merces ondersteunings team](mailto:servicedesk@merces.nl) om de gebruikers toe te voegen in het platform HR2day by merces. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [HR2day door merces-client ondersteuning](mailto:servicedesk@merces.nl).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel HR2day by merces in het toegangs venster klikt, moet u automatisch worden aangemeld bij de HR2day door merces waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
