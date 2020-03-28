---
title: 'Zelfstudie: Azure Active Directory-integratie met Ziflow | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086196"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Zelfstudie: Azure Active Directory-integratie met Ziflow

In deze zelfstudie leert u hoe u Ziflow integreert met Azure Active Directory (Azure AD).
De integratie van Ziflow met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Ziflow.
* U uw gebruikers automatisch laten aanmelden bij Ziflow (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Ziflow wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Ziflow single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ziflow steunt **door SP** geïnitieerde SSO

## <a name="adding-ziflow-from-the-gallery"></a>Ziflow toevoegen vanuit de galerie

Als u de integratie van Ziflow in Azure AD wilt configureren, moet u Ziflow vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Ziflow vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Ziflow**in het zoekvak , selecteer **Ziflow** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Ziflow in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on with Ziflow op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Ziflow.

Als u Azure AD-single sign-on met Ziflow wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Ziflow Single Sign-On](#configure-ziflow-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Ziflow-testgebruiker](#create-ziflow-test-user)** - om een tegenhanger van Britta Simon in Ziflow te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Ziflow te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Ziflow-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Ziflow Domain en URL's single sign-on informatie](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > De bovenstaande waarden zijn niet echt. U werkt de unieke ID-waarde in de ID-waarde en de URL aan met de werkelijke waarde, die later in de zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Ziflow instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ziflow-single-sign-on"></a>Ziflow-aanmelding configureren

1. Meld u in een ander browservenster aan bij Ziflow als beveiligingsbeheerder.

2. Klik rechtsboven op Avatar en klik vervolgens op **Account beheren**.

    ![Ziflow-configuratie beheren](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Klik linksboven op **Eén aanmelding**.

    ![Ziflow-configuratieteken](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Voer op de pagina **Single Sign-On** de volgende stappen uit:

    ![Ziflow-configuratie single](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecteer **Type** als **SAML2.0**.

    b. Plak in het tekstvak **AanmeldingsURL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van de Azure-portal.

    c. Upload het basis-64 gecodeerde certificaat dat u hebt gedownload van de Azure-portal naar het **X509-ondertekeningscertificaat.**

    d. Plak in het tekstvak **Afmelden URL** de waarde van **de URL van Afmelden**, die u hebt gekopieerd uit de Azure-portal.

    e. Kopieer in de sectie **Configuratie-instellingen voor uw id-provider** de gemarkeerde unieke ID-waarde en sluit deze toe met de id en aanmeldings-URL in de **basisSAML-configuratie** op Azure-portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld brittasimon@yourcompanydomain.extensiontype gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Ziflow.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Ziflow**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Ziflow**in de lijst met toepassingen .

    ![De Ziflow-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ziflow-test-user"></a>Ziflow-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Ziflow, moeten ze worden ingericht in Ziflow. In Ziflow is inrichten een handmatige taak.

Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u aan bij Ziflow als beveiligingsbeheerder.

2. Navigeer naar **Personen** bovenaan.

    ![Ziflow-configuratiepersonen](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klik **op Toevoegen** en klik vervolgens op Gebruiker **toevoegen**.

    ![Ziflow-configuratie die gebruiker toevoegt](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Voer in de pop-up **Een gebruiker** toevoegen de volgende stappen uit:

    ![Ziflow-configuratie die gebruiker toevoegt](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals Britta.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals Simon.

    d. Selecteer uw Ziflow-rol.

    e. Klik **op 1 gebruiker toevoegen**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Ziflow-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Ziflow waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

