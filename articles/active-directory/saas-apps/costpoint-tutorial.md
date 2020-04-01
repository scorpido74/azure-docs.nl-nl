---
title: 'Zelfstudie: Azure Active Directory-integratie met Costpoint | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71840118"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Zelfstudie: Costpoint integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Costpoint integreert met Azure Active Directory (Azure AD). Wanneer u Costpoint integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Costpoint.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Costpoint met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een Abonnement met een malige aanmelding (Costpoint).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Costpoint ondersteunt **SP en IDP** geïnitieerd sso.

## <a name="generate-costpoint-metadata"></a>Costpoint-metagegevens genereren

Costpoint SAML SSO-configuratie wordt uitgelegd in de **deltekCostpoint711Security.pdf-handleiding.** Download deze handleiding van de ondersteuningssite van Deltek Costpoint en raadpleeg de **SAML Single Sign-on Setup** > **Configure SAML Single Sign-on between Costpoint en Microsoft Azure.** Volg de instructies en genereer een **XML-bestand met Costpoint SP Federation Metadata.** 

![Hulpprogramma voor costpointconfiguratie](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint toevoegen vanuit de galerie

Als u Costpoint wilt integreren met Azure AD, voegt u Costpoint eerst toe aan uw lijst met beheerde SaaS-apps vanuit de galerie in de Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**

   ![De knop Azure Active Directory](common/select-azuread.png)

1. Selecteer**Alle toepassingen** **voor ondernemingen** > selecteren .

   ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

   ![De knop Nieuwe toepassing](common/add-new-app.png)

1. Voer **Costpoint** in het zoekvak in in de sectie **Toevoegen in de sectie Toevoegen in de galerie.**

   ![Costpoint in de resultatenlijst](common/search-new-app.png)

1. Selecteer **Kostenpunt**in de lijst met resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Azure AD single sgn-on configureren en testen

Azure AD SSO configureren en testen met Costpoint met behulp van een testgebruiker met de naam **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Costpoint.

Als u Azure AD SSO wilt configureren en testen met Costpoint, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Configureer Costpoint](#configure-costpoint)** om de SAML SSO-instellingen aan toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Maak een Costpoint-testgebruiker](#create-a-costpoint-test-user)** om een tegenhanger van B.Simon in Costpoint te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg de volgende stappen om Azure AD SSO in te schakelen in de Azure-portal:

1. Selecteer op de pagina **Kostenpunt-toepassingintegratie** de optie **Eén aanmelding**.

   ![De koppeling met één aanmelding configureren](common/select-sso.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u het *metagegevensbestand van*de serviceprovider hebt:

   > [!NOTE]
   > U krijgt het metagegevensbestand van de serviceprovider in [metagegevens van Costpoint genereren.](#generate-costpoint-metadata) Hoe het bestand te gebruiken wordt later in de zelfstudie uitgelegd.
 
   1. Selecteer de knop **Metagegevens uploaden,** selecteer vervolgens het **XML-bestand Costpoint SP Federation Metadata** dat eerder door Costpoint is gegenereerd en selecteer vervolgens de knop **Toevoegen** om het bestand te uploaden.

      ![Het metagegevensbestand uploaden](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Wanneer het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in de sectie Costpoint.

      > [!NOTE]
      > Als de **URL-waarden Id** en **Antwoord** niet automatisch worden gepolueerd, voert u de waarden handmatig in op basis van uw vereiste. Controleer of **id(entiteit-id)** en **de URL van de antwoord -URL (URL van de bevestigingsservice)** correct zijn ingesteld en of de URL van **ACS** een geldige URL van het Kostenpunt is die eindigt met **/LoginServlet.cps**.

   1. Selecteer **Extra URL's instellen**. Voer **bij Relay State**een waarde`system=[your system]` in met behulp van het volgende patroon: (bijvoorbeeld **system=DELTEKCP**).

1. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** het pictogram **Kopiëren** om de url van de **appfederatie-metagegevens** te kopiëren en op te slaan in kladblok.

   ![SAML-handtekeningcertificaat](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint configureren

1. Ga terug naar Costpoint Configuration Utility. Plak in het **XML-tekstvak met gegevens van IdP Federation** De inhoud van het *url-bestand app-federatiemetagegevens.* 

   ![Hulpprogramma voor costpointconfiguratie](./media/costpoint-tutorial/config-utility-idp.png)

1. Ga verder met de instructies van de gids **DeltekCostpoint711Security.pdf** om de Costpoint SAML-installatie te voltooien.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is het maken van een testgebruiker in de Azure-portal met de naam B.Simon.

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory** > **Users** > **All users All users**.

   ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

   ![Knop Nieuwe gebruiker](common/new-user.png)

1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:

   ![Het dialoogvenster Gebruiker](common/user-properties.png)

   1. Voer in het veld **Naam** **B.Simon**in.
   
   1. Voer **in** het veld `b.simon\@yourcompanydomain.extension` Gebruikersnaam in B.Simon@contoso.com(bijvoorbeeld).
   
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord.**
   
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door B.Simon toegang te verlenen tot Costpoint.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 

1. Selecteer **Costpoint**in de lijst met toepassingen .

1. Selecteer gebruikers en groepen in het gedeelte **Beheren** van de **overzichtspagina**van de app.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon**in het dialoogvenster **Gebruikers en groepen** in de lijst **Gebruikers** . Kies vervolgens **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en kiest u **Selecteer**.

1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-costpoint-test-user"></a>Een Kostenpunttestgebruiker maken

In deze sectie maakt u een gebruiker in Costpoint. Stel dat de gebruikersnaam **B.SIMON** is en de naam van de gebruiker **Is B.Simon**. Werk samen met het [costpoint-ondersteuningsteam](https://www.deltek.com/about/contact-us) om de gebruiker toe te voegen aan het Costpoint-platform. De gebruiker moet worden gemaakt en geactiveerd voordat hij eenmalige aanmelding kan gebruiken.

Nadat de gebruiker is gemaakt, moet de selectie van de **verificatiemethode** van de gebruiker **Active Directory**zijn, moet het selectievakje EENMALIG **aanmelden SAML** zijn ingeschakeld en moet de gebruikersnaam uit Azure Active Directory **Active Directory of Certificaat-id** zijn (weergegeven in de volgende schermafbeelding).

![Costpoint-gebruiker](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Test SSO

Wanneer u de tegel Costpoint selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Costpoint-toepassing omdat u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies om SaaS-apps te integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
