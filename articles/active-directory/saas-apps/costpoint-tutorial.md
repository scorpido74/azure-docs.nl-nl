---
title: 'Zelfstudie: Integratie met Costpoint Azure Active Directory | Microsoft Docs'
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
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840118"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Zelfstudie: Costpoint integreren met Azure Active Directory

In deze zelf studie leert u hoe u Costpoint integreert met Azure Active Directory (Azure AD). Wanneer u Costpoint integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Costpoint.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Costpoint met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Costpoint-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie gaat u Azure AD-SSO configureren en testen in een test omgeving. Costpoint ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="generate-costpoint-metadata"></a>Costpoint-meta gegevens genereren

Costpoint SAML SSO-configuratie wordt uitgelegd in de **DeltekCostpoint711Security. PDF-** hand leiding. Down load deze hand leiding van de Deltek Costpoint support-site en Raadpleeg de installatie van de **eenmalige SAML-aanmelding** >  SAML-eenmalige aanmelding**configureren tussen de Costpoint en** de sectie Microsoft Azure. Volg de instructies en Genereer een XML-bestand met Costpoint voor de **SP-Federatie-meta gegevens** . 

![Hulp programma Costpoint-configuratie](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint toevoegen vanuit de galerie

Als u Costpoint met Azure AD wilt integreren, voegt u Costpoint eerst toe aan uw lijst met beheerde SaaS-apps uit de galerie in het Azure Portal:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.

   ![De Azure Active Directory-knop](common/select-azuread.png)

1. Selecteer **bedrijfs toepassingen** > **alle toepassingen**.

   ![De blade Enterprise-toepassingen](common/enterprise-applications.png)

1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.

   ![De knop nieuwe toepassing](common/add-new-app.png)

1. Voer in de sectie **toevoegen vanuit de galerie** **Costpoint** in het zoekvak in.

   ![Costpoint in de lijst met resultaten](common/search-new-app.png)

1. Selecteer in de lijst met resultaten **Costpoint**en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Azure AD eenmalige SGN configureren en testen

Azure AD SSO met Costpoint configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Costpoint.

Als u Azure AD SSO wilt configureren en testen met Costpoint, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Configureer Costpoint](#configure-costpoint)** om de SAML SSO-instellingen aan de kant van de toepassing te configureren.
1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Maak een Costpoint-test gebruiker](#create-a-costpoint-test-user)** die een equivalent van B. Simon heeft in Costpoint dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen in de Azure Portal:

1. Selecteer op de pagina Toepassings integratie van **Costpoint** de optie **eenmalige aanmelding**.

   ![De koppeling voor eenmalige aanmelding configureren](common/select-sso.png)

1. Als u het *META gegevensbestand van de service provider*hebt, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

   > [!NOTE]
   > U krijgt het meta gegevensbestand van de service provider in [Costpoint-meta gegevens genereren](#generate-costpoint-metadata). Het gebruik van het bestand wordt verderop in de zelf studie uitgelegd.
 
   1. Selecteer de knop voor het uploaden van het **META** gegevensbestand en selecteer vervolgens het **XML-bestand met Costpoint-meta gegevens** dat eerder is gegenereerd door Costpoint, en selecteer vervolgens de knop **toevoegen** om het bestand te uploaden.

      ![Het meta gegevensbestand uploaden](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Wanneer het meta gegevensbestand is geüpload, worden de **id** -en **antwoord-URL** -waarden automatisch ingevuld in de sectie Costpoint.

      > [!NOTE]
      > Als de **id** -en **antwoord-URL** -waarden niet automatisch worden polulated, voert u de waarden hand matig in volgens uw vereiste. Controleer of de **id (Entiteits-ID)** en de **antwoord-URL (URL van de Assertion Consumer Service)** correct zijn ingesteld en of de **ACS-URL** een geldige Costpoint-URL is die eindigt op **/LoginServlet.CPS**.

   1. Selecteer **extra Url's instellen**. Voer voor **Relay-status**een waarde in met behulp van het volgende patroon: `system=[your system]` (bijvoorbeeld **System = DELTEKCP**).

1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , het **Kopieer** pictogram om de URL voor de **federatieve meta gegevens** van de app te kopiëren en op te slaan in Klad blok.

   ![SAML-handtekeningcertificaat](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint configureren

1. Ga terug naar het Costpoint-configuratie hulpprogramma. Plak de inhoud van het *URL* -bestand met de IDP voor de **federatieve meta gegevens** in het tekstvak voor XML-gegevens. 

   ![Hulp programma Costpoint-configuratie](./media/costpoint-tutorial/config-utility-idp.png)

1. Ga door met de instructies in de hand leiding voor **DeltekCostpoint711Security. PDF** om de Costpoint SAML-installatie te volt ooien.

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

Het doel van deze sectie is het maken van een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

   !['Gebruikers en groepen' en 'Alle gebruikers' koppelingen](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

   ![Knop Nieuwe gebruiker](common/new-user.png)

1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :

   ![Het dialoogvenster gebruiker](common/user-properties.png)

   1. Voer in het veld **naam** **B. Simon**in.
   
   1. Voer in het veld **gebruikers naam** `b.simon\@yourcompanydomain.extension` in (bijvoorbeeld B.Simon@contoso.com).
   
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het veld **wacht woord** .
   
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door B. Simon toegang te verlenen tot Costpoint.

1. Selecteer in de Azure Portal **bedrijfs toepassingen** > **alle toepassingen**.

1. Selecteer in de lijst toepassingen de optie **Costpoint**.

1. Selecteer **gebruikers en groepen**in de sectie **beheren** van de app-overzichts pagina.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

   ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** in de lijst **gebruikers** de optie **B. Simon**. Kies vervolgens **selecteren**.

1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en kiest **u selecteren**.

1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-costpoint-test-user"></a>Een Costpoint-test gebruiker maken

In deze sectie maakt u een gebruiker in Costpoint. Stel dat de gebruikers-id **b. Simon** is en dat de naam van de gebruiker **b. Simon**is. Werk met het [Costpoint-client ondersteunings team](https://www.deltek.com/about/contact-us) om de gebruiker toe te voegen in het Costpoint-platform. U moet de gebruiker maken en activeren voordat u eenmalige aanmelding kunt gebruiken.

Nadat de gebruiker is gemaakt, moet de **verificatie methode** van de gebruiker worden **Active Directory**, moet het selectie vakje **eenmalige SAML-aanmelding** zijn ingeschakeld en moet de gebruikers naam van Azure Active Directory **Active Directory of Certificaat-ID** (weer gegeven in de volgende scherm afbeelding).

![Costpoint-gebruiker](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Costpoint in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de Costpoint-toepassing, omdat u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
