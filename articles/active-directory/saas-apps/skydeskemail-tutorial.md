---
title: 'Zelfstudie: Azure Active Directory-integratie met SkyDesk-e-mail | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SkyDesk-e-mail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090428"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Zelfstudie: Azure Active Directory-integratie met SkyDesk-e-mail

In deze zelfstudie leert u hoe u SkyDesk-e-mail integreert met Azure Active Directory (Azure AD).
De integratie van SkyDesk Email met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot SkyDesk Email.
* U uw gebruikers automatisch laten aanmelden bij SkyDesk Email (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met SkyDesk-e-mail, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* SkyDesk E-mail eenmalige aanmelding ingeschakeld abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SkyDesk Email ondersteunt **SP** geïnitieerde SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>SkyDesk-e-mail toevoegen vanuit de galerie

Als u de integratie van SkyDesk-e-mail in Azure AD wilt configureren, moet u SkyDesk-e-mail vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SkyDesk-e-mail vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SkyDesk Email**in het zoekvak , selecteer **SkyDesk-e-mail** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![SkyDesk-e-mail in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SkyDesk Email op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SkyDesk-e-mail.

Als u Azure AD-singlesign-aan wilt configureren en testen met SkyDesk Email, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SkyDesk E-mail Single Sign-On](#configure-skydesk-email-single-sign-on)** configureren - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[SkyDesk E-mailtestgebruiker maken](#create-skydesk-email-test-user)** - om een tegenhanger van Britta Simon in SkyDesk-e-mail te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de interne aanmelding van Azure AD met SkyDesk-e-mail te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de integratiepagina van de **SkyDesk E-mailtoepassing** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![SkyDesk-e-maildomein en URL's met eenmalige aanmelding](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het ondersteuningsteam voor e-mailclient](https://www.skydesk.jp/apps/support/) van SkyDesk om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **SkyDesk-e-mail instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-skydesk-email-single-sign-on"></a>SkyDesk-e-mail eenmalige aanmelding configureren

1. Meld je in een andere webbrowser aan bij je SkyDesk E-mailaccount als beheerder.

1. Klik in het menu bovenaan op **Instellen**en selecteer **Org**.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klik op **Domeinen** in het linkerdeelvenster.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klik op **Domein toevoegen**.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Voer uw domeinnaam in en verifieer het domein.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klik op **SAML-verificatie** vanuit het linkerdeelvenster.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Voer op de pagina **SAML-verificatie** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Als u SAML-verificatie wilt gebruiken, moet u de **url-instelling voor domeinen** of **portalen** hebben geverifieerd. U de URL van de portal instellen met de unieke naam.

    ![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.

    b. Plak in het tekstvak Van **de URL van afmelden** de waarde van **de URL van Afmelden**, die u hebt gekopieerd van azure-portal.

    c. **Url van wachtwoord wijzigen** is optioneel, dus laat het leeg.

    d. Klik op **Sleutel ophalen uit bestand** om het gedownloade certificaat uit azure-portal te selecteren en klik vervolgens op **Openen** om het certificaat te uploaden.

    e. Selecteer **RSA** als **algoritme**.

    f. Klik **op Ok** om de wijzigingen op te slaan.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SkyDesk Email.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens SkyDesk-e-mail**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SkyDesk-e-mail**in de lijst met toepassingen .

    ![De skydesk-e-mailkoppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-skydesk-email-test-user"></a>SkyDesk-e-mailtestgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in SkyDesk Email.

Klik op **Gebruikerstoegang** vanuit het linkerdeelvenster in SkyDesk Email en voer vervolgens uw gebruikersnaam in.

![Eenmalige aanmelding configureren](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Als u bulkgebruikers wilt maken, moet u contact opnemen met het [ondersteuningsteam voor e-mailclient van SkyDesk.](https://www.skydesk.jp/apps/support/)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de skydesk-e-mailtegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SkyDesk-e-mail waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

