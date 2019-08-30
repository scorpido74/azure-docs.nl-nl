---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met F5 | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166091"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met F5

In deze zelf studie leert u hoe u F5 kunt integreren met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* F5-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](#configure-f5-single-sign-on-for-header-based-application)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 wilt configureren in azure AD, moet u F5 toevoegen vanuit de galerie naar uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **F5** in het zoekvak.
1. Selecteer **F5** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Eenmalige aanmelding voor Azure AD voor F5 configureren en testen

Azure AD SSO met F5 configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker op F5.

Als u Azure AD SSO met F5 wilt configureren en testen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[F5 SSO configureren](#configure-f5-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een gebruiker van F5 test](#create-f5-test-user)** : als u een tegen hanger wilt hebben van B. Simon in F5 dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **F5** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het ondersteunings team van de [client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

1. Kopieer op de sectie **F5 instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan F5.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **F5**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst

1. Open een nieuw webbrowser venster en meld u aan bij uw bedrijfs site van F5 (op basis van de koptekst) als beheerder en voer de volgende stappen uit:

1. U moet het meta gegevens certificaat importeren in de F5 (op basis van de header) die later in het installatie proces wordt gebruikt. Ga naar **systeem > certificaat beheer > certificaat beheer > > SSL-certificaat lijst**. Klik op **importeren** van de rechter bovenhoek

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure12.png)
 
1. Daarnaast hebt u ook een **SSL-certificaat** nodig voor de`headerapp.superdemo.live`hostnaam (), in dit voor beeld we hebben het Joker teken certificaat gebruikt.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure13.png)

1. Ga naar – **F5 (op header gebaseerd) Big-IP klik > begeleide configuratie > federatie > SAML-service provider**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure01.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure02.png)
 
1. De **entiteit-id** opgeven (hetzelfde als wat u hebt geconfigureerd op de configuratie van de Azure AD-toepassing)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure03.png) 

1. Een nieuwe virtuele server maken, het **doel adres**opgeven, de omleidings **poort** is optioneel. Kies het **certificaat voor joker tekens** (of het **certificaat** dat u hebt geüpload voor de toepassing) dat u eerder hebt geüpload en de **bijbehorende persoonlijke sleutel**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure04.png) 

1. Upload de **meta gegevens** van de configuratie en geef een nieuwe **naam op voor de SAML IDP-connector** . u moet ook het federatieve certificaat opgeven dat u eerder hebt geüpload.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure05.png)
 
1. **Nieuwe maken** Back-end-app-groep, geeft u de **IP-adressen** op van de back-end-toepassings servers.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure06.png)

1. Kies onder eenmalige aanmelding http- **header**. U kunt andere headers toevoegen op basis van uw toepassing. Zie de bijlage voor de lijst met SAML-sessie variabelen

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure07.png) 

1. Contact opnemen met [F5 (gebaseerd op koptekst) client ondersteunings team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) voor **Eigenschappen van controle van eind punten** documentatie Details.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure08.png)

1. Contact opnemen met [F5 (gebaseerd op koptekst) client ondersteunings team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) voor **sessie beheer eigenschappen** documentatie Details.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure09.png)

1. **Controleer de samen vatting** en klik op **implementeren**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure10.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>Een F5 test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in F5. Werk samen met het ondersteunings [team voor F5-clients](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen op het F5-platform. Gebruikers moeten worden gemaakt en worden geactiveerd voordat u eenmalige aanmelding gebruiken. 

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de F5-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

