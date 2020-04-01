---
title: 'Zelfstudie: Azure Active Directory-integratie met Mozy Enterprise | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233500"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Zelfstudie: Azure Active Directory-integratie met Mozy Enterprise

In deze zelfstudie leert u hoe u Mozy Enterprise integreert met Azure Active Directory (Azure AD).
De integratie van Mozy Enterprise met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Mozy Enterprise.
* U uw gebruikers automatisch laten aanmelden bij Mozy Enterprise (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie van Azure met Mozy Enterprise wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Mozy Enterprise single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mozy Enterprise ondersteunt **door SP** geïnitieerde SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy Enterprise toevoegen vanuit de galerie

Als u de integratie van Mozy Enterprise in Azure AD wilt configureren, moet u Mozy Enterprise vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Mozy Enterprise vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Mozy Enterprise**in het zoekvak , selecteer **Mozy Enterprise** in het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Mozy Enterprise in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Mozy Enterprise op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Mozy Enterprise.

Als u Azure AD-singlesign-aan met Mozy Enterprise wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Mozy Enterprise-testgebruiker](#create-mozy-enterprise-test-user)** - om een tegenhanger van Britta Simon in Mozy Enterprise te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on met Mozy Enterprise te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Mozy** Enterprise-toepassingsintegratie de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Mozy Enterprise Domain en URL's single sign-on informatie](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [mozy Enterprise Client support team](https://support.mozy.com/) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Mozy Enterprise instellen** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Mozy Enterprise Single Sign-On configureren

1. Log in een ander browservenster in op uw Mozy Enterprise-bedrijfssite als beheerder.

2. Klik **in** de sectie Configuratie op **Verificatiebeleid**.
   
    ![Verificatiebeleid](./media/mozy-enterprise-tutorial/ic777314.png "Verificatiebeleid")

3. Voer in de sectie **Verificatiebeleid** de volgende stappen uit:
   
    ![Verificatiebeleid](./media/mozy-enterprise-tutorial/ic777315.png "Verificatiebeleid")
   
    a. Selecteer **Directory Service** als **provider**.
   
    b. Selecteer **LDAP-push gebruiken**.
   
    c. Klik op het tabblad **SAML-verificatie**.
   
    d. Url **van aanmelding**plakken , die u hebt gekopieerd van de Azure-portal naar het tekstvak **voor verificatie-URL.**
   
    e. Plak **Azure AD-id**, die u hebt gekopieerd van de Azure-portal in het **SAML-eindpunttekstvak.**
   
    f. Open het gedownloade basis-64 gecodeerde certificaat in kladblok, kopieer de inhoud ervan in het klembord en plak het volledige certificaat in het tekstvak **van het SAML-certificaat.**
   
    g. Selecteer **SSO inschakelen voor beheerders om in te loggen met hun netwerkreferenties.**
   
    h. Klik **op Wijzigingen opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Mozy Enterprise.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Mozy Enterprise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Mozy Enterprise**in de lijst met toepassingen .

    ![De Mozy Enterprise-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mozy-enterprise-test-user"></a>Mozy Enterprise-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Mozy Enterprise, moeten ze worden ingericht in Mozy Enterprise. In het geval van Mozy Enterprise is inrichten een handmatige taak.

>[!NOTE]
>U alle andere mozy enterprise-hulpprogramma's voor het maken van gebruikersaccounts of API's die mozy enterprise biedt, gebruiken om Azure AD-gebruikersaccounts in te richten.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Log in bij uw **Mozy** Enterprise-huurder.

2. Klik op **Gebruikers**en klik vervolgens op **Nieuwe gebruiker toevoegen.**
   
    ![Gebruikers](./media/mozy-enterprise-tutorial/ic777317.png "Gebruikers")
   
    >[!NOTE]
    >De optie **Nieuwe gebruiker toevoegen** wordt alleen weergegeven als **Mozy** is geselecteerd als provider onder **Verificatiebeleid**. Als SAML-verificatie is geconfigureerd, worden de gebruikers automatisch toegevoegd op hun eerste login via Single sign-on.
    
3. Voer in het dialoogvenster nieuwe gebruiker de volgende stappen uit:
   
    ![Gebruikers toevoegen](./media/mozy-enterprise-tutorial/ic777318.png "Gebruikers toevoegen")
   
    a. Selecteer een groep in de lijst **Een groep** kiezen.
   
    b. Selecteer een type gebruikerslijst in de lijst **Welk type gebruiker.**
   
    c. Typ in het tekstvak **Gebruikersnaam** de naam van de Azure AD-gebruiker.
   
    d. Typ in het tekstvak **E-mail** het e-mailadres van de Azure AD-gebruiker.
   
    e. Selecteer **E-mail met gebruikersinstructies verzenden**.
   
    f. Klik **op Gebruiker(s) toevoegen.**

     >[!NOTE]
     > Nadat de gebruiker is gemaakt, wordt een e-mail verzonden naar de Azure AD-gebruiker met een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de mozy enterprise-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Mozy Enterprise waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

