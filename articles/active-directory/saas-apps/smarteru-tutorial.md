---
title: 'Zelfstudie: Azure Active Directory-integratie met SmarterU | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 712e7bcf513592f97950902faff2f7754093b9fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76549288"
---
> [!NOTE]
> Het proces voor de integratie van SmarterU met Azure Active Directory wordt ook gedocumenteerd en onderhouden in het [SmarterU-helpsysteem.](https://help.smarteru.com/ID2053086)

# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Zelfstudie: Azure Active Directory-integratie met SmarterU

In deze zelfstudie leert u hoe u SmarterU integreren met Azure Active Directory (Azure AD).
De integratie van SmarterU met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot SmarterU.
* U uw gebruikers automatisch laten aanmelden bij SmarterU (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SmarterU wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* SmarterU-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SmarterU ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-smarteru-from-the-gallery"></a>SmarterU toevoegen vanuit de galerie

Als u de integratie van SmarterU in Azure AD wilt configureren, moet u SmarterU vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SmarterU vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u boven aan het dialoogvenster op De knop **Nieuwe toepassing.**

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SmarterU**in het zoekvak , selecteer **SmarterU** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![SmarterU in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SmarterU op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SmarterU.

Als u Azure AD-singlesign-aan met SmarterU wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SmarterU Single Sign-On configureren](#configure-smarteru-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[SmarterU-testgebruiker maken](#create-smarteru-test-user)** - om een tegenhanger van Britta Simon in SmarterU te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om azure AD-eenmaligaanmelding met SmarterU te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **SmarterU-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![SmarterU-domein- en URL's met eenmalige aanmelding](common/idp-identifier.png)

    Typ de volgende URL in het tekstvak **Id**: `https://www.smarteru.com/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **SmarterU instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-smarteru-single-sign-on"></a>Smarteru-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw SmarterU-bedrijfssite als beheerder.

1. Klik op de werkbalk bovenaan op **Accountinstellingen**.

    ![Accountinstellingen](./media/smarteru-tutorial/accountsettings.png)

1. Voer op de configuratiepagina voor accounts de volgende stappen uit:

    ![Externe autorisatie](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Selecteer **Externe autorisatie inschakelen**.
  
    b. Selecteer in de sectie **Beheer master-aanmelding** het tabblad **SmarterU.**
  
    c. Selecteer in de sectie **Standaardinloggen gebruiker** het tabblad **SlimmerU.**
  
    d. Selecteer **SAML inschakelen**.
  
    e. Kopieer de inhoud van het gedownloade metagegevensbestand en plak deze vervolgens in het tekstvak **idp-metagegevens.**

    f. Selecteer een **kenmerk/claim voor een id**.
  
    g. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SmarterU.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **SlimmerU**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SmarterU**in de lijst met toepassingen .

    ![De SmarterU-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-smarteru-test-user"></a>SmarterU-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij SmarterU, moeten ze worden ingericht in SmarterU. In het geval van SmarterU is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. meld u aan bij uw **SmarterU-huurder.**

1. Ga naar **Gebruikers.**

1. Voer in de gebruikerssectie de volgende stappen uit:

    ![Nieuwe gebruiker](./media/smarteru-tutorial/adduser.png)  

    a. Klik **op +Gebruiker**.

    b. Typ de bijbehorende kenmerkwaarden van het Azure AD-gebruikersaccount in de volgende tekstvakken: **Primaire e-mail,** **werknemer-id**, **wachtwoord**, **wachtwoord verifiëren**, **Voornaam**, **Achternaam**.

    c. Klik **op Actief**.

    d. Klik op **Opslaan**.

> [!NOTE]
> U alle andere smarteru-hulpprogramma's voor het maken van gebruikersaccounts of API's die door SmarterU worden verstrekt, gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SmarterU in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SmarterU waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
