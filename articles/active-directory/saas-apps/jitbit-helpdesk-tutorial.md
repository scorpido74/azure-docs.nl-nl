---
title: 'Zelf studie: integratie Azure Active Directory met Jitbit-Help Desk | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jitbit Help Desk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 29addcd62afd193af83196b2d942e9778ff3f031
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099411"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Zelf studie: integratie Azure Active Directory met Jitbit Help Desk

In deze zelf studie leert u hoe u Jitbit-Help Desk integreert met Azure Active Directory (Azure AD).
Integratie van Jitbit-Help Desk met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de Jitbit-Help Desk.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij de Jitbit-Help Desk (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Jitbit Help Desk wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding voor Jitbit-helpdesk

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Jitbit-Help Desk ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Jitbit Help Desk toevoegen vanuit de galerie

Als u de integratie van Jitbit-Help Desk wilt configureren in azure AD, moet u Jitbit-Help Desk toevoegen uit de galerie aan uw lijst met beheerde SaaS-apps.

**Als u Jitbit-Help Desk wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Jitbit Help Desk**in het zoekvak, selecteer **Jitbit Help Desk** in het resultaten paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Jitbit-Help Desk in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met Jitbit-Help Desk configureren en testen op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jitbit-Help Desk tot stand brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Jitbit Help Desk, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Jitbit-Help Desk configureren](#configure-jitbit-helpdesk-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Jitbit-gebruikers-helpdesk test gebruiker](#create-jitbit-helpdesk-test-user)** -om een equivalent van Julia Simon in Jitbit-Help Desk te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Jitbit Help Desk:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Jitbit Help Desk** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over Jitbit-helpdesk domein en Url's eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: 
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Jitbit helpdesk client](https://www.jitbit.com/support/) om deze waarde op te halen.

    b. Typ in het tekstvak **id (Entiteits-ID)** een URL als volgt:`https://www.jitbit.com/web-helpdesk/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **Jitbit Help Desk instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Eenmalige aanmelding van Jitbit-Help Desk configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de bedrijfs site van uw Jitbit-Help Desk.

1. Klik in de werk balk bovenaan op **beheer**.

    ![Beheer](./media/jitbit-helpdesk-tutorial/ic777681.png "Beheer")

1. Klik op **algemene instellingen**.

    ![Gebruikers, bedrijven en machtigingen](./media/jitbit-helpdesk-tutorial/ic777680.png "Gebruikers, bedrijven en machtigingen")

1. Voer de volgende stappen uit in de sectie **verificatie-instellingen** configureren:

    ![Verificatie-instellingen](./media/jitbit-helpdesk-tutorial/ic777683.png "Verificatie-instellingen")

    a. Selecteer **SAML 2,0 eenmalige aanmelding inschakelen**om u aan te melden met behulp van eenmalige aanmelding (SSO) met **OneLogin**.

    b. Plak in het tekstvak **URL eind punt** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Open uw met **base 64** versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **X. 509-certificaat**

    d. Klik op **wijzigingen opslaan**.

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

In deze sectie schakelt u Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan de Jitbit-Help Desk.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Jitbit Help Desk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Jitbit Help Desk**.

    ![De koppeling voor de Help Desk Jitbit in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-jitbit-helpdesk-test-user"></a>Test gebruiker van Jitbit-Help Desk maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij de Jitbit-Help Desk, moeten ze worden ingericht in de Jitbit-Help Desk. In het geval van een Jitbit-Help Desk is het inrichten een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **Jitbit Help Desk** -Tenant.

1. Klik in het menu aan de bovenkant op **beheer**.

    ![Beheer](./media/jitbit-helpdesk-tutorial/ic777681.png "Beheer")

1. Klik op **gebruikers, bedrijven en machtigingen**.

    ![Gebruikers, bedrijven en machtigingen](./media/jitbit-helpdesk-tutorial/ic777682.png "Gebruikers, bedrijven en machtigingen")

1. Klik op **gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/jitbit-helpdesk-tutorial/ic777685.png "Gebruiker toevoegen")

1. Typ in de sectie maken de gegevens van het Azure AD-account dat u als volgt wilt inrichten:

    ![Maken](./media/jitbit-helpdesk-tutorial/ic777686.png "Maken")

   a. Typ in het tekstvak **username** de gebruikers naam van de gebruiker, zoals **BrittaSimon**.

   b. Typ in het tekstvak **e-mail** het e-mail adres van **BrittaSimon@contoso.com**de gebruiker zoals.

   c. Typ in het tekstvak **voor naam** de voor naam van de gebruiker, zoals **Julia**.

   d. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

   e. Klik op **maken**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van Jitbit-helpdesk gebruikers of Api's die worden geleverd door de Jitbit-Help Desk, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Jitbit helpdesk tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Jitbit-Help Desk waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
