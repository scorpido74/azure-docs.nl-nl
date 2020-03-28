---
title: 'Zelfstudie: Azure Active Directory-integratie met Chargebee | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Chargebee.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 033d413d-1656-4d9c-a606-dd33c23948f9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbaf3d527ad1e58914c6b3f9c8b5b4ea57ae08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68931869"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Zelfstudie: Chargebee integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Chargebee integreren met Azure Active Directory (Azure AD). Wanneer u Chargebee integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Chargebee.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Chargebee met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Chargebee single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Chargebee ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-chargebee-from-the-gallery"></a>Chargebee toevoegen uit de galerie

Als u de integratie van Chargebee in Azure AD wilt configureren, moet u Chargebee vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Chargebee** in de sectie **Toevoegen in de galeriein** het zoekvak.
1. Selecteer **Chargebee** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Azure AD-aanmelding voor Chargebee configureren en testen

Azure AD SSO configureren en testen met Chargebee met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Chargebee.

Als u Azure AD SSO wilt configureren en testen met Chargebee, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Chargebee SSO configureren](#configure-chargebee-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak chargebee-testgebruiker](#create-chargebee-test-user)** - om een tegenhanger van B.Simon in Chargebee te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Chargebee-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<domainname>.chargebee.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.chargebee.com/saml/<domainname>/acs`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>`is de naam van het domein dat de gebruiker maakt nadat hij het account claimt. In het geval van andere informatie, neem dan contact op met [chargebee client support team](mailto:support@chargebee.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Chargebee instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Chargebee.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Chargebee**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-chargebee-sso"></a>Chargebee SSO configureren

1. Open een nieuw webbrowservenster en meld u aan op uw Chargebee-bedrijfssite als beheerder.

4. Klik links van het menu op **Instellingen** > **beveiligd** > **beheren**.

    ![Chargebee-configuratie](./media/chargebee-tutorial/config01.png)

5. Voer in de **pop-up Voor melding** de volgende stappen uit:

    ![Chargebee-configuratie](./media/chargebee-tutorial/config02.png)

    a. Selecteer **SAML**.

    b. Plak in het tekstvak **Inlog-URL** de **waarde van de inlog-URL** plakken die u hebt gekopieerd van de Azure-portal.

    c. Open het certificaat Base64 gecodeerd in kladblok, kopieer de inhoud ervan en plak het in het tekstvak **SAML-certificaat.**

    d. Klik op **Bevestigen**.

### <a name="create-chargebee-test-user"></a>Chargebee-testgebruiker maken

Als u Azure AD-gebruikers wilt inschakelen, moet u zich aanmelden bij Chargebee en moeten deze worden ingericht in Chargebee. In Chargebee is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u in een ander browservenster aan bij Chargebee als beveiligingsbeheerder.

2. Klik links van het menu op **Klanten** en navigeer vervolgens naar **Een nieuwe klant maken.**

    ![Freedcamp-configuratie](./media/chargebee-tutorial/config03.png)

3. Vul op de pagina **Nieuwe klant** de onderstaande velden in en klik op **Klant maken** voor het maken van de gebruiker.

    ![Freedcamp-configuratie](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de chargebee-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Chargebee waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

