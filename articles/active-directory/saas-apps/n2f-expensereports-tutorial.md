---
title: 'Zelfstudie: Azure Active Directory-integratie met N2F - Onkostenrapporten | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en N2F - Onkostenrapporten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161285"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Zelfstudie: Azure Active Directory-integratie met N2F - Onkostenrapporten

In deze zelfstudie leert u hoe u N2F - Onkostenrapporten integreert met Azure Active Directory (Azure AD).
Het integreren van N2F - Onkostenrapporten met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot N2F - Onkostenrapporten.
* U uw gebruikers automatisch laten inloggen op N2F - Onkostenrapporten (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met N2F - Onkostenrapporten, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* N2F - Onkostenrapporteert eenmalig abonnement met aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* N2F - Onkostenrapporten ondersteunen **SP** en **IDP** geïnitieerd sso

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>N2F toevoegen - Onkostenrapporten uit de galerie

Als u de integratie van N2F - Onkostenrapporten in Azure AD wilt configureren, moet u N2F - Onkostenrapporten uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om N2F - Onkostenrapporten uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ In het zoekvak **N2F - Onkostenrapporten**, selecteer **N2F - Onkostenrapporten** van het resultaatpaneel en klik vervolgens op **Knop Toevoegen** om de toepassing toe te voegen.

     ![N2F - Onkostenrapporten in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met N2F - Onkostenrapporten op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in N2F - Onkostenrapporten.

Als u Azure AD-melding met N2F - Onkostenrapporten wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[N2F - Onkostenrapporten Enkelvoudteken -](#configure-n2f---expense-reports-single-sign-on)** configureren om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[N2F - Onkostenrapporten test gebruiker](#create-n2f---expense-reports-test-user)** - om een tegenhanger van Britta Simon in N2F - Onkostenrapporten die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de volgende stappen uit te voeren voor het configureren van Azure AD-eenmaligaanmelding met N2F - Onkostenrapporten:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **N2F - Onkostenrapporten** de integratie pagina met toepassingen voor **één melding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de **iDP-modus** wilt configureren, hoeft de gebruiker in de sectie **BasisSAML-configuratie** geen stappen uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![N2F - Onkostenrapporten Domein- en URL's-informatie met eenmalige aanmelding](common/preintegrated.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![N2F - Onkostenrapporten Domein- en URL's-informatie met eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://www.n2f.com/app/`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

7. Kopieer in de sectie **Mijnbeleid instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F configureren - Onkostenrapporten Eenmalig aanmelden

1. Meld u in een ander browservenster aan bij uw N2F - Onkostenrapporten bedrijfssite als beheerder.

2. Klik op **Instellingen** en selecteer **Instellingen vooraf** in de vervolgkeuzelijst.

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecteer het tabblad **Accountinstellingen.**

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/configure2.png)

4. Selecteer **Verificatie** en selecteer + Het tabblad **Een verificatiemethode toevoegen.**

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecteer **SAML Microsoft Office 365** als verificatiemethode.

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/configure4.png)

6. Voer in de sectie **Verificatiemethode** de volgende stappen uit:

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/configure5.png)

    a. Plak in het tekstvak **Entiteit-id** de azure **AD-id-waarde** die u hebt gekopieerd van de Azure-portal.

    b. Plak in het tekstvak **metagegevens URL** de **url-waarde van de appfederatie- metagegevens,** die u hebt gekopieerd van de Azure-portal.

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
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot N2F - Onkostenrapporten.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Vervolgens N2F - Onkostenrapporten**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **N2F - Onkostenrapporten**selecteert u in de lijst met toepassingen .

    ![De koppeling N2F - Onkostenrapporten in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-n2f---expense-reports-test-user"></a>N2F maken - Onkostenrapporten testgebruiker

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij N2F - Onkostenrapporten, moeten ze worden ingericht in N2F - Onkostenrapporten. In het geval van N2F - Onkostenrapporten is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw N2F - Onkostenrapporten bedrijfssite als beheerder.

2. Klik op **Instellingen** en selecteer **Instellingen vooraf** in de vervolgkeuzelijst.

    ![N2F - Onkosten Toevoegen gebruiker](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecteer het tabblad **Gebruikers** in het linkernavigatiedeelvenster.

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/user1.png)

4. Selecteer **+ Nieuw gebruikerstabblad.**

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/user2.png)

5. Voer **in** de sectie Gebruiker de volgende stappen uit:

    ![N2F - Configuratie van onkostenrapporten](./media/n2f-expensereports-tutorial/user3.png)

    a. Voer in het tekstvak **E-mailadres** het e-mailadres in van de gebruiker, zoals **\@brittasimon contoso.com**.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta.**

    c. Voer in het tekstvak **Volledige naam** de volledige naam van de gebruiker in, bijvoorbeeld **Britta Simon**.

    d. Kies **Rol, Direct manager (N+1)** en **Division** volgens uw organisatievereiste.

    e. Klik **op Uitnodiging valideren en verzenden**.

    > [!NOTE]
    > Als u problemen ondervindt bij het toevoegen van de gebruiker, neem dan contact op met [het ondersteuningsteam voor onkostenrapporten](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel N2F - Onkostenrapporten in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Onkostenrapporten N2F - Waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

