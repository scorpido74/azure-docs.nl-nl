---
title: 'Zelf studie: integratie met beheer Services voor aanwezigheid Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory-en aanwezigheids beheer Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143d0afce7a3644286703a9eba0da1ee45305f2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67106544"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Zelf studie: integratie met beheer services van de aanwezigheid Azure Active Directory

In deze zelf studie leert u hoe u de services voor het beheer van de aanwezigheid kunt integreren met Azure Active Directory (Azure AD).
Het integreren van de beheer Services voor de aanwezigheid van Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de beheer services van de aanwezigheid.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij beheer Services (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met beheer Services voor aanwezigheid wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor aanwezigheids beheer Services voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Aanwezigheids beheer services bieden ondersteuning voor door **SP** GEÏNITIEERDe SSO

## <a name="adding-attendance-management-services-from-the-gallery"></a>Beheer Services voor de aanwezigheid toevoegen vanuit de galerie

Als u de integratie van de beheer Services voor de aanwezigheid wilt configureren in azure AD, moet u beheer Services voor de aanwezigheid van de-galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om de beheer Services voor de aanwezigheid van een beheerder toe te voegen in de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak functie **Beheer Services**, selecteer functie voor het **beheer** van de aanwezigheid en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Aanwezigheids beheer Services in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met aanwezigheids beheer Services op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Management Services voor aanwezigheid worden ingesteld.

U moet de volgende bouw stenen volt ooien om de eenmalige aanmelding van Azure AD te configureren en te testen met beheer Services voor aanwezigheid:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor aanwezigheids beheer Services configureren](#configure-attendance-management-services-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een test gebruiker voor de beheer Services](#create-attendance-management-services-test-user)** voor de aanwezigheid van een beheerder, zodat er een soort Julia Simon in aanwezigheids beheer Services is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met beheer Services voor aanwezigheid:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina voor de integratie van de **Beheer Services** -toepassing **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over het domein en Url's voor aanwezigheids beheer Services voor eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://id.obc.jp/<tenant information >/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team voor de aanwezigheid van Management Services-client](https://www.obcnet.jp/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer de gewenste URL ('s) volgens uw vereiste in het gedeelte Beheer Services voor de functie voor het **instellen van** de functie voor het beheren van de aanwezigheid.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Eenmalige aanmelding voor aanwezigheids beheer Services configureren

1. Meld u in een ander browser venster aan bij de bedrijfs site van uw aanwezigheids beheer Services als beheerder.

1. Klik op **SAML-verificatie** in het **gedeelte Security Management**.

    ![Configuratie van aanwezigheids beheer Services](./media/attendancemanagementservices-tutorial/user1.png)

1. Voer de volgende stappen uit:

    ![Configuratie van aanwezigheids beheer Services](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecteer **SAML-verificatie gebruiken**.

    b. Plak in het tekstvak **id** de waarde van de **Azure ad-id** -waarde die u van Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **URL verificatie-eind punt** de waarde van de waarde van de **aanmeldings-URL** , die u hebt gekopieerd uit Azure Portal.

    d. Klik op **Selecteer een bestand** om het certificaat te uploaden dat u hebt gedownload van Azure AD.

    e. Selecteer **wachtwoord verificatie uitschakelen**.

    f. Klik op **registratie**

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

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen aan de beheer services van de aanwezigheid.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Beheer Services**voor de aanwezigheid.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **aanwezigheids beheer Services**.

    ![De koppeling voor de beheer Services voor aanwezigheid in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-attendance-management-services-test-user"></a>Een test gebruiker voor de beheer Services voor de aanwezigheid maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij de beheer services van de aanwezigheid, moeten ze worden ingericht in beheer Services voor aanwezigheid. In het geval van aanwezigheids beheer Services is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij de bedrijfs site van uw aanwezigheids beheer Services als beheerder.

1. Klik op **gebruikers beheer** in het **gedeelte Security Management**.

    ![Werknemer toevoegen](./media/attendancemanagementservices-tutorial/user5.png)

1. Klik op **nieuwe regels aanmelden**.

    ![Werknemer toevoegen](./media/attendancemanagementservices-tutorial/user3.png)

1. Voer in de sectie **informatie over OBCiD** de volgende stappen uit:

    ![Werknemer toevoegen](./media/attendancemanagementservices-tutorial/user4.png)

    a. Typ in het tekstvak **OBCiD** het e-mail adres van `BrittaSimon\@contoso.com`de gebruiker zoals.

    b. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    c. Klik op **registratie**

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel beheer Services voor aanwezigheid in het toegangs venster klikt, moet u automatisch worden aangemeld bij de beheer Services voor de aanwezigheid waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)