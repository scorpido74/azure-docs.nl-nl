---
title: 'Zelfstudie: Azure Active Directory-integratie met InsideView | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding tussen Azure Active Directory en InsideView configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100093"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Zelfstudie: Azure Active Directory-integratie met InsideView

In deze zelfstudie leert u hoe u InsideView integreert met Azure Active Directory (Azure AD).
Deze integratie biedt deze voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot InsideView.
* U uw gebruikers automatisch laten inloggen op InsideView (eenmalige aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met InsideView, moet u het:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Een InsideView-abonnement met eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD single sign-on in een testomgeving.

* InsideView ondersteunt door IdP geïnitieerde SSO.

## <a name="add-insideview-from-the-gallery"></a>InsideView toevoegen vanuit de galerie

Als u de integratie van InsideView in Azure AD wilt instellen, moet u InsideView vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen:**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Voer in het zoekvak **InsideView**in . Selecteer **InsideView** in de zoekresultaten en selecteer **Toevoegen**.

    ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met InsideView met behulp van een testgebruiker genaamd Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de desbetreffende gebruiker in InsideView.

Als u Azure AD-aanmelding met InsideView wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Configureer de single sign-on van Azure AD](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureer InsideView single sign-on](#configure-insideview-single-sign-on)** aan de toepassingszijde.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om azure AD-enkele aanmelding te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om azure AD-enkele aanmelding voor de gebruiker in te schakelen.
5. **[Maak een InsideView-testgebruiker](#create-an-insideview-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in.

Ga als volgt te werk om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met InsideView te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Met toepassingen in InsideView de optie **Eén aanmelding:**

    ![Eén aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen:

    ![Eén aanmeldingsmethode selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het pictogram **Bewerken** om het dialoogvenster **BasisSAML-configuratie** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Neem in het dialoogvenster **BasisSAML-configuratie** de volgende stappen.

    ![Dialoogvenster BasisSAML-configuratie](common/idp-reply.png)

    Voer in het vak **URL van antwoord** een URL in dit patroon in:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Deze waarde is een tijdelijke aanduiding. U moet de werkelijke antwoord-URL gebruiken. Neem contact op met het [ondersteuningsteam van InsideView](mailto:support@insideview.com) om de waarde te krijgen. U ook verwijzen naar de patronen die worden weergegeven in het dialoogvenster **BasisSAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de koppeling **Downloaden** naast **Certificaat (Raw)** en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer in de sectie **InsideView instellen** de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Url van afmelden**.

### <a name="configure-insideview-single-sign-on"></a>InsideView-aanmelding configureren

1. Meld u in een nieuw browservenster aan bij uw InsideView-bedrijfssite als beheerder.

1. Selecteer boven aan het venster De optie **Beheer,** **Instellingen voor SingleSignOn**en **voeg SAML toe.**
   
   ![SAML-instellingen voor één aanmelding](./media/insideview-tutorial/ic794135.png "SAML-instellingen voor één aanmelding")

1. Neem in de sectie **Een nieuwe SAML toevoegen** de volgende stappen.

    ![Een nieuwe SAML-sectie toevoegen](./media/insideview-tutorial/ic794136.png "Een nieuwe SAML-sectie toevoegen")

    1. Voer in het vak **STS-naam** een naam voor uw configuratie in.

    1. Plak in het vak **SamlP/WS-Fed Ongevraagd EndPoint** de **URL-waarde aanmelding** die u hebt gekopieerd uit de Azure-portal.

    1. Open het Raw-certificaat dat u hebt gedownload van de Azure-portal. Kopieer de inhoud van het certificaat naar het klembord en plak de inhoud vervolgens in het vak **STS-certificaat.**

    1. Voer in het vak **Crm-gebruikersnaamtoewijzing** de invoer in **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. Voer in het vak **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** **E-mailtoewijzing van Crm** in .

    1. Voer in het vak Crm **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** **Voornaamtoewijzing** invoeren.

    1. Voer in het vak Crm **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** **lastName Mapping** de enter .  

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam Britta Simon in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** in het linkerdeelvenster, selecteer **Gebruikers**en selecteer **Alle gebruikers:**

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het venster:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Neem **in** het dialoogvenster Gebruiker de volgende stappen.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **BrittaSimon@\<uw\< bedrijfsdomein in>. uitbreiding>**. (Bijvoorbeeld .) BrittaSimon@contoso.com

    1. Selecteer **Wachtwoord weergeven**en noteer de waarde in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door haar toegang te verlenen tot InsideView.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **Vervolgens InsideView**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **InsideView**in de lijst met toepassingen .

    ![Lijst van aanvragen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **gebruikers en groepen:**

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen de optie **Britta Simon** in de gebruikerslijst en klik op de knop **Selecteren** onder aan het venster.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **Selecteren** onder aan het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-insideview-test-user"></a>Een InsideView-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij InsideView, moet u deze toevoegen aan InsideView. Je moet ze handmatig toevoegen.

Als u gebruikers of contactpersonen wilt maken in InsideView, neemt u contact op met het [ondersteuningsteam van InsideView](mailto:support@insideview.com).

> [!NOTE]
> U elk hulpprogramma voor het maken van gebruikersaccounts of API's van InsideView gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u uw Azure AD-configuratie met eenmalige aanmelding testen met behulp van het Access-paneel.

Wanneer u de InsideView-tegel selecteert in het Toegangspaneel, moet u automatisch worden aangemeld bij de InsideView-instantie waarvoor u SSO hebt ingesteld. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over het toegangspaneel.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
