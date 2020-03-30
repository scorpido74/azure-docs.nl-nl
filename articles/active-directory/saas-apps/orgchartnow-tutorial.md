---
title: 'Zelfstudie: Azure Active Directory-integratie met OrgChart Now | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095436"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Zelfstudie: Azure Active Directory-integratie met OrgChart nu

In deze zelfstudie leert u hoe u OrgChart Now integreert met Azure Active Directory (Azure AD).
Het integreren van OrgChart Now met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie nu toegang heeft tot OrgChart.
* U uw gebruikers automatisch laten inloggen op OrgChart Now (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met OrgChart Now, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met eenmalige aanmelding voor orgchart nu

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* OrgChart Now ondersteunt **SP** en **IDP** geïnitieerde SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Organigram nu toevoegen vanuit de galerie

Als u de integratie van OrgChart Now in Azure AD wilt configureren, moet u OrgChart Now vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Organchart Now vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **orgchart nu**in het zoekvak , selecteer **OrgChart Nu** van het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Organigram Nu in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met OrgChart Now op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in OrgChart Now.

Als u Azure AD-single sign-on wilt configureren en testen met OrgChart Now, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer orgchart Now Single Sign-On](#configure-orgchart-now-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[OrgChart Now test gebruiker](#create-orgchart-now-test-user)** - om een tegenhanger van Britta Simon in OrgChart Now dat is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD-enkele aanmelding met OrgChart Now te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **OrgChart** Now-toepassingsintegratie de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![OrgChart Now-domein- en URL's-informatie met eenmalige aanmelding](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL: `https://sso2.orgchartnow.com`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![installatiekopie](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`is de **Azure AD-id** gekopieerd uit de sectie **OrgChart Now instellen,** die later in de zelfstudie wordt beschreven.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **OrgChart Now** de juiste URL(s) naar uw behoefte.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Orgchart nu eenmalig aanmelden configureren

Als u eenmalige aanmelding wilt configureren aan de kant **van OrgChart Now,** moet u de gedownloade **XML met aalmetagegevens** van de Federatie en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam van OrgChart Now](mailto:ocnsupport@officeworksoftware.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door nu toegang te verlenen tot OrgChart.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens OrgChart Now**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **orgchart nu**in de lijst met toepassingen .

    ![De koppeling OrgChart Now in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-orgchart-now-test-user"></a>Testgebruiker Organigram nu maken

Als u Azure AD-gebruikers in staat wilt stellen zich nu aan te melden bij OrgChart, moeten ze nu zijn ingericht in OrgChart. 

1. OrgChart Now ondersteunt just-in-time provisioning, die standaard is ingeschakeld. Er wordt een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot OrgChart Now als deze nog niet bestaat. De just-in-time gebruikersinrichtingsfunctie maakt alleen **een alleen-lezen** gebruiker wanneer een SSO-verzoek afkomstig is van een erkende IDP en de e-mail in de SAML-bewering niet in de gebruikerslijst wordt gevonden. Voor deze functie voor automatische inrichting moet u een toegangsgroep met de titel **Algemeen** maken in OrgChart Now. Volg de onderstaande stappen om een toegangsgroep te maken:

    a. Ga naar de optie **Groepen beheren** nadat u op de **versnelling** in de rechterbovenhoek van de gebruikersinterface hebt geklikt.

    ![OrgChart Nu-groepen](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecteer het pictogram **Toevoegen** en geef de groep **Algemeen** een naam en klik op **OK**. 

    ![Organigram nu toevoegen](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecteer de map(s) die u wilt dat de algemene of alleen-lezen gebruikers toegang hebben tot:

    ![OrgChart Now-mappen](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Vergrendel** de mappen zodat alleen beheerders gebruikers ze kunnen wijzigen. Druk vervolgens op **OK**.

    ![Organigram nu vergrendelen](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Als u **beheerders-** en **lees-/schrijfgebruikers** wilt maken, moet u handmatig een gebruiker maken om toegang te krijgen tot hun privilegeniveau via SSO. Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

    a. Log nu in bij OrgChart als beveiligingsbeheerder.

    b.  Klik op **Instellingen** in de rechterbovenhoek en navigeer vervolgens naar **Gebruikers beheren.**

    ![Instellingen voor Organigram nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klik op **Toevoegen** en voer de volgende stappen uit:

    ![OrgChart Nu beheren](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Voer **in** het tekstvak Gebruikersnaam de gebruikersnaam in zoals **brittasimon\@contoso.com**.

    * Voer in het tekstvak **E-mailadres** de e-mail van de gebruiker in, zoals **\@brittasimon contoso.com**.

    * Klik op**toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Organigram nu klikt in het toegangspaneel, moet u automatisch worden aangemeld bij het Organigram waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

