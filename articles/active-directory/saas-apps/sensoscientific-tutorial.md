---
title: 'Zelfstudie: Azure Active Directory-integratie met sensoscientific draadloos temperatuurbewakingssysteem | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SensoScientific Wireless Temperature Monitoring System.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091273"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Zelfstudie: Azure Active Directory-integratie met sensoscientific draadloos temperatuurbewakingssysteem

In deze zelfstudie leert u hoe u het SensoScientific Wireless Temperature Monitoring System integreren met Azure Active Directory (Azure AD).
De integratie van het SensoScientific Wireless Temperature Monitoring System met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot het draadloze temperatuurbewakingssysteem van SensoScientific.
* U uw gebruikers automatisch laten inloggen op het SensoScientific Wireless Temperature Monitoring System (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie van Azure wilt configureren met het SensoScientific Wireless Temperature Monitoring System, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* SensoScientific Wireless Temperature Monitoring System single sign-on enabled subscription SensoScientific Wireless Temperature Monitoring System

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SensoScientific Wireless Temperature Monitoring System ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Het toevoegen van SensoScientific Wireless Temperature Monitoring System uit de galerij

Als u de integratie van het SensoScientific Wireless Temperature Monitoring System in Azure AD wilt configureren, moet u sensoscientific wireless temperature monitoring system uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om sensoscientific draadloos temperatuurbewakingssysteem toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer in het zoekvak **SensoScientific Wireless Temperature Monitoring System**, selecteer **SensoScientific Wireless Temperature Monitoring System** van het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

    ![SensoScientific Wireless Temperature Monitoring System in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SensoScientific Wireless Temperature Monitoring System op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding op het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in het SensoScientific Wireless Temperature Monitoring System.

Als u Azure AD single sign-on wilt configureren en testen met sensoScientific Wireless Temperature Monitoring System, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer SensoScientific Wireless Temperature Monitoring System Single Sign-On](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak sensoscientific draadloze temperatuur monitoring systeem test gebruiker](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - om een tegenhanger van Britta Simon in SensoScientific Wireless Temperature Monitoring System dat is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met sensoScientific Wireless Temperature Monitoring System:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **SensoScientific Wireless Temperature Monitoring** System-toepassingsintegratiepagina De optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![SensoScientific Wireless Temperature Monitoring System Domein en URL's single sign-on informatie](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **SensoScientific Wireless Temperature Monitoring System** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>SensoScientific Wireless Temperature Monitoring System Single Sign-On configureren

1. Meld u aan bij uw SensoScientific Wireless Temperature Monitoring System-toepassing als beheerder.

1. Klik in het navigatiemenu bovenaan op **Configureren** en ga naar **Configureren** onder **Eén aanmeldingsinstellingen** om de instellingen voor eenmalig aanmelden te openen en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Selecteer **De naam van de uitgever** als Azure AD.

    b. Plak in het tekstvak **van de url van de uitgever** de Azure **AD-id** die u hebt gekopieerd vanuit azure-portal.

    c. Plak in het tekstvak **VOOR één aanmeldingsservice URL** de **aanmeldings-URL** die u hebt gekopieerd van Azure-portal.

    d. Plak in het tekstvak **URL van de voormeldingsservice** de **URL van de afmelding** die u hebt gekopieerd uit azure-portal.

    e. Blader door het certificaat dat u hebt gedownload van de Azure-portal en upload hier.

    f. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot sensoScientific Wireless Temperature Monitoring System.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens SensoScientific Wireless Temperature Monitoring System**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **SensoScientific Wireless Temperature Monitoring System**.

    ![De koppeling van het SensoScientific Wireless Temperature Monitoring System in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific Wireless Temperature Monitoring System test gebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij het SensoScientific Wireless Temperature Monitoring System, moeten ze zijn ingericht in het SensoScientific Wireless Temperature Monitoring System. Werk samen met het  [ondersteuningsteam van het SensoScientific Wireless Temperature Monitoring System](https://www.sensoscientific.com/contact-us/)om de gebruikers toe te voegen aan het SensoScientific Wireless Temperature Monitoring System-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SensoScientific Wireless Temperature Monitoring System in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het SensoScientific Wireless Temperature Monitoring System waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

