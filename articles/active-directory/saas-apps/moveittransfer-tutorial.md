---
title: 'Zelfstudie: Azure Active Directory-integratie met MOVEit Transfer - Azure AD-integratie | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en MOVEit Transfer - Azure AD-integratie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161316"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Zelfstudie: Azure Active Directory-integratie met MOVEit Transfer - Azure AD-integratie

In deze zelfstudie leert u hoe u MOVEit Transfer - Azure AD-integratie integreert met Azure Active Directory (Azure AD).
Integratie van MOVEit Transfer - Azure AD-integratie met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot MOVEit Transfer - Azure AD-integratie.
* U uw gebruikers automatisch laten inlogen bij MOVEit Transfer - Azure AD-integratie (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met MOVEit Transfer - Azure AD-integratie, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* MOVEit Transfer - Azure AD-integratie-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MOVEit Transfer - Azure AD-integratie ondersteunt **SP** geïnitieerde SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit-overdracht toevoegen - Azure AD-integratie vanuit de galerie

Als u de integratie van MOVEit Transfer - Azure AD-integratie in Azure AD wilt configureren, moet u MOVEit Transfer - Azure AD-integratie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om MOVEit Transfer - Azure AD-integratie vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **moveit-overdracht - Azure AD-integratie**in het zoekvak , selecteer **MOVEit Transfer - Azure AD-integratie** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![MOVEit Transfer - Azure AD-integratie in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met MOVEit Transfer - Azure AD-integratie op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in MOVEit Transfer - Azure AD-integratie.

Als u Azure AD-singlesign-on wilt configureren en testen met MOVEit Transfer - Azure AD-integratie, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[MovEit Transfer - Azure AD-integratie Single Sign-On configureren](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[MovEit Transfer - Azure AD-integratietestgebruiker](#create-moveit-transfer---azure-ad-integration-test-user)** - maken om een tegenhanger van Britta Simon te hebben in MOVEit Transfer - Azure AD-integratie die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren voor het configureren van Azure AD Single Sign-on met MOVEit Transfer - Azure AD-integratie:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **MOVEit Transfer - Azure AD-integratievan toepassing** voor de integratie van azure , de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het metagegevensbestand is geüpload, wordt de waarde **id** en **de URL-antwoord** automatisch ingevuld in de sectie **BasisSAML-configuratie:**

    ![MOVEit Transfer - Azure AD-integratiedomein en URL's met eenmalige aanmeldingsgegevens](common/sp-identifier-reply.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://contoso.com`

    > [!NOTE]
    > De **URL-waarde aanmelding** is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [MOVEit Transfer - Azure AD-integratieclientondersteuningsteam](https://community.ipswitch.com/s/support) om de waarde te krijgen. U het **bestand met metagegevens** van de serviceprovider downloaden van de **URL metagegevens** van de serviceprovider, die later wordt uitgelegd in het gedeelte **MovEit Transfer configureren - Azure AD-integratie Single Sign-On** van de zelfstudie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **MOVEit Transfer - Azure AD-integratie** de juiste URL(s) naar uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MOVEit Transfer configureren - Azure AD-integratie Single Sign-On

1. Meld u aan bij uw MOVEit Transfer-tenant als beheerder.

2. Klik in het linkernavigatiedeelvenster op **Instellingen**.

    ![Sectie Instellingen aan app-kant](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klik op de koppeling **Eén aanmelding,** die onder **Beveiligingsbeleid -> User Auth valt.**

    ![Beveiligingsbeleid aan app-kant](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klik op de URL-koppeling Metagegevens om het metagegevensdocument te downloaden.

    ![URL van metagegevens van serviceprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Controleer **of entiteits-id** **overeenkomt met de id** in de sectie **BasisSAML-configuratie** .
   * De URL **van de bevestigingslocatie** van de ConsumerService verifiëren komt overeen met de URL van de **ANTWOORD-URL** in de sectie **BasisSAML-configuratie.**
    
     ![Eenmalige aanmelding in de app configureren](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klik op de knop **Identiteitsprovider toevoegen** om een nieuwe Federatieve Identiteitsprovider toe te voegen.

    ![Id-provider toevoegen](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klik **op Bladeren...** om het metagegevensbestand te selecteren dat u hebt gedownload van de Azure-portal en klik vervolgens op **Identiteitsprovider toevoegen** om het gedownloade bestand te uploaden.

    ![SAML-identiteitsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selecteer '**Ja'** als **ingeschakeld** op de pagina Instellingen van de Federatie **voor federaties van federaties van federaties van federaties...** en klik op **Opslaan**.

    ![Federatieve instellingen voor identiteitsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Voer op de pagina Gebruikersinstellingen van de Federatie **van Federated Identity Provider** de volgende acties uit:
    
    ![Instellingen voor federatieve identiteitbewerken bewerken](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecteer **SAML NameID** als **aanmeldingsnaam**.
    
    b. Selecteer **Andere** als **volledige naam** en zet in het tekstvak **Kenmerknaam** de waarde: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecteer **Anders** als **e-mail** en in het `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`tekstvak **Kenmerknaam** wordt de waarde geplaatst: .
    
    d. Selecteer **Ja** als **account automatisch maken bij signon**.
    
    e. Klik op de knop **Save**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot MOVEit Transfer - Azure AD-integratie.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, Selecteer **Alle toepassingen**en selecteer **vervolgens MOVEit Transfer - Azure AD-integratie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **MOVEit Transfer - Azure AD-integratie**in de lijst met toepassingen.

    ![De koppeling MOVEit Transfer - Azure AD-integratie in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit Transfer - Azure AD-integratietestgebruiker maken

Het doel van deze sectie is het maken van een gebruiker genaamd Britta Simon in MOVEit Transfer - Azure AD-integratie. MOVEit Transfer - Azure AD-integratie ondersteunt just-in-time provisioning, die u hebt ingeschakeld. Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot MOVEit Transfer - Azure AD-integratie als deze nog niet bestaat.

>[!NOTE]
>Als u handmatig een gebruiker wilt maken, moet u contact opnemen met het ondersteuningsteam voor [azure-integratieclient van MOVEit Transfer - Azure AD-integratie.](https://community.ipswitch.com/s/support)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de MOVEit Transfer - Azure AD-integratietegel in het Access-paneel klikt, moet u automatisch worden aangemeld bij de MOVEit Transfer - Azure AD-integratie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

