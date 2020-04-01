---
title: 'Zelfstudie: Azure Active Directory-integratie met Yodeck | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: af0748f36e8d2299921f987c517f7a4923bd0d55
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086480"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Zelfstudie: Azure Active Directory-integratie met Yodeck

In deze zelfstudie leert u hoe u Yodeck integreert met Azure Active Directory (Azure AD).
De integratie van Yodeck met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Yodeck.
* U uw gebruikers automatisch laten aanmelden bij Yodeck (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Yodeck wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Abonnement met eenmalig aanmelden voor Yodeck

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Yodeck ondersteunt **SP** en **IDP** geïnitieerd sso

## <a name="adding-yodeck-from-the-gallery"></a>Yodeck toevoegen vanuit de galerie

Als u de integratie van Yodeck in Azure AD wilt configureren, moet u Yodeck uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Yodeck uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Yodeck**in het zoekvak , selecteer **Yodeck** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Yodeck in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Yodeck op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Yodeck.

Als u Azure AD Single Sign-on met Yodeck wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Yodeck Single Sign-On](#configure-yodeck-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Yodeck-testgebruiker maken](#create-yodeck-test-user)** - om een tegenhanger van Britta Simon in Yodeck te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Yodeck te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Integratie van Yodeck-toepassingen** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stap uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Informatie over Yodeck-domeinen en URL's met eenmalige aanmelding](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![installatiekopie](common/both-preintegrated-signon.png)

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.yodeck.com/login`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Yodeck-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw Yodeck-bedrijfssite als beheerder.

2. Klik op de optie **Gebruikersinstellingen** in de rechterbovenhoek van de pagina en selecteer **Accountinstellingen**.

    ![Yodeck-configuratie](./media/yodeck-tutorial/configure1.png)

3. Selecteer **SAML** en voer de volgende stappen uit:

    ![Yodeck-configuratie](./media/yodeck-tutorial/configure2.png)

    a. Selecteer **Importeren uit URL**.

    b. Plak in het **tekstvak URL URL** van de URL van de app-federatie de url-waarde van de **app-federatie,** die u hebt gekopieerd uit de Azure-portal en klik op **Importeren**.
    
    c. Na het importeren van **de url van de appfederatie- metagegevens**worden de resterende velden automatisch ingevuld.

    d. Klik op **Opslaan**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Yodeck.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Yodeck**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Yodeck**in de lijst met toepassingen .

    ![De Yodeck-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-yodeck-test-user"></a>Yodeck-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Yodeck, moeten ze worden ingericht in Yodeck. In het geval van Yodeck is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw Yodeck-bedrijfssite als beheerder.

2. Klik op de optie **Gebruikersinstellingen** in de rechterbovenhoek van de pagina en selecteer **Gebruikers**.

    ![Werknemer toevoegen](./media/yodeck-tutorial/user1.png)

3. Klik op **+Gebruiker** om het tabblad Gebruikersgegevens te **openen.**

    ![Werknemer toevoegen](./media/yodeck-tutorial/user2.png)

4. Voer in het dialoogvenster **User Details** de volgende stappen uit:

    ![Werknemer toevoegen](./media/yodeck-tutorial/user3.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker zoals **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker zoals **Simon**.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld brittasimon@contoso.com.

    d. Selecteer de juiste optie **Accountmachtigingen** volgens uw organisatorische vereisten.
    
    e. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Yodeck-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Yodeck waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

