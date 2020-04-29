---
title: 'Zelf studie: integratie Azure Active Directory met Zscaler-beheerder voor persoonlijke toegang | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler private Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67085645"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Zelf studie: integratie Azure Active Directory met Zscaler-beheerder voor persoonlijke toegang

In deze zelf studie leert u hoe u Zscaler private Access Administrator integreert met Azure Active Directory (Azure AD).
Het integreren van Zscaler private Access-beheerder met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de Zscaler-beheerder voor persoonlijke toegang.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor Zscaler beheerder voor persoonlijke toegang (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Zscaler-beheerder voor persoonlijke toegang wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor Zscaler private Access Administrator eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler private Access-beheerder ondersteunt SSO die door **SP** en **IDP** is geïnitieerd

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>De Zscaler-beheerder voor persoonlijke toegang toevoegen vanuit de galerie

Als u de integratie van Zscaler private Access-beheerder wilt configureren in azure AD, moet u Zscaler private Access Administrator uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Zscaler persoonlijke toegangs beheerder toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zscaler private Access Administrator** **in het** zoekvak en klik op de knop **toevoegen** om de toepassing toe te voegen.

    ![Zscaler persoonlijke toegangs beheerder in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Zscaler private Access Administrator op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler private Access Administrator tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Zscaler private Access Administrator, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Zscaler private Access Administrator configureren](#configure-zscaler-private-access-administrator-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Zscaler private Access-beheerder test gebruiker](#create-zscaler-private-access-administrator-test-user)** -als u een equivalent van Julia Simon in Zscaler private Access Administrator wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Zscaler-beheerder voor persoonlijke toegang:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Zscaler private Access-beheer** toepassing de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Zscaler persoonlijke toegang Administrator domein en Url's eenmalige aanmelding gegevens](common/idp-relay.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `idpadminsso`

5.  Als u de toepassing in de modus door **SP** gestart wilt configureren, voert u de volgende stap uit:

    ![Zscaler persoonlijke toegang Administrator domein en Url's eenmalige aanmelding gegevens](common/both-signonurl.png)

    Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Zscaler private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer de gewenste URL ('s) volgens uw vereiste in het gedeelte **Stel Zscaler private Access-beheerder** in.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Eenmalige aanmelding van Zscaler private Access Administrator configureren

1. Meld u in een ander webbrowser venster aan Zscaler beheerder voor persoonlijke toegang als beheerder.

2. Klik bovenaan op **beheer** en navigeer naar **verificatie** sectie op IDP- **configuratie**.

    ![Zscaler beheerder voor persoonlijke toegang](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klik in de rechter bovenhoek op **IDP-configuratie toevoegen**. 

    ![Zscaler private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Voer de volgende stappen uit op de pagina **IDP-configuratie toevoegen** :
 
    ![Zscaler private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klik op **bestand selecteren** om het gedownloade meta gegevensbestand te uploaden vanuit Azure AD in het IDP-bestand voor het **uploaden van meta gegevens** .

    b. De **meta gegevens van IDP** worden gelezen uit Azure AD en alle veld gegevens worden ingevuld zoals hieronder wordt weer gegeven.

    ![Zscaler private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecteer **eenmalige aanmelding** als **Administrator**.

    d. Selecteer uw domein in het veld **domeinen** .
    
    e. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot de beheerder van de persoonlijke toegang van Zscaler.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer **Zscaler beheerder voor persoonlijke toegang**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zscaler private Access Administrator**.

    ![De Zscaler-koppeling voor de persoonlijke toegangs beheerder in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Test gebruiker voor Zscaler private Access-beheerder maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij de Zscaler-beheerder voor persoonlijke toegang, moeten ze worden ingericht in Zscaler-beheerder voor persoonlijke toegang. In het geval van een persoonlijke Zscaler-beheerder is het inrichten een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij de bedrijfs site van uw Zscaler private Access-beheerder.

2. Klik bovenaan op **beheer** en navigeer naar **verificatie** sectie op IDP- **configuratie**.

    ![Zscaler beheerder voor persoonlijke toegang](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klik op **beheerders** aan de linkerkant van het menu.

    ![Beheerder van Zscaler private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Klik in de rechter bovenhoek op **beheerder toevoegen**:

    ![Beheerder van Zscaler private Access Administrator toevoegen](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Voer de volgende stappen uit op de pagina **beheerder toevoegen** :

    ![Beheerder van Zscaler private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Voer in het tekstvak **gebruikers naam** het e-mail adres BrittaSimon@contoso.comvan de gebruiker in.

    b. Typ het wacht woord in het tekstvak **wacht woord** .

    c. Typ het wacht woord in het tekstvak **wacht woord bevestigen** .

    d. Selecteer **rol** als **Zscaler-beheerder voor persoonlijke toegang**.

    e. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld BrittaSimon@contoso.com.

    f. Typ in het tekstvak **telefoon** het telefoon nummer.

    g. Selecteer de tijd zone in het tekstvak **tijd zone** .

    h. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zscaler private Access Administrator in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Zscaler-beheerder voor persoonlijke toegang waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

