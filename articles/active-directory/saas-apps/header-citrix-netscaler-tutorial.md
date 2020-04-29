---
title: 'Zelf studie: de integratie van eenmalige aanmelding met Citrix NetScaler (verificatie op basis van headers) Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding (SSO) tussen Azure Active Directory en Citrix NetScaler door gebruik te maken van verificatie op basis van een header.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ea6824975d0cb3f4b909db41188c490bbba6d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80477953"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Zelf studie: eenmalige aanmelding Azure Active Directory integratie met Citrix NetScaler (verificatie op basis van een header)

In deze zelf studie leert u hoe u Citrix NetScaler integreert met Azure Active Directory (Azure AD). Wanneer u Citrix NetScaler integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Citrix NetScaler.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Citrix NetScaler met hun Azure AD-accounts.
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

* [Verificatie op basis van een header voor Citrix NetScaler](#publish-the-web-server)

* [Verificatie op basis van Kerberos voor Citrix NetScaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler toevoegen vanuit de galerie

Als u Citrix NetScaler met Azure AD wilt integreren, voegt u eerst Citrix NetScaler toe aan uw lijst met beheerde SaaS-apps uit de galerie:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.

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

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen met behulp van de Azure Portal:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)in het deel venster **Citrix NetScaler** Application Integration onder **beheren**de optie **eenmalige aanmelding**.

1. Selecteer op het deel venster **eenmalige aanmelding selecteren** de optie **SAML**.

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** het pictogram pen **bewerken** voor **eenvoudige configuratie van SAML** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **basis configuratie van SAML** gaat u als volgt te werk om de toepassing te configureren in de **gestarte modus IDP** :

    1. Voer in het tekstvak **id** een URL in die het volgende patroon heeft:`https://<Your FQDN>`

    1. Voer in het tekstvak **antwoord-URL** een URL in die het volgende patroon heeft:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Als u de toepassing in de modus door **SP gestart** wilt configureren, selecteert u **extra url's instellen** en voert u de volgende stap uit:

    * Voer in het tekstvak **URL voor aanmelding** een URL in die het volgende patroon heeft:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * De Url's die in deze sectie worden gebruikt, zijn geen echte waarden. Werk deze waarden bij met de werkelijke waarden voor id, antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteunings team van de Citrix NetScaler-client](https://www.citrix.com/contact/technical-support.html) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.
    > * Als u SSO wilt instellen, moeten de Url's toegankelijk zijn via open bare websites. U moet de firewall of andere beveiligings instellingen inschakelen op de Citrix NetScaler-zijde om enble Azure AD te gebruiken om het token te plaatsen op de geconfigureerde URL.

1. Kopieer de URL in het deel venster **eenmalige aanmelding met SAML** in het gedeelte voor het **SAML-handtekening certificaat** voor de URL voor de **federatieve meta gegevens**van de app en sla deze op in Klad blok.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. De Citrix NetScaler-toepassing verwacht dat de SAML-beweringen een specifieke indeling hebben. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer het **bewerkings** pictogram en wijzig de kenmerk toewijzingen.

    ![De SAML-kenmerk toewijzing bewerken](common/edit-attribute.png)

1. De Citrix NetScaler-toepassing verwacht ook nog enkele kenmerken die opnieuw moeten worden door gegeven in het SAML-antwoord. In het dialoog venster **gebruikers kenmerken** , onder **gebruikers claims**, voert u de volgende stappen uit om de SAML-token kenmerken toe te voegen, zoals weer gegeven in de tabel:

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. Voer in het tekstvak **naam** de naam in van het kenmerk dat voor die rij wordt weer gegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer **bron**bij **kenmerk**.

    1. Voer in de lijst **bron kenmerk** de kenmerk waarde in die voor die rij wordt weer gegeven.

    1. Selecteer **OK**.

    1. Selecteer **Opslaan**.

1. In de sectie **Citrix NetScaler instellen** kopieert u de relevante url's op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het menu links in het Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.

1. Selecteer **nieuwe gebruiker** boven aan het deel venster.

1. Voer in **gebruikers** eigenschappen de volgende stappen uit:

   1. Voer **Name** `B.Simon`in bij naam.  

   1. Voer _username@companydomain.extension_in bij **gebruikers naam**. Bijvoorbeeld `B.Simon@contoso.com`.

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

- [Citrix NetScaler SSO configureren voor verificatie op basis van een header](#publish-the-web-server)

- [Citrix NetScaler SSO configureren voor verificatie op basis van Kerberos](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>De webserver publiceren 

Een virtuele server maken:

1. Selecteer**taakverdelings** > **Services**van **Traffic Management** > .
    
1. Selecteer **Toevoegen**.

    ![Configuratie venster voor Citrix NetScaler-Services](./media/header-citrix-netscaler-tutorial/web01.png)

1. Stel de volgende waarden in voor de webserver waarop de toepassingen worden uitgevoerd:

   * **Servicenaam**
   * **Server-IP/bestaande server**
   * **Protocolsubstatus**
   * **Importeer**

     ![Configuratie venster voor Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>De load balancer configureren

De load balancer configureren:

1. Ga naar de**virtuele servers**met**taak verdeling** > van **verkeers beheer** > .

1. Selecteer **Toevoegen**.

1. Stel de volgende waarden in zoals beschreven in de volgende scherm afbeelding:

    * **Naam**
    * **Protocolsubstatus**
    * **IP-adres**
    * **Importeer**

1. Selecteer **OK**.

    ![Configuratie van Citrix NetScaler-deel venster basis instellingen](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>De virtuele server koppelen

De load balancer binden aan de virtuele server:

1. Selecteer in het deel venster **Services en service groepen** **geen taak verdeling virtuele-server service binding**.

   ![Configuratie van het deel venster Service binding voor het verdelen van de virtuele server voor Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Controleer de instellingen, zoals weer gegeven in de volgende scherm afbeelding, en selecteer vervolgens **sluiten**.

   ![Configuratie van Citrix NetScaler-de binding van de Virtual Server-services controleren](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Het certificaat binden

Als u deze service als TLS wilt publiceren, bindt u het server certificaat en test u de toepassing:

1. Onder **certificaat**selecteert u **geen server certificaat**.

   ![Configuratie van Citrix NetScaler-server certificaat deel venster](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Controleer de instellingen, zoals weer gegeven in de volgende scherm afbeelding, en selecteer vervolgens **sluiten**.

   ![Configuratie van Citrix NetScaler-het certificaat controleren](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profiel

Als u het Citrix ADC SAML-profiel wilt configureren, voert u de volgende secties uit:

### <a name="create-an-authentication-policy"></a>Een verificatie beleid maken

Een verificatie beleid maken:

1. Ga naar **beveiliging** > **AAA-** > **beleid** > voor**verificatie** > van het toepassings verkeer authenticatie**beleid**.

1. Selecteer **Toevoegen**.

1. Typ of selecteer de volgende waarden in het deel venster **verificatie beleid maken** :

    * **Naam**: Voer een naam in voor het verificatie beleid.
    * **Actie**: Voer **SAML**in en selecteer vervolgens **toevoegen**.
    * **Expressie**: Voer **waar**in.     
    
    ![Configuratie van Citrix NetScaler-deel venster authenticatie beleid maken](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Selecteer **Maken**.

### <a name="create-an-authentication-saml-server"></a>Een SAML-verificatie server maken

Als u een SAML-server voor verificatie wilt maken, gaat u naar het deel venster **authenticatie-SAML-server maken** en voert u de volgende stappen uit:

1. Voer bij **naam**een naam in voor de SAML-verificatie server.

1. Onder **SAML-meta gegevens exporteren**:

   1. Schakel het selectie vakje **meta gegevens importeren** in.

   1. Voer de URL voor federatieve meta gegevens in via de Azure SAML-gebruikers interface die u eerder hebt gekopieerd.
    
1. Voer de relevante URL in voor de naam van de **Uitgever**.

1. Selecteer **Maken**.

![Configuratie van Citrix NetScaler-het deel venster authenticatie-SAML-server maken](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Een virtuele-verificatie server maken

Een virtuele-verificatie server maken:

1.  Ga naar **beveiliging** > **AAA-** > **beleids regels** > voor**verificatie** > van toepassings verkeer**virtuele servers**.

1.  Selecteer **toevoegen**en voer de volgende stappen uit:

    1. Voer bij **naam**een naam in voor de virtuele-verificatie server.

    1. Schakel het selectie vakje **niet-adresseerbaar** in.

    1. Voor **protocol**selecteert u **SSL**.

    1. Selecteer **OK**.

    ![Het deel venster configuratie van Citrix NetScaler-verificatie van de virtuele server](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>De virtuele verificatie server configureren voor het gebruik van Azure AD

Wijzig twee secties voor de virtuele verificatie server:

1.  Selecteer **geen verificatie beleid**in het deel venster **geavanceerde verificatie beleidsregels** .

    ![Configuratie van Citrix NetScaler-deel venster Geavanceerde verificatie beleidsregels](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. Selecteer in het deel venster **beleids binding** het verificatie beleid en selecteer vervolgens **binding**.

    ![Configuratie van Citrix NetScaler-deel venster beleids binding](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Selecteer in het deel venster **op formulieren gebaseerde virtuele servers** **geen taak verdeling virtuele server**.

    ![Configuratie van Citrix NetScaler-het deel venster virtuele servers op basis van een formulier](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. Voer voor **verificatie-FQDN**een Fully QUALIFIED domain name (FQDN) in (vereist).

1. Selecteer de virtuele netwerk taakverdelings server die u wilt beveiligen met Azure AD-verificatie.

1. Selecteer **binden**.

    ![Configuratie van het deel venster binding van de virtuele server voor Citrix NetScaler-taak verdeling](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Zorg ervoor dat u **gereed** selecteert in het deel venster configuratie van de **virtuele-verificatie server** .

1. Ga in een browser naar de URL van de toepassing om uw wijzigingen te verifiëren. De aanmeldings pagina van uw Tenant wordt weer gegeven in plaats van de niet-geverifieerde toegang die u eerder hebt gezien.

    ![Configuratie van Citrix NetScaler-een aanmeldings pagina in een webbrowser](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Citrix NetScaler SSO configureren voor verificatie op basis van een header

### <a name="configure-citrix-adc"></a>Citrix ADC configureren

Als u Citrix ADC wilt configureren voor verificatie op basis van een header, voltooit u de volgende secties.

#### <a name="create-a-rewrite-action"></a>Een herschrijf actie maken

1. Ga naar **AppExpert** > **Rewrite** > herschrijf**acties**herschrijven.
 
    ![Configuratie van Citrix NetScaler-deel venster acties opnieuw schrijven](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Selecteer **toevoegen**en voer de volgende stappen uit:

    1. Voer bij **naam**een naam in voor de herschrijf actie.

    1. Voer bij **Type** **INSERT_HTTP_HEADER**in.

    1. Voer bij **header naam**een naam in voor de header (in dit voor beeld gebruiken we _SecretID_).

    1. Voer voor **expressie** **AAA in. Gebruiker. KENMERK ("mySecretID")**, waarbij **MySecretID** de Azure AD SAML-claim is die naar Citrix ADC is verzonden.

    1. Selecteer **Maken**.

    ![Configuratie van Citrix NetScaler-actie deel venster herschrijf bewerking maken](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Een herschrijf beleid maken

1.  Ga naar **AppExpert** > **Rewrite** > herschrijf**beleid**herschrijven.
 
    ![Configuratie van Citrix NetScaler-het deel venster beleids regels opnieuw schrijven](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Selecteer **toevoegen**en voer de volgende stappen uit:

    1. Voer bij **naam**een naam in voor het beleid voor herschrijven.

    1. Selecteer bij **actie**de actie herschrijven die u in de voor gaande sectie hebt gemaakt.

    1. Voer bij **expressie** **waar**in.

    1. Selecteer **Maken**.

    ![Configuratie van Citrix NetScaler-deel venster herschrijf beleid maken](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Een beleid voor herschrijven binden aan een virtuele server

Een beleid voor herschrijven koppelen aan een virtuele server met behulp van de gebruikers interface:

1. Ga naar de**virtuele servers**met**taak verdeling** > van **verkeers beheer** > .

1. Selecteer in de lijst met virtuele servers de virtuele server waaraan u het herschrijf beleid wilt koppelen en selecteer vervolgens **openen**.

1. Selecteer op het deel venster **taak verdeling voor virtuele server** onder **Geavanceerde instellingen**de optie **beleid**. Alle beleids regels die zijn geconfigureerd voor uw NetScaler-exemplaar, worden weer gegeven in de lijst.
 
    ![Configuratie venster voor Citrix NetScaler-taak verdeling van virtuele server](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Configuratie venster voor Citrix NetScaler-taak verdeling van virtuele server](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Schakel het selectie vakje in naast de naam van het beleid dat u aan deze virtuele server wilt koppelen.
 
    ![Configuratie venster voor het binden van het verkeer van de virtuele server voor Citrix NetScaler-netwerk taakverdeling](./media/header-citrix-netscaler-tutorial/header08.png)

1. In het dialoog venster **type kiezen** :

    1. Selecteer voor **beleid kiezen**de optie **verkeer**.

    1. Selecteer **aanvraag**bij **type kiezen**.

    ![Het dialoog venster Citrix NetScaler-configuratie-beleid](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Selecteer **OK**. Een bericht in de status balk geeft aan dat het beleid is geconfigureerd.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>De SAML-server wijzigen om kenmerken te extra heren uit een claim

1.  Ga naar **beveiliging** > **AAA-toepassings Traffic** > **beleid** > **verificatie** > **Geavanceerde beleids** > regels**acties** > **servers**.

1.  Selecteer de juiste verificatie-SAML-server voor de toepassing.
 
    ![Configuratie van Citrix NetScaler-configureren van het SAML-server paneel](./media/header-citrix-netscaler-tutorial/header09.png)

1. Voer in het knel punt **kenmerken** de SAML-kenmerken in die u wilt extra heren, gescheiden door komma's. In ons voor beeld voeren we het kenmerk `mySecretID`in.
 
    ![Configuratie venster voor Citrix NetScaler-kenmerken](./media/header-citrix-netscaler-tutorial/header10.png)

1. Als u de toegang wilt controleren, zoekt u op de URL in een browser naar het SAML-kenmerk onder **headers verzameling**.

    ![Configuratie van de verzameling Citrix NetScaler-headers op de URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Een Citrix NetScaler-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Citrix NetScaler. Citrix NetScaler ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. U kunt in deze sectie geen actie ondernemen. Als een gebruiker nog niet bestaat in Citrix NetScaler, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van de Citrix NetScaler-client](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>SSO testen 

In deze sectie test u uw Azure AD SSO-configuratie met behulp van het toegangs venster.

Wanneer u de tegel Citrix NetScaler selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Citrix NetScaler waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Citrix NetScaler met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van Kerberos](citrix-netscaler-tutorial.md)
