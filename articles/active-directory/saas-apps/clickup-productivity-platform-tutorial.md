---
title: 'Zelfstudie: Azure Active Directory-integratie met ClickUp Productivity Platform | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ClickUp Productivity Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: f497955b698d7ca390b40686e94d553a7cd5c948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048759"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Zelfstudie: Azure Active Directory-integratie met ClickUp Productivity Platform

In deze zelfstudie leert u hoe u ClickUp Productivity Platform integreert met Azure Active Directory (Azure AD).
De integratie van ClickUp Productivity Platform met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot ClickUp Productivity Platform.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ClickUp Productivity Platform (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met ClickUp Productivity Platform hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op ClickUp Productivity Platform waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ClickUp Productivity Platform ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>ClickUp Productivity Platform toevoegen vanuit de galerie

Om de integratie van ClickUp Productivity Platform te configureren in Azure AD, moet u ClickUp Productivity Platform vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om ClickUp Productivity Platform toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **ClickUp Productivity Platform** in het zoekvak, selecteer **ClickUp Productivity Platform** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![ClickUp Productivity Platform in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure Active Directory met ClickUp Productivity Platform op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in ClickUp Productivity Platform tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met ClickUp Productivity Platform, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor ClickUp Productivity Platform configureren](#configure-clickup-productivity-platform-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor ClickUp Productivity Platform maken](#create-clickup-productivity-platform-test-user)**: als u een tegenhanger van Britta Simon in ClickUp Productivity Platform wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD bij ClickUp Productivity Platform te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie voor **ClickUp Productivity Platform** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij ClickUp Productivity Platform](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.clickup.com/login/sso`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > De id-waarde is niet echt. Vervang deze waarde door de werkelijke id op de manier die verderop in deze zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Eenmalige aanmelding bij ClickUp Productivity Platform configureren

1. Meld u in een ander browservenster als beheerder aan bij de ClickUp Productivity Platform-tenant.

2. Klik op het **gebruikersprofiel**en selecteer **Instellingen**.

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Selecteer **Microsoft** onder Single Sign-On (SSO) Provider (Provider voor eenmalige aanmelding).

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Op de pagina **Configure Microsoft Single Sign On** (Eenmalige aanmelding via Microsoft configureren) voert u de volgende stappen uit:

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Klik op **Kopiëren** om de waarde van de entiteits-ID te kopiëren en plak deze in het tekstvak voor de **id (entiteits-ID)** in het gedeelte **Standaard SAML-configuratie** in de Azure-portal.
    
    b. Plak in het tekstvak **Azure Federation Metadata URL** (URL voor federatieve metagegevens voor Azure) de waarde voor de app-URL voor federatieve metagegevens die u hebt gekopieerd uit de Azure-portal en klik op **Opslaan**.

5. U voltooit de installatie door op **Authenticate With Microsoft to complete setup** (Verifiëren met Microsoft om de installatie te voltooien) te klikken en te verifiëren met een Microsoft-account.

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure4.png)

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot ClickUp Productivity Platform.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en vervolgens **ClickUp Productivity Platform**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ClickUp Productivity Platform** in de lijst met toepassingen.

    ![De link ClickUp Productivity Platform in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-clickup-productivity-platform-test-user"></a>Testgebruiker voor ClickUp Productivity Platform maken

1. Meld u in een ander browservenster als beheerder aan bij de ClickUp Productivity Platform-tenant.

2. Klik op het **gebruikersprofiel**en selecteer **Personen**.
   
    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/user1.png)

3. Voer het e-mailadres van de gebruiker in het tekstvak in en klik op **Uitnodigen**.

    ![Configuratie van ClickUp Productivity](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > De gebruiker ontvangt de melding en moet de uitnodiging accepteren om het account te activeren.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ClickUp Productivity Platform klikt in het Toegangsvenster, wordt u automatisch aangemeld bij de instantie van ClickUp Productivity Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

