---
title: 'Zelfstudie: Azure Active Directory-integratie met SilkRoad Life Suite | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090828"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Zelfstudie: Azure Active Directory-integratie met SilkRoad Life Suite

In deze zelfstudie leert u hoe u SilkRoad Life Suite integreert met Azure Active Directory (Azure AD).
De integratie van SilkRoad Life Suite met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot SilkRoad Life Suite.
* U uw gebruikers automatisch laten inloggen op SilkRoad Life Suite (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met SilkRoad Life Suite, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* SilkRoad Life Suite-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SilkRoad Life Suite ondersteunt **SP** geïnitieerde SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>SilkRoad Life Suite toevoegen vanuit de galerie

Als u de integratie van SilkRoad Life Suite in Azure AD wilt configureren, moet u SilkRoad Life Suite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SilkRoad Life Suite vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **SilkRoad**Life Suite in het zoekvak , selecteer **SilkRoad Life Suite** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![SilkRoad Life Suite in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SilkRoad Life Suite op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding op het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SilkRoad Life Suite.

Als u Azure AD single sign-on wilt configureren en testen met SilkRoad Life Suite, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer SilkRoad Life Suite Single Sign-On](#configure-silkroad-life-suite-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak SilkRoad Life Suite-testgebruiker](#create-silkroad-life-suite-test-user)** - om een tegenhanger van Britta Simon te hebben in SilkRoad Life Suite die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD single sign-on te configureren met SilkRoad Life Suite:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Met de **integratie van de SilkRoad Life** **Suite-toepassing de**optie Eén aanmelding .

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    > [!NOTE]
    > U krijgt de **Service Provider metadata bestand** later uitgelegd in deze tutorial.

    a. Klik op **Metagegevensbestand uploaden**.

    ![installatiekopie](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![installatiekopie](common/browse-upload-metadata.png)

    c. Zodra het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in de sectie BasisSAML-configuratie:

    ![installatiekopie](common/sp-identifier-reply.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

    d. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<subdomain>.silkroad-eng.com/Authentication/`

5. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u geen **metagegevensbestand van de serviceprovider hebt:**

    ![SilkRoad Life Suite-domein en URL's met eenmalige aanmeldingsinformatie](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<subdomain>.silkroad-eng.com/Authentication/`

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
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [silkroad Life Suite Client support team](https://www.silkroad.com/locations/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **SilkRoad Life Suite** instellen de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>SilkRoad Life Suite -aanmelding configureren

1. Meld u aan bij uw site van het SilkRoad-bedrijf als beheerder.

    > [!NOTE]
    > Neem contact op met SilkRoad Support of uw silkroad services-vertegenwoordiger om toegang te krijgen tot de SilkRoad Life Suite-verificatietoepassing voor het configureren van federatie met Microsoft Azure AD.

1. Ga naar **Serviceprovider**en klik op **Federatiegegevens**.

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klik **op Federatiemetagegevens downloaden**en sla het metagegevensbestand op uw computer op. Gebruik gedownloade federatiemetagegevens als **metagegevensbestand van serviceproviders** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Klik in uw **SilkRoad-toepassing** op **Verificatiebronnen**.

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klik **op Verificatiebron toevoegen**.

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Voer in de sectie **Verificatiebron toevoegen** de volgende stappen uit:

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Klik **onder Optie 2 - Metagegevensbestand**op **Bladeren** om het gedownloade metagegevensbestand van Azure-portal te uploaden.
  
    b. Klik **op Identiteitsprovider maken met bestandsgegevens**.

1. Klik in de sectie **Verificatiebronnen** op **Bewerken**.

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Voer in het dialoogvenster **Verificatiebron bewerken** de volgende stappen uit:

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Als **ingeschakeld**selecteert u **Ja**.

    b. Plak in het tekstvak **EntityId** de waarde van **Azure AD-id** die u hebt gekopieerd van Azure-portal.

    c. Typ in het tekstvak **IdP-beschrijving** een beschrijving voor uw configuratie *(bijvoorbeeld: Azure AD SSO*).

    d. Upload in het tekstvak **Metagegevensbestand** het **metagegevensbestand** dat u hebt gedownload van de Azure-portal.
  
    e. Typ in het tekstvak **IdP-naam** een naam die specifiek is voor uw configuratie *(bijvoorbeeld: Azure SP*).
  
    f. Plak in het tekstvak VAN de **uitlogservice URL** de waarde van de URL van **afmelden** die u hebt gekopieerd uit azure-portal.

    g. Plak in het tekstvak van de **aanmeldingsservice-URL** de waarde van **de aanmeldings-URL** die u hebt gekopieerd van azure-portal.

    h. Klik op **Opslaan**.

1. Alle andere verificatiebronnen uitschakelen.

    ![Azure AD-aanmelding](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SilkRoad Life Suite.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens SilkRoad Life Suite**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SilkRoad Life Suite**in de lijst met toepassingen.

    ![De SilkRoad Life Suite-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-silkroad-life-suite-test-user"></a>SilkRoad Life Suite-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in SilkRoad Life Suite. Werk samen met [silkroad Life Suite Client support team](https://www.silkroad.com/locations/) om de gebruikers toe te voegen aan het SilkRoad Life Suite platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de SilkRoad Life Suite-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SilkRoad Life Suite waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
