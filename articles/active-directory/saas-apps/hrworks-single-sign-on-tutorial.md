---
title: 'Zelf studie: integratie met HRworks-eenmalige aanmelding Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en HRworks eenmalige aanmelding.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: a36266c14531f935779266829402392dc4a03411
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705999"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Zelf studie: integratie met HRworks-eenmalige aanmelding Azure Active Directory

In deze zelf studie leert u hoe u eenmalige aanmelding integreert met Azure Active Directory (Azure AD).
Het integreren van HRworks single sign-on met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot HRworks eenmalige aanmelding.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor HRworks eenmalige aanmelding (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met HRworks eenmalige aanmelding wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding van HRworks-abonnementen

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Eenmalige aanmelding voor HRworks ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Eenmalige aanmelding voor HRworks toevoegen vanuit de galerie

Als u de integratie van HRworks eenmalige aanmelding wilt configureren in azure AD, moet u HRworks eenmalige aanmelding toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om eenmalige aanmelding voor HRworks toe te voegen in de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **HRworks eenmalige aanmelding**, selecteer **HRworks eenmalige aanmelding** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![HRworks eenmalige aanmelding in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met HRworks eenmalige aanmelding op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in HRworks eenmalige aanmelding tot stand brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met HRworks eenmalige aanmelding, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. Eenmalige aanmelding van **[HRworks configureren](#configure-hrworks-single-sign-on-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een HRworks-test gebruiker voor eenmalige aanmelding](#create-hrworks-single-sign-on-test-user)** , zodat er een equivalent van Julia Simon in HRworks eenmalige aanmelding is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met HRworks eenmalige aanmelding te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **HRworks single sign-on** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens over eenmalige aanmelding van HRworks en Url's voor eenmalige aanmelding](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van de client voor eenmalige aanmelding van HRworks](https://www.hrworks.de/dienstleistungen/support/) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **eenmalige aanmelding voor HRworks instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Eenmalige aanmelding HRworks eenmalige aanmelding configureren

1. Meld u in een ander webbrowser venster aan om eenmalige aanmelding in te HRworks als beheerder.

2. Klik op **beheerder** > **basis principes** > **beveiliging** > **eenmalige aanmelding** aan de linkerkant van de menu balk en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Schakel het selectie vakje **eenmalige aanmelding gebruiken** in.

    b. Selecteer **XML-meta** gegevens als **invoer methode voor meta gegevens**.

    c. Selecteer **afzonderlijke NameID-id** als **waarde voor NameID**.

    d. Open in Klad blok de XML voor meta gegevens die u hebt gedownload van de Azure Portal, kopieer de inhoud en plak deze in het tekstvak **meta gegevens** .

    e. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. Typ in het veld **gebruikers naam** de gebruikers naam, zoals BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan eenmalige aanmelding voor HRworks.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer **eenmalige aanmelding HRworks**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **eenmalige aanmelding HRworks**.

    ![De HRworks-koppeling voor eenmalige aanmelding in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Test gebruiker voor eenmalige aanmelding voor HRworks maken

Als u Azure AD-gebruikers wilt inschakelen, meldt u zich aan bij HRworks eenmalige aanmelding, moeten ze worden ingericht in HRworks eenmalige aanmelding. In HRworks-eenmalige aanmelding is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan om eenmalige aanmelding te HRworks als beheerder.

2. Klik op **beheerder** > **personen** > **personen** > **nieuwe persoon** aan de linkerkant van de menu balk.

     ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. Klik in het pop-upvenster op **volgende**.

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Vul in het pop-upvenster **nieuwe persoon met land voor juridische voor waarden** de relevante gegevens in, zoals de **voor naam**, **Achternaam** en klik op **maken**.
    
    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel HRworks single sign-on in het toegangs venster klikt, moet u automatisch worden aangemeld bij de eenmalige aanmelding van HRworks waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

