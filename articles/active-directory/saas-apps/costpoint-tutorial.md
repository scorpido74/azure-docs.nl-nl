---
title: 'Zelfstudie: Azure Active Directory-integratie met Costpoint | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 1db1589561968cf1e2974edcee2bfe1cccb4a327
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549242"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Zelfstudie: Costpoint integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Costpoint kunt integreren met Azure AD (Active Directory). Wanneer u Costpoint integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Costpoint.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Costpoint.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Costpoint-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD in een testomgeving. Costpoint biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding.

## <a name="generate-costpoint-metadata"></a>Metagegevens voor Costpoint genereren

Costpoint SAML SSO-configuratie wordt uitgelegd in de handleiding **DeltekCostpoint711Security.pdf**. Download deze handleiding op de ondersteuningssite van Deltek Costpoint en raadpleeg de sectie **Eenmalige aanmelding instellen met SAML** > **Eenmalige aanmelding van SAML instellen tussen Costpoint en Microsoft Azure**. Volg de instructies en genereer een **XML-bestand met federatieve metagegevens voor Costpoint SP**. 

![Hulpprogramma voor Costpoint-configuratie](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint toevoegen vanuit de galerie

Om Costpoint te integreren met Azure AD voegt u Costpoint eerst vanuit de galerie toe aan de lijst met beheerde SaaS-apps in Azure Portal:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.

   ![De knop Azure Active Directory](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

   ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

   ![De knop Nieuwe toepassing](common/add-new-app.png)

1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Costpoint**.

   ![Costpoint in de resultatenlijst](common/search-new-app.png)

1. Selecteer **SiteIntel** in de resultatenlijst, en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Costpoint met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Costpoint.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Costpoint, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Configureer Costpoint](#configure-costpoint)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Een Costpoint-testgebruiker maken](#create-a-costpoint-test-user)** : als u een tegenhanger van B.Simon in Costpoint wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Selecteer op de integratiepagina van de toepassing **Costpoint** de optie **Eenmalige aanmelding**.

   ![De koppeling voor eenmalige aanmelding configureren](common/select-sso.png)

1. Voltooi in de sectie **Standaard SAML-configuratie** deze stappen als u het *Service Provider-metagegevensbestand* hebt:

   > [!NOTE]
   > U downloadt het Service Provider-metagegevensbestand in [Metagegevens voor Costpoint genereren](#generate-costpoint-metadata). Later in de zelfstudie wordt uitgelegd hoe u het bestand gebruikt.
 
   1. Selecteer de knop **Metagegevensbestand uploaden**. Selecteer het **XML-bestand met federatieve metagegevens voor Costpoint SP** dat eerder is gegenereerd in Costpoint, en selecteer vervolgens de knop **Toevoegen** om het bestand te uploaden.

      ![Het metagegevensbestand uploaden](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Wanneer het metagegevensbestand is geüpload, worden de waarde voor **Id** en **Antwoord-URL** automatisch ingevuld in de Costpoint-sectie.

      > [!NOTE]
      > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, voert u de waarden handmatig in afhankelijk van uw vereisten. Controleer of **Id (Entiteits-id)** en **Antwoord-URL (URL voor Assertion Consumer Service)** juist zijn ingesteld, en dat **ACS-URL** een geldige Costpoint-URL is die eindigt met **/LoginServlet.cps**.

   1. Selecteer **Extra URL's instellen**. Voer voor **Relaystatus** een waarde in met het volgende patroon:`system=[your system]` (bijvoorbeeld **system=DELTEKCP**).

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** in de sectie **SAML-handtekeningcertificaat** het pictogram **Kopiëren** om de **App-URL voor federatieve metagegevens** te kopiëren, en sla deze op in Kladblok.

   ![SAML-handtekeningcertificaat](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint configureren

1. Ga terug naar het hulpprogramma voor Costpoint-configuratie. Plak in het tekstvak **XML-bestand met federatieve IdP-metagegevens** de inhoud van het bestand *App-URL voor federatieve metagegevens*. 

   ![Hulpprogramma voor Costpoint-configuratie](./media/costpoint-tutorial/config-utility-idp.png)

1. Ga verder met de instructies uit de handleiding **DeltekCostpoint711Security.pdf** om het instellen van Costpoint SAML te voltooien.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in Azure Portal een testgebruiker te maken met de naam B.Simon.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

   ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

   ![Knop Nieuwe gebruiker](common/new-user.png)

1. Ga als volgt te werk in het venster dat**** verschijnt:

   ![Het dialoogvenster Gebruiker](common/user-properties.png)

   1. Typ **B.Simon** in het veld **Naam**.
   
   1. Voer in het veld **Gebruikersnaam** in: `b.simon\@yourcompanydomain.extension` (bijvoorbeeld B.Simon@contoso.com).
   
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord**.
   
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Costpoint.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.

1. Selecteer **Costpoint** in de lijst met toepassingen.

1. Selecteer op de overzichtspagina van de app in de sectie **Beheren** de optie **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in de lijst **Gebruikers** in het dialoogvenster **Gebruikers en groepen**. Kies vervolgens **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren**.

1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-costpoint-test-user"></a>Een Costpoint-testgebruiker maken

In deze sectie maakt u een gebruiker in Costpoint. Ga ervanuit dat de gebruikers-id **B.SIMON** is, en dat de naam van de gebruiker **B.Simon** is. Neem contact op met het [klantondersteuningsteam van Costpoint](https://www.deltek.com/about/contact-us) om de gebruiker toe te voegen aan het Costpoint-platform. De gebruiker moet worden gemaakt en geactiveerd voordat deze eenmalige aanmelding kan gebruiken.

Nadat de gebruiker is gemaakt, moet de geselecteerde **Verificatiemethode** van de gebruiker **Active Directory** zijn. Ook moet het selectievakje **Eenmalige aanmelding voor SAML** zijn geselecteerd, en moet de gebruikersnaam van Azure Active Directory **Active Directory of Certificaat-id** zijn (zoals weergegeven in de volgende schermopname).

![Costpoint-gebruiker](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Costpoint selecteert, wordt u automatisch aangemeld bij de Costpoint-toepassing omdat u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
