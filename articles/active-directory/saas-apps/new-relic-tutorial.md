---
title: 'Zelfstudie: Azure Active Directory-integratie met nieuwe relieken | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233523"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Zelfstudie: Azure Active Directory-integratie met nieuwe relieken

In deze zelfstudie leert u hoe u Nieuwe relieken integreren met Azure Active Directory (Azure AD).
De integratie van New Relic met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot New Relic.
* U uw gebruikers automatisch laten inloggen op New Relic (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met New Relic wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Nieuw Relic-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* New Relic ondersteunt **SP** geïnitieerde SSO

## <a name="adding-new-relic-from-the-gallery"></a>Nieuwe relic uit de galerij toevoegen

Als u de integratie van New Relic in Azure AD wilt configureren, moet u New Relic uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Nieuwe relikwie uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ In het zoekvak **Nieuw relic**, selecteer **Nieuwe relikwie** uit het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![Nieuwe Relic in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met New Relic op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in New Relic.

Als u Azure AD-singlesign-aan wilt configureren en testen met Nieuwe Relieken, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Nieuwe Relic Single Sign-On](#configure-new-relic-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak nieuwe Relic-testgebruiker](#create-new-relic-test-user)** - om een tegenhanger van Britta Simon te hebben in New Relic die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om azure AD-eenmaligaanmelding met Nieuwe relieken te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Nieuwe Rede-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Nieuwe Relic-domein en URL's meteenaanmeldingsinformatie](common/sp-identifier.png)

    a. Typ in het tekstvak **Aanmelding op URL** `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` een URL met het volgende patroon: - Zorg ervoor dat u uw eigen nieuwe reliekenaccount-id vervangt.

    b. Typ een URL in het vak **Id (Entiteits-id)**: `rpm.newrelic.com`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Nieuwe relikwie instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-new-relic-single-sign-on"></a>Nieuwe relieken aanmelding configureren

1. Meld u in een ander browservenster aan op uw **nieuwe locatie van het bedrijf New Relic** als beheerder.

2. Klik in het menu bovenaan op **Accountinstellingen**.
   
    ![Accountinstellingen](./media/new-relic-tutorial/ic797036.png "Accountinstellingen")

3. Klik op het tabblad **Beveiliging en verificatie** en klik vervolgens op het tabblad Eén **teken.**
   
    ![Eén aanmelding](./media/new-relic-tutorial/ic797037.png "Eenmalige aanmelding")

4. Voer op de pagina SAML de volgende stappen uit:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klik **op Bestand kiezen** om het gedownloade Azure Active Directory-certificaat te uploaden.

    b. Plak in het tekstvak **URL voor extern inloggen** de waarde van de **aanmeldings-URL**, die u hebt gekopieerd van azure-portal.
   
    c. Plak in het tekstvak **VOOR de landing-URL** van afmelden de waarde van **de URL van Afmelden**, die u hebt gekopieerd van azure-portal.

    d. Klik **op Mijn wijzigingen opslaan**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot New Relic.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer Vervolgens **Nieuwe relikwie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Nieuwe relikwie**in de lijst met toepassingen .

    ![De nieuwe relic-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-new-relic-test-user"></a>Nieuwe Relic-testgebruiker maken

Om Azure Active Directory-gebruikers in staat te stellen zich aan te melden bij New Relic, moeten ze worden ingericht in New Relic. In het geval van New Relic is inrichten een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten op New Relic:**

1. Log in op uw **New Relic-bedrijfssite** als beheerder.

2. Klik in het menu bovenaan op **Accountinstellingen**.
   
    ![Accountinstellingen](./media/new-relic-tutorial/ic797040.png "Accountinstellingen")

3. Klik **in** het deelvenster Account aan de linkerkant op **Overzicht**en klik vervolgens op **Gebruiker toevoegen**.
   
    ![Accountinstellingen](./media/new-relic-tutorial/ic797041.png "Accountinstellingen")

4. Voer in het dialoogvenster **Actieve gebruikers** de volgende stappen uit:
   
    ![Actieve gebruikers](./media/new-relic-tutorial/ic797042.png "Actieve gebruikers")
   
    a. Typ in het tekstvak **E-mail** het e-mailadres van een geldige Azure Active Directory-gebruiker die u wilt inrichten.

    b. Selecteer **als rol** **Gebruiker**.

    c. Klik **op Deze gebruiker toevoegen**.

>[!NOTE]
>U alle andere hulpprogramma's voor het maken van nieuwe Reidsaccounts voor gebruikersaccounts of API's van New Relic gebruiken om Azure AD-gebruikersaccounts in te richten.
> 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Nieuwe relikwie in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Nieuwe Relikwie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

