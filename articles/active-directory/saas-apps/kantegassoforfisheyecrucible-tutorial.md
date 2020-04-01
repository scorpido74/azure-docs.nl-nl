---
title: 'Zelfstudie: Azure Active Directory-integratie met Kantega SSO voor FishEye/Crucible | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kantega SSO voor FishEye/Crucible.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f262f94c451d5dcffd933bdebb1374b8733b9fd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67595176"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Zelfstudie: Azure Active Directory-integratie met Kantega SSO voor FishEye/Crucible

In deze zelfstudie leert u hoe Kantega SSO voor FishEye/Crucible wordt geïntegreerd met Azure Active Directory (Azure AD).
De integratie van Kantega SSO voor FishEye/Crucible met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Kantega SSO voor FishEye/Crucible.
* U uw gebruikers automatisch laten aanmelden bij Kantega SSO voor FishEye/Crucible (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Kantega SSO voor FishEye/Crucible, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Kantega SSO voor FishEye/Crucible single sign-on enabled subscription Kantega SSO for FishEye/Crucible single sign-on enabled subscription Kantega SSO for FishEye/Crucible single sign-on enabled subscription Kante

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kantega SSO voor FishEye/Crucible ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Het toevoegen van Kantega SSO voor FishEye / Crucible uit de galerij

Als u de integratie van Kantega SSO voor FishEye/Crucible in Azure AD wilt configureren, moet u Kantega SSO voor FishEye/Crucible vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Kantega SSO voor FishEye/Crucible toe te voegen voor FishEye/Crucible uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Kantega SSO voor FishEye/Crucible in**het zoekvak en selecteer **Kantega SSO voor FishEye/Crucible** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Kantega SSO voor FishEye/Crucible in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Kantega SSO voor FishEye/Crucible op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding voor het werk moet een koppeling strekken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Kantega SSO voor FishEye/Crucible worden vastgesteld.

Als u Azure AD-single sign-on met Kantega SSO voor FishEye/Crucible wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Kantega SSO voor FishEye/Crucible Single Sign-On](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Kantega SSO voor FishEye/Crucible-testgebruiker](#create-kantega-sso-for-fisheyecrucible-test-user)** - om een tegenhanger van Britta Simon in Kantega SSO voor FishEye/Crucible te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD single sign-on te configureren met Kantega SSO voor FishEye/Crucible:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Kantega SSO voor FishEye/Crucible-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Kantega SSO voor FishEye/Crucible Domain en URL's single sign-on informatie](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Kantega SSO voor FishEye/Crucible Domain en URL's single sign-on informatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. Deze waarden worden ontvangen tijdens de configuratie van FishEye / Crucible plugin die later wordt uitgelegd in de tutorial.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **Kantega SSO instellen voor FishEye/Crucible** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Kantega SSO configureren voor FishEye/Crucible Single Sign-On

1. Meld u in een ander browservenster aan bij uw FishEye/Crucible on-premises server als beheerder.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Klik onder de sectie Systeeminstellingen op **Nieuwe invoegtoepassingen zoeken**. 

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Zoek **kantega SSO naar Crucible** en klik op De knop **Installeren** om de nieuwe SAML-plug-in te installeren.

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. De installatie van de plug-in wordt gestart. 

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Zodra de installatie is voltooid. Klik op **Sluiten**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Klik op **Beheren**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Klik op **Configure** om de nieuwe invoegtoepassing te configureren. 

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. In de **SAML** sectie. Selecteer **Azure Active Directory (Azure AD)** in de vervolgkeuzelijst **Identiteitsprovider toevoegen.**

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Selecteer abonnementsniveau als **Basis**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. Voer in de sectie **App-eigenschappen** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Kopieer de **URI-waarde voor app-id's** en gebruik deze als **id, url voor beantwoorden en aanmelding smaken** in de sectie **BasisSAML-configuratie** in Azure-portal.

    b. Klik op **Volgende**.

1. Voer in de sectie **Metagegevens import** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Selecteer **Metagegevensbestand op mijn computer**en upload metagegevensbestand, dat u hebt gedownload van de Azure-portal.

    b. Klik op **Volgende**.

1. Voer in de sectie **Naam en SSO de** volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Naam van de identiteitsprovider toevoegen aan het tekstvak **identiteitsprovider** (bijvoorbeeld Azure AD).

    b. Klik op **Volgende**.

1. Controleer het certificaat Ondertekenen en klik op **Volgende**.   

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. Voer in de sectie **FishEye-gebruikersaccounts** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Selecteer Indien nodig gebruikers maken in de interne directory van **FishEye** en voer u de juiste naam van de groep in voor gebruikers (kan meerdere nee zijn. van groepen gescheiden door komma).

    b. Klik op **Volgende**.

1. Klik op **Voltooien**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. Voer in de sectie **Bekende domeinen voor Azure AD** de volgende stappen uit:  

    ![Eenmalige aanmelding configureren](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Selecteer **Bekende domeinen** in het linkerdeelvenster van de pagina.

    b. Voer domeinnaam in het tekstvak **Bekende domeinen** in.

    c. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Kantega SSO voor FishEye/Crucible.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, Selecteer **Alle toepassingen**en selecteer **Vervolgens Kantega SSO voor FishEye/Crucible**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Kantega SSO voor FishEye/Crucible**in de lijst met toepassingen.

    ![De Kantega SSO voor FishEye/Crucible link in de lijst toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Kantega SSO maken voor FishEye/Crucible testgebruiker

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij FishEye/Crucible, moeten ze zijn ingericht in FishEye/Crucible. In Kantega SSO voor FishEye/Crucible is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw Crucible on-premises server als beheerder.

1. Plaats de muisaanwijzer op het tandwiel en klik op **de gebruikers**.

    ![Werknemer toevoegen](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. Klik onder de sectie **Gebruikers** op **Gebruiker toevoegen**.

    ![Werknemer toevoegen](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Voer op de pagina **Nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![Werknemer toevoegen](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. Typ in het tekstvak **Weergavenaam** de weergavenaam van de gebruiker, zoals Britta Simon.

    c. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    e. Voer in het tekstvak **Wachtwoord bevestigen** het wachtwoord van de gebruiker opnieuw in.

    f. Klik op**toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Kantega SSO voor FishEye/Crucible-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Kantega SSO voor FishEye/Crucible waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)