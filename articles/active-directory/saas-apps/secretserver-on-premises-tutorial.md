---
title: 'Zelfstudie: Azure Active Directory-integratie met Secret Server (On-Premises) | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Secret Server (On-Premises).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880126"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Zelfstudie: Secret Server (On-Premises) integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Secret Server (On-Premises) integreert met Azure Active Directory (Azure AD). Wanneer u Secret Server (On-Premises) integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Secret Server (On-Premises).
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Secret Server (On-Premises) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement op eenmalige aanmelding (Secret Server) (On-Premises).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Secret Server (On-Premises) ondersteunt **SP en IDP** geïnitieerde SSO

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Geheime server (on-premises) toevoegen vanuit de galerie

Als u de integratie van Secret Server (On-Premises) in Azure AD wilt configureren, moet u Secret Server (On-Premises) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Secret Server (On-Premises)** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **Secret Server (On-Premises)** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Secret Server (On-Premises) met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Secret Server (On-Premises).

Als u Azure AD SSO wilt configureren en testen met Secret Server (On-Premises), voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[SSO (Secret Server) configureren om](#configure-secret-server-on-premises-sso)** de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Testgebruiker Geheime server (On-Premises)](#create-secret-server-on-premises-test-user)** maken - om een tegenhanger van B.Simon in Secret Server (On-Premises) te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Secret Server (On-Premises)** toepassingsintegratie de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Voer in het tekstvak **Id** de door de gebruiker gekozen waarde in als voorbeeld:`https://secretserveronpremises.azure`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > De bovenstaande entiteits-id is slechts een voorbeeld en u bent vrij om een unieke waarde te kiezen die uw secret server-exemplaar in Azure AD identificeert. U moet deze entiteits-id naar [het clientondersteuningsteam van Secret Server (On-Premises)](https://thycotic.force.com/support/s/) verzenden en deze configureren aan hun kant. Voor meer informatie, lees [dit artikel](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [het clientondersteuningsteam van Secret Server (On-Premises)](https://thycotic.force.com/support/s/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram **Bewerken** om het dialoogvenster **SAML-ondertekeningscertificaat** te openen.

    ![Ondertekeningsopties](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Selecteer **Ondertekeningsoptie** als **antwoord en bewering van TEKEN SAML**.

    ![Ondertekeningsopties](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Kopieer in de sectie **Geheime server (On-Premises)** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>SSO (Secret Server (On-Premises) configureren

Als u eenmalige aanmelding wilt configureren aan de kant van de **geheime server (On-Premises),** moet u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van de Azure-portal naar het ondersteuningsteam van de [geheime server (On-Premises)](https://thycotic.force.com/support/s/)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Secret Server (On-Premises).

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer Secret Server **(On-Premises) in**de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-secret-server-on-premises-test-user"></a>Testgebruiker Geheime server (On-Premises) maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Secret Server (On-Premises). Werk samen met [het ondersteuningsteam van Secret Server (On-Premises)](https://thycotic.force.com/support/s/) om de gebruikers toe te voegen in het On-Premises platform (Secret Server). Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Geheime server (On-Premises) klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de geheime server (on-premises) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)