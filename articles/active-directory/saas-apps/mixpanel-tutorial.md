---
title: 'Zelfstudie: Azure Active Directory-integratie met Mixpanel | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: 58074d02dfc437a1804784e73fa4e65086b53b9e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160467"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Zelfstudie: Azure Active Directory-integratie met Mixpanel

In deze zelfstudie leert u hoe u Mixpanel integreert met Azure Active Directory (Azure AD).
Het integreren van Mixpanel met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Mixpanel.
* U uw gebruikers automatisch laten inloggen op Mixpanel (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Mixpanel, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met één aanmelding voor Mixpanel

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mixpanel ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-mixpanel-from-the-gallery"></a>Mixpanel toevoegen vanuit de galerie

Als u de integratie van Mixpanel in Azure AD wilt configureren, moet u Mixpanel uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Mixpanel vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **mixpanel**in het zoekvak , selecteer in het resultaatpaneel **mixpaneel** en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Mixpanel in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Mixpanel op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Mixpanel.

Als u Azure AD-single sign-aan wilt configureren en testen met Mixpanel, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Stel enkele aanmelding voor mixpanel in](#configure-mixpanel-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Mixpanel-testgebruiker maken](#create-mixpanel-test-user)** - om een tegenhanger van Britta Simon te hebben in Mixpanel die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren als u de enkele aanmelding van Azure AD met Mixpanel wilt configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Integratie van de toepassing Mixpanel** de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over mixpanel-domein en URL's met eenmalige aanmelding](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://mixpanel.com/login/`

    > [!NOTE]
    > Meld u [https://mixpanel.com/register/](https://mixpanel.com/register/) aan om uw inloggegevens in te stellen en neem contact op met het [ondersteuningsteam van Mixpanel](mailto:support@mixpanel.com) om SSO-instellingen voor uw tenant in te schakelen. Je de URL-waarde van je aanmelding seinen ook krijgen van je ondersteuningsteam van Mixpanel. 

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Mixpanel instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-mixpanel-single-sign-on"></a>Eén aanmelding voor mixpanel configureren

1. Meld je in een ander browservenster aan bij je Mixpanel-toepassing als beheerder.

2. Klik onder aan de pagina op het **tandwielpictogram** je in de linkerhoek. 
   
    ![Eén aanmelding voor mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Klik op het **tabblad Beveiliging van Toegang** en klik vervolgens op Instellingen **wijzigen**.
   
    ![Instellingen voor mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Klik **op** de pagina Uw certificaat wijzigen op **Bestand kiezen** om het gedownloade certificaat te uploaden en klik vervolgens op **VOLGENDE**.
   
    ![Instellingen voor mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Plak in het tekstvak voor verificatie-URL op de pagina **URL van uw verificatie** de waarde van de **aanmeldings-URL** die u hebt gekopieerd vanuit azure-portal en klik op **VOLGENDE**.
   
    ![Instellingen voor mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Klik op **Gereed**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Mixpanel.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens Mixpanel**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Mixpanel**in de lijst met toepassingen .

    ![De koppeling Mixpanel in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mixpanel-test-user"></a>Gebruikers van mixpanel-test maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in Mixpanel. 

1. Meld u aan op uw site van het Bedrijf Mixpanel als beheerder.

2. Klik onder aan de pagina op de kleine versnellingsknop in de linkerhoek om het **venster Instellingen** te openen.

3. Klik op het tabblad **Team.**

4. Typ in het tekstvak **van het teamlid** het e-mailadres van Britta in Azure.
   
    ![Instellingen voor mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Klik op **Uitnodigen**. 

> [!Note]
> De gebruiker ontvangt een e-mail om het profiel in te stellen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Mixpanel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Mixpanel waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

