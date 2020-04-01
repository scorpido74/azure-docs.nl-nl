---
title: 'Zelfstudie: Azure Active Directory-integratie met Proxyclick | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding tussen Azure Active Directory en Proxyclick configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093499"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Zelfstudie: Azure Active Directory-integratie met Proxyclick

In deze zelfstudie leert u hoe u Proxyclick integreert met Azure Active Directory (Azure AD).
Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Proxyclick.
* U uw gebruikers automatisch laten inloggen op Proxyclick (eenmalige aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Proxyclick wilt configureren, moet u het:

* Een Azure AD-abonnement Als u geen Azure [AD-omgeving](https://azure.microsoft.com/pricing/free-trial/)hebt, u zich aanmelden voor een proefversie van één maand.
* Een Proxyclick-abonnement dat één aanmelding heeft ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* Proxyclick ondersteunt door SP geïnitieerde en IdP-geïnitieerde SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick toevoegen vanuit de galerie

Als u de integratie van Proxyclick in Azure AD wilt instellen, moet u Proxyclick vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer **proxyklik**in in het zoekvak . Selecteer **Proxyclick** in de zoekresultaten en selecteer **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Proxyclick met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Proxyclick.

Als u Azure AD-enkele aanmelding met Proxyclick wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Proxyclick single sign-on](#configure-proxyclick-single-sign-on)** configureren aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een proxyclick-testgebruiker](#create-a-proxyclick-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Proxyclick te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Proxyclick-toepassingsintegratie de optie **Eén aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Als u de toepassing in de idp-startmodus wilt configureren, neemt u in het dialoogvenster **BasisSAML-configuratie** de volgende stappen.

    ![Dialoogvenster BasisSAML-configuratie](common/idp-intiated.png)

    1. Voer in het vak **Id** een URL in dit patroon in:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Voer in het vak **URL van antwoord** een URL in dit patroon in:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Als u de toepassing wilt configureren in de door SP geïnitieerde modus, selecteert u **Extra URL's instellen**. Voer in het vak **Aanmelding smaken van URL** een URL in dit patroon in:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Informatie over proxyclick-domein en URL's met eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de werkelijke id, de URL van het antwoord en de url van aanmelding gebruiken. Stappen voor het verkrijgen van deze waarden worden later beschreven in deze zelfstudie.

6. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** naast **Certificaat (Base64)** en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Proxyclick instellen** de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick-aanmelding configureren

1. Meld u in een nieuw browservenster aan bij uw proxyclick-bedrijfssite als beheerder.

2. Selecteer **Instellingen voor account&:**

    ![Accountinstellingen & selecteren](./media/proxyclick-tutorial/configure1.png)

3. Schuif omlaag naar de sectie **Integraties** en selecteer **SAML:**

    ![SAML selecteren](./media/proxyclick-tutorial/configure2.png)

4. Neem in de sectie **SAML** de volgende stappen.

    ![SAML-sectie](./media/proxyclick-tutorial/configure3.png)

    1. Kopieer de **URL-waarde van SAML-consument** en plak deze in het vak **URL van antwoord** in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

    1. Kopieer de **URL-waarde van SAML SSO Redirect** en plak deze in de vakken **Aanmelden op URL** en **Id** in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

    1. Selecteer **HTTP Redirect**in de lijst **SAML-aanvraagmethode** .

    1. Plak in het vak **Uitgever** de Azure **AD-id-waarde** die u hebt gekopieerd vanuit de Azure-portal.

    1. Plak in het vak **SAML 2.0-eindpunt URL** de **url-waarde aanmelding** die u hebt gekopieerd uit de Azure-portal.

    1. Open in Kladblok het certificaatbestand dat u hebt gedownload van de Azure-portal. Plak de inhoud van dit bestand in het vak **Certificaat.**

    1. Selecteer **Save changes**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door haar toegang te verlenen tot Proxyclick.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Proxyclick**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Proxyclick**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het venster.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-proxyclick-test-user"></a>Een proxyclick-testgebruiker maken

Als u Azure AD-gebruikers wilt laten inloggen bij Proxyclick, moet u deze toevoegen aan Proxyclick. Je moet ze handmatig toevoegen.

Als u een gebruikersaccount wilt maken, neemt u de volgende stappen:

1. Meld u aan bij uw proxyclick-bedrijfssite als beheerder.

1. Selecteer **Collega's** boven aan het venster:

    ![Collega's selecteren](./media/proxyclick-tutorial/user1.png)

1. Selecteer **Collega toevoegen:**

    ![Collega toevoegen selecteren](./media/proxyclick-tutorial/user2.png)

1. Neem **in** de sectie Een collega toevoegen de volgende stappen.

    ![Een collega-sectie toevoegen](./media/proxyclick-tutorial/user3.png)

    1. Voer in het vak **E-mail** het e-mailadres van de gebruiker in. In dit geval, **brittasimon\@contoso.com**.

    1. Voer in het vak **Voornaam** de voornaam van de gebruiker in. In dit geval, **Britta.**

    1. Voer in het vak **Achternaam** de achternaam van de gebruiker in. In dit geval, **Simon.**

    1. Selecteer **Gebruiker toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de tegel Proxyclick selecteert in het access-paneel, moet u automatisch worden aangemeld bij het exemplaar Proxyclick waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over het toegangspaneel.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

