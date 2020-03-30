---
title: 'Zelfstudie: Azure Active Directory-integratie met Percolate | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding tussen Azure Active Directory en Percolate configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094601"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Zelfstudie: Azure Active Directory-integratie met Percolate

In deze zelfstudie leert u hoe u Percolate integreert met Azure Active Directory (Azure AD).

Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Percolate.
* U uw gebruikers automatisch laten inloggen op Percolate (eenmalige aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Percolate wilt configureren, moet u het:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Een Percolate-abonnement dat één aanmelding heeft ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* Percolate ondersteunt door SP geïnitieerde en IdP-geïnitieerde SSO.

## <a name="add-percolate-from-the-gallery"></a>Percolate toevoegen vanuit de galerie

Als u de integratie van Percolate in Azure AD wilt configureren, moet u Percolate uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer **Percolate**in in het zoekvak . Selecteer **Percolate** in de zoekresultaten en selecteer **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Percolate met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Percolate.

Als u Azure AD Single Sign-on met Percolate wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Percolate-aanmelding](#configure-percolate-single-sign-on)** configureren aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een Percolate-testgebruiker](#create-a-percolate-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Percolate te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Percolate-toepassingsintegratie** de optie **Enkele aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. In het dialoogvenster **BasisSAML-configuratie** hoeft u geen actie te ondernemen om de toepassing in de idp-modus te configureren. De app is al geïntegreerd met Azure.

    ![Percolate-domein- en URL's met eenmalige aanmeldingsgegevens](common/preintegrated.png)

5. Als u de toepassing in de door SP gestarte modus wilt configureren, selecteert u **https://percolate.com/app/login**Extra **URL's instellen** en voert u in het vak Aanmelden op **URL** het selectievakje IN:

   ![Percolate-domein- en URL's met eenmalige aanmeldingsgegevens](common/metadata-upload-additional-signon.png)
6. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** het pictogram **Kopiëren** om de url van de **appfederatie-metagegevens**te kopiëren. Sla deze URL op.

    ![De URL van de app-federatie-metagegevens kopiëren](common/copy-metadataurl.png)

7. Kopieer in de sectie **Percolate instellen** de juiste URL's op basis van uw vereisten.

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.

### <a name="configure-percolate-single-sign-on"></a>Percolate-aanmelding configureren

1. Meld u in een nieuw browservenster aan bij Percolate als beheerder.

2. Selecteer Aan de linkerkant van de startpagina de optie **Instellingen:**
    
    ![Instellingen selecteren](./media/percolate-tutorial/configure01.png)

3. Selecteer In het linkerdeelvenster De optie **SSO** onder **Organisatie:**

    ![SSO selecteren onder Organisatie](./media/percolate-tutorial/configure02.png)

    1. Plak in het vak **Aanmeldings-URL** de **waarde van de inlog-URL** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het vak **Entiteits-id** de Azure **AD-id-waarde** die u hebt gekopieerd vanuit de Azure-portal.

    1. Open in Kladblok het basis-64-gecodeerde certificaat dat u hebt gedownload van de Azure-portal. Kopieer de inhoud en plak deze in het vak **x509-certificaten.**

    1. Voer in het vak **E-mailkenmerk** **e-mailadres in**.

    1. Het **URL-vak Van de identiteitsprovider-metagegevens** is een optioneel veld. Als u een **url met aalmetagegevens van app-federatie hebt** gekopieerd van de Azure-portal, u deze in dit vak plakken.

    1. Selecteer **Nee**in de lijst **Moet AuthNRequests worden ondertekend?**

    1. Selecteer **Nee**in de lijst **SSO automatisch inrichten inschakelen** .

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam Britta Simon in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** in het linkerdeelvenster, selecteer **Gebruikers**en selecteer **Alle gebruikers:**

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Neem **in** het dialoogvenster Gebruiker de volgende stappen.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **BrittaSimon@\<uw\< bedrijfsdomein in>. uitbreiding>**. (Bijvoorbeeld .) BrittaSimon@contoso.com

    1. Selecteer **Wachtwoord weergeven**en noteer de waarde in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure AD-eenmaligaanmelding te gebruiken door haar toegang te verlenen tot Percolate.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Vervolgens Percolate**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Percolate**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruikers en groepen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-percolate-test-user"></a>Een Percolate-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Percolate, moet u deze toevoegen aan Percolate. Je moet ze handmatig toevoegen.

Als u een gebruikersaccount wilt maken, neemt u de volgende stappen:

1. Meld u aan bij Percolate als beheerder.

2. Selecteer in het linkerdeelvenster de optie **Gebruikers** onder **Organisatie**. Selecteer **Nieuwe gebruikers:**

    ![Nieuwe gebruikers selecteren](./media/percolate-tutorial/configure03.png)

3. Neem op de pagina **Gebruikers maken** de volgende stappen.

    ![Pagina Gebruikers maken](./media/percolate-tutorial/configure04.png)

    1. Voer in het vak **E-mail** het e-mailadres van de gebruiker in. Bijvoorbeeld brittasimon@contoso.com.

    1. Voer in het vak **Volledige naam** de naam van de gebruiker in. Bijvoorbeeld, **Brittasimon**.

    1. Selecteer **Gebruikers maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de tegel Percolate selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het project Percolate waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)