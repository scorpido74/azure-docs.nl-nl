---
title: 'Zelfstudie: Azure Active Directory-integratie met TeamSeer | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3e505e4823db8c9f42bbff216f30326081110dcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088970"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Zelfstudie: Azure Active Directory-integratie met TeamSeer

In deze zelfstudie leert u hoe u TeamSeer integreert met Azure Active Directory (Azure AD).
De integratie van TeamSeer met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot TeamSeer.
* U uw gebruikers automatisch laten aanmelden bij TeamSeer (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met TeamSeer wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* TeamSeer single sign-on enabled subscription TeamSeer single sign-on enabled subscription TeamSeer single sign-on enabled subscription TeamS

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TeamSeer steunt **door SP** geïnitieerde SSO

## <a name="adding-teamseer-from-the-gallery"></a>TeamSeer toevoegen vanuit de galerie

Als u de integratie van TeamSeer in Azure AD wilt configureren, moet u TeamSeer vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om TeamSeer uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **TeamSeer**in het zoekvak , selecteer **TeamSeer** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![TeamSeer in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met TeamSeer op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TeamSeer.

Als u Azure AD-singlesign-aan met TeamSeer wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer TeamSeer Single Sign-On](#configure-teamseer-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak teamseer-testgebruiker](#create-teamseer-test-user)** - om een tegenhanger van Britta Simon in TeamSeer te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met TeamSeer te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **TeamSeer-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![TeamSeer Domain and URLLs single sign-on information TeamSeer Domain and URLLs single sign-on information TeamSeer Domain and URLLs single sign-on information TeamS](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [teamseer client support team](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **TeamSeer instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-teamseer-single-sign-on"></a>TeamSeer-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw TeamSeer-bedrijfssite als beheerder.

1. Ga naar **HR-beheerder**.

    ![HR-beheerder](./media/teamseer-tutorial/ic789634.png "HR-beheerder")

1. Klik **op Instellen**.

    ![Installatie](./media/teamseer-tutorial/ic789635.png "Instellen")

1. Klik **op SAML-providergegevens instellen**.

    ![SAML-instellingen](./media/teamseer-tutorial/ic789636.png "SAML-instellingen")

1. Voer in de sectie SAML-providerdetails de volgende stappen uit:

    ![SAML-instellingen](./media/teamseer-tutorial/ic789637.png "SAML-instellingen")

    a. Plak in het **tekstvak URL** de **URL-waarde aanmelding** die u hebt gekopieerd uit de Azure-portal.

    b. Open uw basis-64 gecodeerd certificaat in kladblok, kopieer de inhoud ervan naar uw klembord en plak het vervolgens op het tekstvak **idp Public Certificate.**

1. Voer de volgende stappen uit om de SAML-providerconfiguratie te voltooien:

    ![SAML-instellingen](./media/teamseer-tutorial/ic789638.png "SAML-instellingen")

    a. Typ in de **e-mailadressen van**de test de testgebruiker.
  
    b. Typ **in** het tekstvak Uitgever de URL van de uitgever van de serviceprovider.
  
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
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot TeamSeer.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens TeamSeer**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TeamSeer**in de lijst met toepassingen .

    ![De koppeling TeamSeer in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-teamseer-test-user"></a>TeamSeer-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij TeamSeer, moeten ze zijn ingericht in ShiftPlanning. In het geval van TeamSeer is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **TeamSeer-bedrijfssite** als beheerder.

1. Ga naar **gebruikers van \> HR-beheerders** en klik op **De wizard Nieuwe gebruiker uitvoeren**.

    ![HR-beheerder](./media/teamseer-tutorial/ic789640.png "HR-beheerder")

1. Voer in de sectie **Gebruikersgegevens** de volgende stappen uit:

    ![Gebruikersgegevens](./media/teamseer-tutorial/ic789641.png "Gebruikersgegevens")

    a. Typ de **voornaam**, **achternaam**, **gebruikersnaam (e-mailadres)** van een geldig Azure AD-account dat u wilt inrichten in de gerelateerde tekstvakken.
  
    b. Klik op **Volgende**.

1. Volg de instructies op het scherm voor het toevoegen van een nieuwe gebruiker en klik op **Voltooien**.

> [!NOTE]
> U alle andere hulpprogramma's voor het maken van gebruikersaccounts van TeamSeer gebruiken die door TeamSeer worden verstrekt om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TeamSeer in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de TeamSeer waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
