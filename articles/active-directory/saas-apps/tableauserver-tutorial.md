---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Tableau Server | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Tableau Server kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: cb052f4ce962b1757f47f64e3f26fa5db2a731fa
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Tableau Server

In deze zelfstudie leert u hoe u Tableau Server kunt integreren met Azure AD (Active Directory). Wanneer u Tableau Server integreert met Azure AD, kunt u:

* In Azure AD de toegang beheren tot Tableau Server.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Tableau Server.
* Uw accounts op één centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Ingeschakeld Tableau Server-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Tableau Server ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Zodra u Tableau Server hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Tableau Server toevoegen vanuit de galerie

Voor het configureren van de integratie van Tableau Server met Azure AD moet u Tableau Server uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Tableau Server** in het zoekvak.
1. Selecteer **Tableau Server** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Azure AD-eenmalige aanmelding configureren en testen voor Tableau Server

Configureer en test Azure AD-eenmalige aanmelding met Tableau Server met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Tableau Server.

Voltooi de volgende bouwstenen om Azure AD-eenmalige aanmelding met Tableau Server te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Tableau Server configureren](#configure-tableau-server-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Tableau Server-testgebruiker maken](#create-tableau-server-test-user)** : als tegenhanger van B.Simon in Tableau Server die is gekoppeld aan de representatie van een gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Tableau Server** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > De bovenstaande waarden zijn geen echte waarden. Werk de waarden bij met de werkelijke URL en id van de Tableau Server-configuratiepagina die verderop in de zelfstudie wordt uitgelegd.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Tableau Server instellen** de juiste URL(‘s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Tableau Server.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Tableau Server** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-server-sso"></a>Eenmalige aanmelding van Tableau Server configureren

1. Als u wilt dat eenmalige aanmelding voor uw toepassing wordt geconfigureerd, moet u zich bij uw Tableau Server-tenant aanmelden als beheerder.

2. Selecteer op het tabblad **CONFIGURATION** **Gebruikers-id en -toegang** en selecteer vervolgens het tabblad **Verificatiemethode**.

    ![Eenmalige aanmelding configureren](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Voer de volgende stappen uit op de pagina **CONFIGURATION**:

    ![Eenmalige aanmelding configureren](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Selecteer SAML bij **Verificatiemethode**.

    b. Schakel het selectievakje in bij **SAML-verificatie inschakelen voor de server**.

    c. Retour-URL Tableau Server: de URL waarmee Tableau Server-gebruikers toegang krijgen, zoals `http://tableau_server`. Het gebruik van `http://localhost` is niet aanbevolen. Het gebruik van een URL met een afsluitende slash (bijvoorbeeld `http://tableau_server/`) wordt niet ondersteund. Kopieer de **Retour-URL van Tableau Server** en plak deze in het tekstvak **URL voor aanmelden** in de sectie **Standaard SAML-configuratie** in Azure Portal

    d. SAML-entiteit-id: de entiteit-id is een unieke aanduiding voor uw Tableau Server-installatie voor de IdP. U kunt de Tableau Server-URL hier opnieuw opgeven, maar dit hoeft niet uw Tableau Server-URL te zijn. Kopieer de **SAML-entiteit-id** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in Azure Portal

    e. Klik op de optie **XML-metagegevensbestand downloaden** en open het in de teksteditortoepassing. Zoek de URL voor Assertion Consumer Service met HTTP POST en index 0 en kopieer de URL. Plak deze nu in het tekstvak **antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal

    f. Zoek het bestand met federatieve metagegevens op dat is gedownload van Azure Portal, en upload het in het bestand met het **metagegevensbestand van de SAML-Idp**.

    g. Voer de namen in voor de kenmerken die de IdP gebruikt om de gebruikersnamen, weergavenamen en e-mailadressen op te slaan.

    h. Klik op **Opslaan**.

    > [!NOTE]
    > De klant moet een PEM gecodeerd x509-certificaatbestand uploaden met de extensie CRT en een met de RSA-privésleutelbestand, of een DSA-privésleutelbestand met de extensie KEY als certificaatsleutelbestand. Raadpleeg [dit](https://help.tableau.com/current/server/en-us/saml_requ.htm) document voor meer informatie over het certificaatbestand en het certificaatsleutelbestand. Als u hulp nodig hebt bij het configureren van SAML op Tableau Server, raadpleegt u dit artikel [Serverbrede SAML configureren](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Een testgebruiker voor Tableau Server maken

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in Tableau Server. U moet alle gebruikers in Tableau Server inrichten.

De gebruikersnaam van de gebruiker moet overeenkomen met de waarde die u hebt geconfigureerd in het aangepaste Azure AD-kenmerk van **gebruikersnaam**. Met de juiste toewijzing moet de integratie werken met het configureren van Azure AD-eenmalige aanmelding.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, moet u contact opnemen met de beheerder van Tableau Server in uw organisatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Tableau Server klikt, wordt u automatisch aangemeld bij de instantie van Tableau Server waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Tableau Server met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)