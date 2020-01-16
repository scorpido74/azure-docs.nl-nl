---
title: 'Zelf studie: de integratie van eenmalige aanmelding met Citrix NetScaler (op Kerberos gebaseerde verificatie) Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding (SSO) tussen Azure Active Directory en Citrix NetScaler door gebruik te maken van Kerberos-verificatie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785242a2cf51571a6d13b2b4691d33e46369bf94
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977912"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Zelf studie: eenmalige aanmelding Azure Active Directory integratie met Citrix NetScaler (verificatie op basis van Kerberos)

In deze zelf studie leert u hoe u Citrix NetScaler integreert met Azure Active Directory (Azure AD). Wanneer u Citrix NetScaler integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Citrix NetScaler.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Citrix NetScaler met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding voor Citrix NetScaler (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. De zelf studie bevat de volgende scenario's:

* Door **SP gestart** SSO voor Citrix NetScaler

* **Just-in-time** -gebruikers inrichten voor Citrix NetScaler

* [Verificatie op basis van Kerberos voor Citrix NetScaler](#publish-the-web-server)

* [Verificatie op basis van een header voor Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler toevoegen vanuit de galerie

Als u Citrix NetScaler met Azure AD wilt integreren, voegt u eerst Citrix NetScaler toe aan uw lijst met beheerde SaaS-apps uit de galerie:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer in het linkermenu **Azure Active Directory**.

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.

1. Voer in de sectie **toevoegen vanuit de galerie** **Citrix NetScaler** in het zoekvak in.

1. Selecteer in de resultaten **Citrix NetScaler**en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Eenmalige aanmelding van Azure AD voor Citrix NetScaler configureren en testen

Azure AD SSO met Citrix NetScaler configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Citrix NetScaler.

Als u Azure AD SSO wilt configureren en testen met Citrix NetScaler, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) -om uw gebruikers in staat te stellen deze functie te gebruiken.

    1. [Maak een Azure AD-test gebruiker](#create-an-azure-ad-test-user) -om Azure AD SSO te testen met B. Simon.

    1. [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user) toe, zodat B. Simon gebruikmaakt van Azure AD SSO.

1. [Configureer Citrix NetScaler SSO](#configure-citrix-netscaler-sso) -om de SSO-instellingen aan de kant van de toepassing te configureren.

    * [Maak een Citrix NetScaler test gebruiker](#create-a-citrix-netscaler-test-user) -om een equivalent van B. Simon te hebben in Citrix NetScaler die is gekoppeld aan de Azure AD-weer gave van de gebruiker.

1. [SSO testen](#test-sso) : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen met behulp van de Azure Portal:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)in het deel venster **Citrix NetScaler** Application Integration onder **beheren**de optie **eenmalige aanmelding**.

1. Selecteer op het deel venster **eenmalige aanmelding selecteren** de optie **SAML**.

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** het pictogram pen **bewerken** voor **eenvoudige configuratie van SAML** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **basis configuratie van SAML** gaat u als volgt te werk om de toepassing te configureren in de **gestarte modus IDP** :

    1. Voer in het tekstvak **id** een URL in die het volgende patroon bevat: `https://<Your FQDN>`

    1. Voer in het tekstvak **antwoord-URL** een URL in die het volgende patroon heeft: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Als u de toepassing in de modus door **SP gestart** wilt configureren, selecteert u **extra url's instellen** en voert u de volgende stap uit:

    * Voer in het tekstvak **URL voor aanmelding** een URL in die het volgende patroon heeft: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * De Url's die in deze sectie worden gebruikt, zijn geen echte waarden. Werk deze waarden bij met de werkelijke waarden voor id, antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteunings team van de Citrix NetScaler-client](https://www.citrix.com/contact/technical-support.html) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.
    > * Als u SSO wilt instellen, moeten de Url's toegankelijk zijn via open bare websites. U moet de firewall of andere beveiligings instellingen inschakelen op de Citrix NetScaler-zijde om enble Azure AD te gebruiken om het token te plaatsen op de geconfigureerde URL.

1. Kopieer de URL in het deel venster **eenmalige aanmelding met SAML** in het gedeelte voor het **SAML-handtekening certificaat** voor de URL voor de **federatieve meta gegevens**van de app en sla deze op in Klad blok.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Citrix NetScaler instellen** kopieert u de relevante url's op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het menu links in het Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.

1. Selecteer **nieuwe gebruiker** boven aan het deel venster.

1. Voer in **gebruikers** eigenschappen de volgende stappen uit:

   1. Voer bij **naam**`B.Simon`in.  

   1. Voer _username@companydomain.extension_ in bij **gebruikers naam**. Bijvoorbeeld `B.Simon@contoso.com`.

   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer of kopieer de waarde die wordt weer gegeven in het **wacht woord**.

   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u de gebruiker B. Simon in voor het gebruik van Azure SSO door de gebruikers toegang te verlenen tot Citrix NetScaler.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.

1. Selecteer in de lijst toepassingen de optie **Citrix NetScaler**.

1. Selecteer in het overzicht van de app onder **beheren**de optie **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst **gebruikers** . Kies **Selecteren**.

1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de relevante rol voor de gebruiker in de lijst en kiest **u selecteren**.

1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-citrix-netscaler-sso"></a>Citrix NetScaler SSO configureren

Selecteer een koppeling voor de stappen voor het soort verificatie dat u wilt configureren:

- [Citrix NetScaler SSO configureren voor verificatie op basis van Kerberos](#publish-the-web-server)

- [Citrix NetScaler SSO configureren voor verificatie op basis van een header](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>De webserver publiceren 

Een virtuele server maken:

1. Selecteer **Traffic Management** > **Load Balancing** > **Services**.
    
1. Selecteer **Toevoegen**.

    ![Configuratie venster voor Citrix NetScaler-Services](./media/citrix-netscaler-tutorial/web01.png)

1. Stel de volgende waarden in voor de webserver waarop de toepassingen worden uitgevoerd:

   * **Service naam**
   * **Server-IP/bestaande server**
   * **Protocol**
   * **Poort**

### <a name="configure-the-load-balancer"></a>De load balancer configureren

De load balancer configureren:

1. Ga naar **Traffic Management** > **Load Balancing** > **virtuele servers**.

1. Selecteer **Toevoegen**.

1. Stel de volgende waarden in zoals beschreven in de volgende scherm afbeelding:

    * **Naam**
    * **Protocol**
    * **IP-adres**
    * **Poort**

1. Selecteer **OK**.

    ![Configuratie van Citrix NetScaler-deel venster basis instellingen](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>De virtuele server koppelen

De load balancer binden aan de virtuele server:

1. Selecteer in het deel venster **Services en service groepen** **geen taak verdeling virtuele-server service binding**.

   ![Configuratie van het deel venster Service binding voor het verdelen van de virtuele server voor Citrix NetScaler](./media/citrix-netscaler-tutorial/bind01.png)

1. Controleer de instellingen, zoals weer gegeven in de volgende scherm afbeelding, en selecteer vervolgens **sluiten**.

   ![Configuratie van Citrix NetScaler-de binding van de Virtual Server-services controleren](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Het certificaat binden

Als u deze service als SSL wilt publiceren, bindt u het server certificaat en test u de toepassing:

1. Onder **certificaat**selecteert u **geen server certificaat**.

   ![Configuratie van Citrix NetScaler-server certificaat deel venster](./media/citrix-netscaler-tutorial/bind03.png)

1. Controleer de instellingen, zoals weer gegeven in de volgende scherm afbeelding, en selecteer vervolgens **sluiten**.

   ![Configuratie van Citrix NetScaler-het certificaat controleren](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profiel

Als u het Citrix ADC SAML-profiel wilt configureren, voltooit u de volgende secties.

### <a name="create-an-authentication-policy"></a>Een verificatie beleid maken

Een verificatie beleid maken:

1. Ga naar **beveiliging** > **AAA: toepassings verkeer** > **beleids regels** > **verificatie** > **verificatie beleid**.

1. Selecteer **Toevoegen**.

1. Typ of selecteer de volgende waarden in het deel venster **verificatie beleid maken** :

    * **Naam**: Voer een naam in voor het verificatie beleid.
    * **Actie**: Voer **SAML**in en selecteer vervolgens **toevoegen**.
    * **Expressie**: Voer **waar**in.     
    
    ![Configuratie van Citrix NetScaler-deel venster authenticatie beleid maken](./media/citrix-netscaler-tutorial/policy01.png)

1. Selecteer **Maken**.

### <a name="create-an-authentication-saml-server"></a>Een SAML-verificatie server maken

Als u een SAML-server voor verificatie wilt maken, gaat u naar het deel venster **authenticatie-SAML-server maken** en voert u de volgende stappen uit:

1. Voer bij **naam**een naam in voor de SAML-verificatie server.

1. Onder **SAML-meta gegevens exporteren**:

   1. Schakel het selectie vakje **meta gegevens importeren** in.

   1. Voer de URL voor federatieve meta gegevens in via de Azure SAML-gebruikers interface die u eerder hebt gekopieerd.
    
1. Voer de relevante URL in voor de naam van de **Uitgever**.

1. Selecteer **Maken**.

![Configuratie van Citrix NetScaler-het deel venster authenticatie-SAML-server maken](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Een virtuele-verificatie server maken

Een virtuele-verificatie server maken:

1.  Ga naar **beveiliging** > **AAA-toepassings verkeer** > **beleids regels** > **verificatie** > **virtuele server voor verificatie**.

1.  Selecteer **toevoegen**en voer de volgende stappen uit:

    1. Voer bij **naam**een naam in voor de virtuele-verificatie server.

    1. Schakel het selectie vakje **niet-adresseerbaar** in.

    1. Voor **protocol**selecteert u **SSL**.

    1. Selecteer **OK**.
    
1. Selecteer **Doorgaan**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>De virtuele verificatie server configureren voor het gebruik van Azure AD

Wijzig twee secties voor de virtuele verificatie server:

1.  Selecteer **geen verificatie beleid**in het deel venster **geavanceerde verificatie beleidsregels** .

    ![Configuratie van Citrix NetScaler-deel venster Geavanceerde verificatie beleidsregels](./media/citrix-netscaler-tutorial/virtual01.png)

1. Selecteer in het deel venster **beleids binding** het verificatie beleid en selecteer vervolgens **binding**.

    ![Configuratie van Citrix NetScaler-deel venster beleids binding](./media/citrix-netscaler-tutorial/virtual02.png)

1. Selecteer in het deel venster **op formulieren gebaseerde virtuele servers** **geen taak verdeling virtuele server**.

    ![Configuratie van Citrix NetScaler-het deel venster virtuele servers op basis van een formulier](./media/citrix-netscaler-tutorial/virtual03.png)

1. Voer voor **verificatie-FQDN**een Fully QUALIFIED domain name (FQDN) in (vereist).

1. Selecteer de virtuele netwerk taakverdelings server die u wilt beveiligen met Azure AD-verificatie.

1. Selecteer **binden**.

    ![Configuratie van het deel venster binding van de virtuele server voor Citrix NetScaler-taak verdeling](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Zorg ervoor dat u **gereed** selecteert in het deel venster configuratie van de **virtuele-verificatie server** .

1. Ga in een browser naar de URL van de toepassing om uw wijzigingen te verifiëren. De aanmeldings pagina van uw Tenant wordt weer gegeven in plaats van de niet-geverifieerde toegang die u eerder hebt gezien.

    ![Configuratie van Citrix NetScaler-een aanmeldings pagina in een webbrowser](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Citrix NetScaler SSO configureren voor verificatie op basis van Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Een Kerberos-delegering-account maken voor Citrix ADC

1. Een gebruikers account maken (in dit voor beeld gebruiken we _AppDelegation_).

    ![Configuratie venster voor Citrix NetScaler-eigenschappen](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Stel een SPN voor de HOST in voor dit account. 

    Voorbeeld: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    In dit voorbeeld:

    * `IDENTT.WORK` is de FQDN van het domein.
    * `identt` is de NetBIOS-naam van het domein.
    * `appdelegation` is de naam van het delegerings gebruikers account.

1. Configureer de overdracht voor de webserver, zoals wordt weer gegeven in de volgende scherm afbeelding:
 
    ![Configuratie van Citrix NetScaler-delegering onder deel venster Eigenschappen](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > In het scherm voor beeld van de naam van de interne webserver met de WIA-site (Windows Integrated Authentication) is _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (Kerberos delegerings accounts)

De Citrix NetScaler AAA KCD-account configureren:

1.  Ga naar **Citrix Gateway** > **AAA KCD-accounts (Kerberos-beperkte overdracht)** .

1.  Selecteer **toevoegen**en typ of selecteer de volgende waarden:

    * **Naam**: Voer een naam in voor het KCD-account.

    * **Realm**: Voer in hoofd letters het domein en de uitbrei ding in.

    * **SPN van service**: `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` is vereist en moet een hoofd letter zijn. Voorbeeld: `http/cweb2@IDENTT.WORK`.

    * **Gedelegeerde gebruiker**: Voer de gedelegeerde gebruikers naam in.

    * Schakel het selectie vakje **wacht woord voor gedelegeerde gebruiker** in en voer een wacht woord in en bevestig dit.

1. Selecteer **OK**.
 
    ![Citrix NetScaler-configuratie-KCD-account paneel configureren](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Beleid voor Citrix-verkeer en verkeers profielen

Het Citrix-verkeer beleid en het verkeers profiel configureren:

1.  Ga naar **beveiliging** > **AAA-toepassingen verkeer** > **beleids** regels > Traffic-beleid **, profielen en ProfilesTraffic-beleid voor formulier SSO**.

1.  Selecteer **verkeers profielen**.

1.  Selecteer **Toevoegen**.

1.  Als u een verkeers profiel wilt configureren, voert u de volgende waarden in of selecteert u deze.

    * **Naam**: Voer een naam in voor het verkeers profiel.

    * **Eenmalige aanmelding**: selecteren **op**.

    * **KCD-account**: Selecteer het KCD-account dat u in de voor gaande sectie hebt gemaakt.

1. Selecteer **OK**.

    ![Configuratie van Citrix NetScaler-deel venster verkeers profiel configureren](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Selecteer **Traffic Policy**.

1.  Selecteer **Toevoegen**.

1.  Als u een verkeers beleid wilt configureren, voert u de volgende waarden in of selecteert u deze:

    * **Naam**: Voer een naam in voor het verkeers beleid.

    * **Profiel**: Selecteer het verkeers profiel dat u in de voor gaande sectie hebt gemaakt.

    * **Expressie**: Voer **waar**in.

1. Selecteer **OK**.

    ![Configuratie van Citrix NetScaler-het deel venster verkeers beleid configureren](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Een verkeers beleid binden aan een virtuele server in Citrix

Een verkeers beleid binden aan een virtuele server met behulp van de gebruikers interface:

1. Ga naar **Traffic Management** > **Load Balancing** > **virtuele servers**.

1. Selecteer in de lijst met virtuele servers de virtuele server waaraan u het herschrijf beleid wilt koppelen en selecteer vervolgens **openen**.

1. Selecteer op het deel venster **taak verdeling voor virtuele server** onder **Geavanceerde instellingen**de optie **beleid**. Alle beleids regels die zijn geconfigureerd voor uw NetScaler-exemplaar, worden weer gegeven in de lijst.
 
    ![Configuratie venster voor Citrix NetScaler-taak verdeling van virtuele server](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Het dialoog venster Citrix NetScaler-configuratie-beleid](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Schakel het selectie vakje in naast de naam van het beleid dat u aan deze virtuele server wilt koppelen.
 
    ![Configuratie venster voor het binden van het verkeer van de virtuele server voor Citrix NetScaler-netwerk taakverdeling](./media/citrix-netscaler-tutorial/kerberos09.png)

1. In het dialoog venster **type kiezen** :

    1. Selecteer voor **beleid kiezen**de optie **verkeer**.

    1. Selecteer **aanvraag**bij **type kiezen**.

    ![Configuratie van Citrix NetScaler-deel venster type](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Wanneer het beleid is gebonden, selecteert u **gereed**.
 
    ![Configuratie van Citrix NetScaler-beleids venster](./media/citrix-netscaler-tutorial/kerberos10.png)

1. De binding testen met behulp van de WIA-website.

    ![Configuratie van Citrix NetScaler-een test pagina in een webbrowser](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Een Citrix NetScaler-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Citrix NetScaler. Citrix NetScaler ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. U kunt in deze sectie geen actie ondernemen. Als een gebruiker nog niet bestaat in Citrix NetScaler, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van de Citrix NetScaler-client](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>SSO testen 

In deze sectie test u uw Azure AD SSO-configuratie met behulp van het toegangs venster.

Wanneer u de tegel Citrix NetScaler selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Citrix NetScaler waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Citrix NetScaler met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van een header](header-citrix-netscaler-tutorial.md)
