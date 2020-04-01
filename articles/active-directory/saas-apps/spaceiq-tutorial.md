---
title: 'Zelfstudie: Azure Active Directory-integratie met SpaceIQ | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b3358ad473ede9e8d78a835e8c68e690e5340638
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090098"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Zelfstudie: Azure Active Directory-integratie met SpaceIQ

In deze zelfstudie leert u hoe u SpaceIQ integreert met Azure Active Directory (Azure AD).
De integratie van SpaceIQ met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot SpaceIQ.
* U uw gebruikers automatisch laten inloggen op SpaceIQ (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SpaceIQ wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* SpaceIQ-abonnement voor eenmalig aanmelden

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SpaceIQ ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-spaceiq-from-the-gallery"></a>SpaceIQ toevoegen vanuit de galerie

Als u de integratie van SpaceIQ in Azure AD wilt configureren, moet u SpaceIQ vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SpaceIQ vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **SpaceIQ**in het zoekvak , selecteer **SpaceIQ** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![SpaceIQ in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SpaceIQ op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SpaceIQ.

Als u Azure AD-singlesign-aan met SpaceIQ wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer SpaceIQ Single Sign-On](#configure-spaceiq-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak SpaceIQ-testgebruiker](#create-spaceiq-test-user)** - om een tegenhanger van Britta Simon in SpaceIQ te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met SpaceIQ te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **SpaceIQ-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over SpaceIQ-domein en URL's met eenmalige aanmelding](common/idp-intiated.png)

    a. Typ de URL in het tekstvak **Id:**`https://api.spaceiq.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Werk deze waarden bij met de werkelijke url en id van het antwoord die later in de zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **SpaceIQ instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-spaceiq-single-sign-on"></a>SpaceIQ-aanmelding configureren

1. Open een nieuw browservenster en meld u aan bij uw SpaceIQ-omgeving als beheerder.

1. Zodra u bent ingelogd, klikt u op het puzzelbord rechtsboven en klikt u op **Integraties**

    ![Accountinstellingen](./media/spaceiq-tutorial/setting1.png) 

1. Klik onder **Alle provisioning & SSO**op de **Azure-tegel** om een exemplaar van Azure als IDP toe te voegen.

    ![SAML-pictogram](./media/spaceiq-tutorial/setting2.png)

1. Voer in het dialoogvenster **SSO** de volgende stappen uit:

    ![SAML-verificatie-instellingen](./media/spaceiq-tutorial/setting3.png)

    a. Plak in het **vak URL van de SAML-uitgever** de azure **AD-id-waarde** die is gekopieerd uit het configuratievenster van de Azure AD-toepassings.

    b. Kopieer de URL van **SAML CallBack Endpoint (alleen-lezen)** en plak de waarde in het vak **URL beantwoorden** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    c. Kopieer de **URI-waarde voor SAML-doelgroep (alleen-lezen)** en plak de waarde in het vak **Id** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    d. Open het gedownloade certificaatbestand in kladblok, kopieer de inhoud en plak het vervolgens in het vak **X.509-certificaat.**

    e. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SpaceIQ.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens SpaceIQ**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SpaceIQ**in de lijst met toepassingen .

    ![De Koppeling SpaceIQ in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-spaceiq-test-user"></a>SpaceIQ-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in SpaceIQ. Work [SpaceIQ support team](mailto:eng@spaceiq.com) om de gebruikers toe te voegen in het SpaceIQ-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de SpaceIQ-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SpaceIQ waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

