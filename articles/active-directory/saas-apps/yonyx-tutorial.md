---
title: 'Zelfstudie: Azure Active Directory-integratie met Yonyx-interactieve handleidingen | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Yonyx-interactieve hulplijnen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: b2a22beb1c88e30ce0c88a0f57ec71bbdc45ece8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086448"
---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Zelfstudie: Azure Active Directory-integratie met yonyx-interactieve handleidingen

In deze zelfstudie leert u hoe u Yonyx-interactieve hulplijnen integreren met Azure Active Directory (Azure AD).
De integratie van Yonyx-interactieve gidsen met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot yonyx-interactieve gidsen.
* U uw gebruikers automatisch laten aanmelden bij Yonyx Interactive Guides (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Yonyx-interactieve hulplijnen, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Yonyx Interactive Guides single sign-on enabled subscription Yonyx Interactive Guides single sign-on enabled subscription Yonyx Interactive Guides single sign-on enabled subscription Yon

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Yonyx Interactive Guides ondersteunt **SP** geïnitieerde SSO

* Yonyx Interactive Guides ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Yonyx-interactieve gidsen toevoegen vanuit de galerie

Als u de integratie van Yonyx-interactieve hulplijnen in Azure AD wilt configureren, moet u Yonyx-interactieve hulplijnen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om yonyx-interactieve hulplijnen uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer yonyx-interactieve hulplijnen in het zoekvak en selecteer **Yonyx-interactieve hulplijnen** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen. **Yonyx Interactive Guides**

     ![Yonyx Interactieve Gidsen in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Yonyx Interactive Guides op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Yonyx Interactive Guides.

Als u Azure AD-singlesign-aan wilt configureren en testen met Yonyx-interactieve hulplijnen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Yonyx Interactive Guides Single Sign-On](#configure-yonyx-interactive-guides-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Yonyx Interactive Guides test gebruiker](#create-yonyx-interactive-guides-test-user)** - om een tegenhanger van Britta Simon in Yonyx Interactive Guides te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de interne aanmelding van Azure AD te configureren met Yonyx-interactieve hulplijnen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina De integratie van de toepassing **Yonyx Interactive Guides** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Yonyx Interactive Guides Domain and URLLs single sign-on information Yonyx Interactive Guides Domain and URLLs single sign-on information Yonyx Interactive Guides Domain and URLLs single sign-on information Yon](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<company name>.yonyx.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [Yonyx Interactive Guides Client support team](mailto:support@yonyx.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Yonyx-interactieve hulplijnen instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-yonyx-interactive-guides-single-sign-on"></a>Yonyx Interactive Guides Single Sign-On configureren

Als u eenmalige aanmelding wilt configureren aan de kant van **de Interactieve Gidsen van Yonyx,** moet u het gedownloade certificaat **(Base64)** en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam voor interactieve gidsen van Yonyx](mailto:support@yonyx.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Yonyx Interactive Guides.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer vervolgens **Interactieve Hulplijnen van Yonyx**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **yonyx-interactieve hulplijnen**in de lijst met toepassingen .

    ![De koppeling Interactieve Gidsen van Yonyx in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-yonyx-interactive-guides-test-user"></a>Testgebruiker voor Yonyx-hulplijnen maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Yonyx Interactive Guides. Yonyx Interactive Guides ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Yonyx Interactive Guides, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, moet u contact opnemen met het [ondersteuningsteam voor interactieve gidsen van Yonyx.](mailto:support@yonyx.com)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Interactieve Gidsen van Yonyx klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de Interactieve Gidsen van Yonyx waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

