---
title: 'Zelfstudie: Azure Active Directory-integratie met ScreenSteps | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091654"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Zelfstudie: Azure Active Directory-integratie met ScreenSteps

In deze zelfstudie leert u hoe u ScreenSteps integreert met Azure Active Directory (Azure AD).
Het integreren van ScreenSteps met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot ScreenSteps.
* U uw gebruikers automatisch laten inlogen op ScreenSteps (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met ScreenSteps, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met één aanmelding met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ScreenSteps ondersteunt **SP** geïnitieerde SSO

## <a name="adding-screensteps-from-the-gallery"></a>Schermstappen toevoegen vanuit de galerie

Als u de integratie van ScreenSteps in Azure AD wilt configureren, moet u ScreenSteps uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u ScreenSteps uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer In het zoekvak **ScreenSteps**, selecteer **ScreenSteps** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Schermstappen in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met ScreenSteps op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ScreenSteps.

Als u Azure AD-single sign-aan wilt configureren en testen met ScreenSteps, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Schermstappen eenmalig aanmelden configureren](#configure-screensteps-single-sign-on)** - om de instellingen voor eenmalig aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[ScreenSteps-testgebruiker maken](#create-screensteps-test-user)** - om een tegenhanger van Britta Simon in ScreenSteps te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met ScreenSteps te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Schermstappen-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Schermstappen Domein- en URL's met eenmalige aanmeldingsgegevens](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL, die later in deze zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **ScreenSteps instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-screensteps-single-sign-on"></a>Schermstappen eenmalig aanmelden configureren

1. Log in een ander browservenster in op uw bedrijf Site ScreenSteps als beheerder.

1. Klik op **Accountinstellingen**.

    ![Accountbeheer](./media/screensteps-tutorial/ic778523.png "Accountbeheer")

1. Klik **op Eén aanmelding**.

    ![Verificatie op afstand](./media/screensteps-tutorial/ic778524.png "Verificatie op afstand")

1. Klik **op Eén aanmeldingseindpunt maken**.

    ![Verificatie op afstand](./media/screensteps-tutorial/ic778525.png "Verificatie op afstand")

1. Voer in de sectie **Eén aanmeldingspunt** maken de volgende stappen uit:

    ![Een eindpunt voor verificatie maken](./media/screensteps-tutorial/ic778526.png "Een eindpunt voor verificatie maken")

    a. Typ een titel in het tekstvak **Titel.**

    b. Selecteer **SAML**in de lijst **Modus** .

    c. Klik **op Maken**.

1. **Het** nieuwe eindpunt bewerken.

    ![Eindpunt bewerken](./media/screensteps-tutorial/ic778528.png "Eindpunt bewerken")

1. Voer in de sectie **Eén aanmeldingspunt** bewerken de volgende stappen uit:

    ![Eindpunt voor externe verificatie](./media/screensteps-tutorial/ic778527.png "Eindpunt voor externe verificatie")

    a. Klik **op Nieuw SAML-certificaatbestand uploaden**en upload vervolgens het certificaat, dat u hebt gedownload van de Azure-portal.

    b. De **URL-waarde van aanmelding plakken,** die u hebt gekopieerd van de Azure-portal naar het tekstvak voor extern **inlog-URL.Paste** Login URL value, which you have copied from the Azure portal into the Remote Login URL textbox.

    c. De **URL-waarde van afmelden** plakken, die u vanuit de Azure-portal hebt gekopieerd naar het **tekstvak VOOR uitloggen URL.**

    d. Selecteer een **groep** waaraan gebruikers moeten worden toegewezen wanneer ze zijn ingericht.

    e. Klik op **Update**.

    f. Kopieer de **URL van de SAML-consument** naar het klembord en plak in het tekstvak **VOOR aanmeldings-URL** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    g. Ga terug naar het **eindpunt voor eenmalig aanmelding bewerken**.

    h. Klik **op** de knop Standaard maken voor account om dit eindpunt te gebruiken voor alle gebruikers die zich aanmelden bij ScreenSteps. U ook op de knop **Toevoegen aan site** klikken om dit eindpunt te gebruiken voor specifieke sites in **ScreenSteps**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ScreenSteps.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens ScreenSteps**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ScreenSteps**in de lijst met toepassingen .

    ![De koppeling Schermstappen in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-screensteps-test-user"></a>Testgebruiker ScreenSteps maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in ScreenSteps. Werk samen met [het ondersteuningsteam](https://www.screensteps.com/contact) van ScreenSteps Client om de gebruikers toe te voegen in het ScreenSteps-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ScreenSteps in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de ScreenSteps waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)