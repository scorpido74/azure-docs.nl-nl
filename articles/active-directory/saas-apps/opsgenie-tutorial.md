---
title: 'Zelfstudie: Azure Active Directory-integratie met OpsGenie | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095755"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Zelfstudie: Azure Active Directory-integratie met OpsGenie

In deze zelfstudie leert u hoe u OpsGenie integreert met Azure Active Directory (Azure AD).
De integratie van OpsGenie met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot OpsGenie.
* U uw gebruikers automatisch laten aanmelden bij OpsGenie (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met OpsGenie wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* OpsGenie single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* OpsGenie ondersteunt **SP** geïnitieerde SSO

## <a name="adding-opsgenie-from-the-gallery"></a>OpsGenie toevoegen vanuit de galerie

Als u de integratie van OpsGenie in Azure AD wilt configureren, moet u OpsGenie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u OpsGenie vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **OpsGenie**in het zoekvak en selecteer **OpsGenie** in het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![OpsGenie in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met OpsGenie op basis van een testgebruiker genaamd **B. Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in OpsGenie.

Als u Azure AD-singlesign-aan met OpsGenie wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer OpsGenie Single Sign-On](#configure-opsgenie-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkelvoudige aanmelding met B. Simon te testen.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak OpsGenie-testgebruiker](#create-opsgenie-test-user)** - om een tegenhanger van B. Simon in OpsGenie te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met OpsGenie te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **OpsGenie-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![OpsGenie Domain en URL's single sign-on informatie](common/sp-signonurl.png)

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.opsgenie.com/auth/login`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

6. Kopieer in de sectie **OpsGenie instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-opsgenie-single-sign-on"></a>OpsGenie-aanmelding configureren

1. Open een andere browserinstantie en log in bij OpsGenie als beheerder.

2. Klik **op Instellingen**en klik vervolgens op het tabblad Eén **aanmelding.**
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Als u SSO wilt inschakelen, selecteert u **Ingeschakeld**.
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Klik **in** de sectie Provider op het tabblad **Azure Active Directory.**
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Voer op de dialoogvensters Azure Active Directory de volgende stappen uit:
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Plak in het **tekstvak SAML 2.0-eindpunt** **de URL-waarde van aanmelding**die u hebt gekopieerd uit de Azure-portal.
    
    b. Plak in het **tekstvak Metagegevens:** textbox de **url-waarde van app-federatie-metagegevens** die u hebt gekopieerd uit de Azure-portal.
    
    c. Klik **op Wijzigingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is het maken van een testgebruiker in de Azure-portal genaamd B. Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer **in** het veld Naam **B. Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **bsimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B. Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot OpsGenie.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **OpsGenie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **OpsGenie**in de lijst met toepassingen .

    ![De koppeling OpsGenie in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **B. Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-opsgenie-test-user"></a>Testgebruiker OpsGenie maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B. Simon in OpsGenie. 

1. Log in een webbrowservenster in bij uw OpsGenie-tenant als beheerder.

2. Navigeer naar de lijst Met gebruikers door te klikken op **Gebruikers** in het linkerdeelvenster.
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klik **op Gebruiker toevoegen**.

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Typ in het tekstvak **E-mail** het e-mailadres van B. Simon dat is geadresseerd in Azure Active Directory.
   
    b. Typ **B. Simon**in het tekstvak **Volledige naam** .
   
    c. Klik op **Opslaan**. 

>[!NOTE]
>B. Simon krijgt een e-mail met instructies voor het instellen van hun profiel.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel OpsGenie in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de OpsGenie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

