---
title: 'Zelf studie: integratie Azure Active Directory met N2F-onkosten rapporten | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en N2F-onkosten rapporten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161285"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Zelf studie: integratie Azure Active Directory met N2F-onkosten rapporten

In deze zelf studie leert u hoe u N2F-onkosten rapporten integreert met Azure Active Directory (Azure AD).
Het integreren van N2F-onkosten rapporten met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot N2F-onkosten rapporten.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij N2F-onkosten rapporten (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met N2F-onkosten rapporten wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* N2F-abonnement voor eenmalige aanmelding voor onkosten rapporten

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* N2F: onkosten rapporten bieden ondersteuning voor SSO die door **SP** en **IDP** is geïnitieerd

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>N2F-onkosten rapporten toevoegen uit de galerie

Als u de integratie van N2F-onkosten rapporten wilt configureren in azure AD, moet u N2F-onkosten rapporten uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om N2F-onkosten rapporten toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **N2F-onkosten rapporten**in het zoekvak, selecteer **N2F-onkosten rapporten** uit het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![N2F-onkosten rapporten in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met N2F-onkosten rapporten op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in N2F-onkosten rapporten tot stand brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met N2F-onkosten rapporten, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[N2F-onkosten rapporten configureren: eenmalige aanmelding](#configure-n2f---expense-reports-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[N2F maken-onkosten rapporten test gebruiker](#create-n2f---expense-reports-test-user)** : als u een equivalent van Julia Simon wilt hebben in N2F-onkosten rapporten die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met N2F-onkosten rapporten te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **N2F-onkosten rapporten** voor toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus in **IDP** wilt configureren, **hoeft de gebruiker** geen stappen uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![N2F-onkosten rapporten domein en Url's eenmalige aanmelding gegevens](common/preintegrated.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![N2F-onkosten rapporten domein en Url's eenmalige aanmelding gegevens](common/metadata-upload-additional-signon.png)

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://www.n2f.com/app/`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

7. Kopieer op de sectie **MyPolicies instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F configureren-onkosten rapporten eenmalige aanmelding

1. Meld u in een ander webbrowser venster aan bij uw N2F-onkosten rapport bedrijfs site als beheerder.

2. Klik op **instellingen** en selecteer vervolgens **Geavanceerde instellingen** in de vervolg keuzelijst.

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecteer het tabblad **account instellingen** .

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/configure2.png)

4. Selecteer **verificatie** en selecteer vervolgens **+ een tabblad verificatie methode toevoegen** .

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecteer **SAML Microsoft Office 365** als verificatie methode.

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/configure4.png)

6. Voer de volgende stappen uit in de sectie **verificatie methode** :

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/configure5.png)

    a. Plak in het tekstvak **Entiteits-ID** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    b. Plak in het tekstvak **URL voor meta gegevens** de waarde voor de URL van de **app-federatieve meta gegevens** die u van de Azure Portal hebt gekopieerd.

    c. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan N2F-onkosten rapporten.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **N2F-onkosten rapporten**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **N2F-onkosten rapporten**.

    ![De koppeling N2F-onkosten rapporten in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-n2f---expense-reports-test-user"></a>N2F maken-onkosten rapporten test gebruiker

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij N2F-onkosten rapporten, moeten ze worden ingericht in N2F-onkosten rapporten. In het geval van N2F-onkosten rapporten is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw N2F-onkosten rapport bedrijfs site als beheerder.

2. Klik op **instellingen** en selecteer vervolgens **Geavanceerde instellingen** in de vervolg keuzelijst.

    ![N2F-onkosten gebruiker toevoegen](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecteer tabblad **gebruikers** uit het navigatie paneel aan de linkerkant.

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/user1.png)

4. Selecteer **+ Nieuw gebruikers** tabblad.

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/user2.png)

5. Voer de volgende stappen uit in de sectie **gebruiker** :

    ![N2F-configuratie van onkosten rapporten](./media/n2f-expensereports-tutorial/user3.png)

    a. Voer in het tekstvak **e-mail adres** het e-mail adres van de gebruiker in, zoals **\@brittasimon contoso.com**.

    b. Voer in het tekstvak **voor de voor naam** de voor naam van de gebruiker in, zoals **Julia**.

    c. Voer in het tekstvak **Volledige naam** de volledige naam van de gebruiker in, bijvoorbeeld **Britta Simon**.

    d. Kies **rol, direct beheer (N + 1)** en **deling** conform uw organisatie vereiste.

    e. Klik op **valideren en uitnodiging verzenden**.

    > [!NOTE]
    > Als u problemen ondervindt bij het toevoegen van de gebruiker, neemt u contact op met [N2F-ondersteunings team voor onkosten rapporten](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel N2F-onkosten rapporten in het toegangs venster klikt, moet u automatisch worden aangemeld bij de N2F-onkosten rapporten waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

