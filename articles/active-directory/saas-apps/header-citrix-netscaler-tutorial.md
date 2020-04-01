---
title: 'Zelfstudie: Azure Active Directory single sign-on integration with Citrix NetScaler (header-based authentication) | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding (SSO) tussen Azure Active Directory en Citrix NetScaler met behulp van verificatie op basis van kopteksten.
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
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477953"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Zelfstudie: Azure Active Directory single sign-on integration with Citrix NetScaler (header-based authentication)

In deze zelfstudie leert u hoe Citrix NetScaler worden geïntegreerd met Azure Active Directory (Azure AD). Wanneer u Citrix NetScaler integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Citrix NetScaler.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Citrix NetScaler met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Citrix NetScaler single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. De zelfstudie bevat de volgende scenario's:

* **SP-geïnitieerd** SSO voor Citrix NetScaler

* **Just in time** user provisioning voor Citrix NetScaler

* [Header-gebaseerde verificatie voor Citrix NetScaler](#publish-the-web-server)

* [Kerberos-gebaseerde verificatie voor Citrix NetScaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler toevoegen vanuit de galerij

Als u Citrix NetScaler wilt integreren met Azure AD, voegt u citrix NetScaler eerst toe aan uw lijst met beheerde SaaS-apps vanuit de galerie:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer Azure Active **Directory**in het linkermenu .

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

1. Typ **Citrix NetScaler** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**

1. Selecteer **Citrix NetScaler**in de resultaten en voeg vervolgens de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Azure AD-singlesign-on configureren en testen voor Citrix NetScaler

Azure AD SSO configureren en testen met Citrix NetScaler met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Citrix NetScaler.

Als u Azure AD SSO wilt configureren en testen met Citrix NetScaler, voert u de volgende bouwstenen in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) - zodat uw gebruikers deze functie kunnen gebruiken.

    1. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) - om Azure AD SSO te testen met B.Simon.

    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om B.Simon in staat te stellen Azure AD SSO te gebruiken.

1. [Configureer Citrix NetScaler SSO](#configure-citrix-netscaler-sso) - om de SSO-instellingen aan de toepassingszijde te configureren.

    * [Maak een Citrix NetScaler-testgebruiker](#create-a-citrix-netscaler-test-user) - om een tegenhanger van B.Simon in Citrix NetScaler te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.

1. [Test SSO](#test-sso) - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Voer de volgende stappen uit om Azure AD SSO in te schakelen met behulp van de Azure-portal:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)in het deelvenster **Citrix NetScaler-toepassingsintegratie** onder **Beheren**de optie **Eén aanmelding**.

1. Selecteer **SAML**in het deelvenster **Eén aanmeldingsmethode** selecteren .

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** het pictogram Pen **Bewerken** voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Ga als **u** de toepassing in de door **IDP-modus gestarte** modus wilt configureren:

    1. Voer in het tekstvak **Id** een URL in met het volgende patroon:`https://<Your FQDN>`

    1. Voer in het tekstvak **URL beantwoorden** een URL in met het volgende patroon:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Als u de toepassing wilt configureren in **de door SP geïnitieerde** modus, selecteert u **Extra URL's instellen** en voert u de volgende stap uit:

    * Voer in het tekstvak **URL aanmelding** een URL in met het volgende patroon:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * De URL's die in deze sectie worden gebruikt, zijn geen echte waarden. Werk deze waarden bij met de werkelijke waarden voor id, url van antwoord en aanmeldings-URL. Neem contact op met het [Citrix NetScaler client support team](https://www.citrix.com/contact/technical-support.html) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.
    > * Om SSO in te stellen, moeten de URL's toegankelijk zijn vanaf openbare websites. U moet de firewall of andere beveiligingsinstellingen aan de Citrix NetScaler-kant inschakelen om Azure AD te plaatsen om het token op de geconfigureerde URL te plaatsen.

1. Kopieer de URL in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** voor **url metagegevens van appfederatie,** kopieer de URL en sla deze op in kladblok.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. De Citrix NetScaler-toepassing verwacht dat SAML-beweringen in een specifieke indeling zijn, waardoor u aangepaste attribuuttoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer het pictogram **Bewerken** en wijzig de toewijzingen van kenmerken.

    ![De toewijzing van het SAML-kenmerk bewerken](common/edit-attribute.png)

1. De Citrix NetScaler applicatie verwacht ook nog een paar attributen terug te worden gegeven in de SAML reactie. Voer in het dialoogvenster **Gebruikerskenmerken** onder **Gebruikersclaims**de volgende stappen uit om de SAML-tokenkenmerken toe te voegen zoals weergegeven in de tabel:

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. Voer in het tekstvak **Naam** de kenmerknaam in die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer **Bron**voor **Kenmerk**.

    1. Voer in de lijst **Bronkenmerk** de kenmerkwaarde in die voor die rij wordt weergegeven.

    1. Selecteer **OK**.

    1. Selecteer **Opslaan**.

1. Kopieer in de sectie **Citrix NetScaler instellen** de relevante URL's op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkermenu in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.

1. Selecteer **Nieuwe gebruiker** boven aan het deelvenster.

1. Voer **in gebruikerseigenschappen** de volgende stappen uit:

   1. Voer **Name**voor `B.Simon`Naam .  

   1. Voer **voor**gebruikersnaam _username@companydomain.extension_. Bijvoorbeeld `B.Simon@contoso.com`.

   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer of kopieer de waarde die wordt weergegeven in **Wachtwoord**.

   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u de gebruiker B.Simon in staat Azure SSO te gebruiken door de gebruiker toegang te verlenen tot Citrix NetScaler.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

1. Selecteer **Citrix NetScaler**in de lijst met toepassingen.

1. Selecteer in het app-overzicht onder **Beheren**de optie **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst **Gebruikers.** Kies **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de relevante rol voor de gebruiker in de lijst en kiest u **Selecteer**.

1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-citrix-netscaler-sso"></a>Citrix NetScaler SSO configureren

Selecteer een koppeling voor stappen voor het type verificatie dat u wilt configureren:

- [Citrix NetScaler SSO configureren voor verificatie op basis van kopteksten](#publish-the-web-server)

- [Citrix NetScaler SSO configureren voor verificatie op basis van Kerberos](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>De webserver publiceren 

Ga als lid van het nieuwe bedrijf naar een virtuele server:

1. Selecteer **Services voor** > **het balanceren van** > **verkeersbeheer**.
    
1. Selecteer **Toevoegen**.

    ![Citrix NetScaler-configuratie - deelvenster Services](./media/header-citrix-netscaler-tutorial/web01.png)

1. Stel de volgende waarden in voor de webserver waarop de toepassingen worden uitgevoerd:

   * **Servicenaam**
   * **Server-IP/ Bestaande server**
   * **Protocol**
   * **Poort**

     ![Citrix NetScaler-configuratievenster](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>De load balancer configureren

Ga als u de load balancer configureren:

1. Ga naar **Virtual** > Servers**voor het balanceren van** > het beheer van**het**laden .

1. Selecteer **Toevoegen**.

1. Stel de volgende waarden in zoals beschreven in de volgende schermafbeelding:

    * **Naam**
    * **Protocol**
    * **IP-adres**
    * **Poort**

1. Selecteer **OK**.

    ![Citrix NetScaler-configuratie - deelvenster Basisinstellingen](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>De virtuele server binden

Ga als u de load balancer binden aan de virtuele server:

1. Selecteer in het deelvenster **Services en servicegroepen** de optie **Geen taakverdelingsservicebinding**.

   ![Citrix NetScaler-configuratie - deelvenster Voor het balanceren van virtuele serverservicebinding](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Controleer de instellingen zoals in de volgende schermafbeelding en selecteer **Sluiten**.

   ![Citrix NetScaler-configuratie - Controleer de binding van de virtuele serverservices](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Het certificaat binden

Als u deze service als TLS wilt publiceren, bindt u het servercertificaat en test u de toepassing:

1. Selecteer **onder Certificaat**de optie Geen **servercertificaat**.

   ![Citrix NetScaler-configuratie - venster servercertificaat](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Controleer de instellingen zoals in de volgende schermafbeelding en selecteer **Sluiten**.

   ![Citrix NetScaler-configuratie - Het certificaat verifiëren](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML profiel

Als u het Citrix ADC SAML-profiel wilt configureren, vult u de volgende secties in:

### <a name="create-an-authentication-policy"></a>Een verificatiebeleid maken

Ga als u een verificatiebeleid maken:

1. Ga naar**Authentication** > **verificatiebeleid** **voor verificatiebeleid voor beveiliging** > **AAA – Verificatiebeleid** > **voor toepassingsverkeer** > .

1. Selecteer **Toevoegen**.

1. Voer in het deelvenster **Verificatiebeleid maken** de volgende waarden in of selecteer deze:

    * **Naam:** Voer een naam in voor uw verificatiebeleid.
    * **Actie:** Voer **SAML**in en selecteer **Toevoegen**.
    * **Expressie**: Voer **waar in**.     
    
    ![Citrix NetScaler-configuratie - deelvenster Verificatiebeleid maken](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Selecteer **Maken**.

### <a name="create-an-authentication-saml-server"></a>Een verificatie-SAML-server maken

Als u een VERIFICATIE-SAML-server wilt maken, gaat u naar het deelvenster **VERIFICATIE SAML-server maken** en voert u de volgende stappen uit:

1. Voer voor **Naam**een naam in voor de VERIFICATIE SAML-server.

1. Onder **SAML-metagegevens exporteren:**

   1. Schakel het selectievakje **Metagegevens importeren** in.

   1. Voer de URL van de federatiemetagegevens in van de Azure SAML-gebruikersinterface die u eerder hebt gekopieerd.
    
1. Voer **voor de naam van de uitgever**de relevante URL in.

1. Selecteer **Maken**.

![Citrix NetScaler-configuratie - deelvenster VERIFICATIE SAML-server maken](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Een virtuele verificatieserver maken

Ga als u een virtuele verificatieserver maken:

1.  Ga naar **Beveiliging** > **AAA - Verificatieverificatie** > verificatievan**toepassingsverkeer** > **Authentication** > **Verificatie virtuele servers**.

1.  Selecteer **Toevoegen**en voer de volgende stappen uit:

    1. Voer voor **Naam**een naam in voor de virtuele verificatieserver.

    1. Schakel het selectievakje **Niet-adresseerbaar** in.

    1. Selecteer **SSL**voor **Protocol**.

    1. Selecteer **OK**.

    ![Citrix NetScaler-configuratie - deelvenster Virtuele server verificatie](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>De virtuele verificatieserver configureren om Azure AD te gebruiken

Twee secties wijzigen voor de virtuele verificatieserver:

1.  Selecteer **geen verificatiebeleid**in het deelvenster **Geavanceerd verificatiebeleid** .

    ![Citrix NetScaler-configuratie - deelvenster Geavanceerde verificatiebeleidsregels](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. Selecteer in het deelvenster **Beleidsbinding** het verificatiebeleid en selecteer **Vervolgens Binden**.

    ![Citrix NetScaler-configuratie - deelvenster Beleidsbinding](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Selecteer in het deelvenster **Virtuele servers op basis van formulieren** de optie Geen virtuele server voor **taakverdeling**.

    ![Citrix NetScaler-configuratie - deelvenster Virtuele servers op basis van formulieren](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. Voer **voor verificatie FQDN**een volledig gekwalificeerde domeinnaam (FQDN) in (vereist).

1. Selecteer de virtuele server voor het balanceren van de taak die u wilt beveiligen met Azure AD-verificatie.

1. Selecteer **Binden**.

    ![Citrix NetScaler-configuratie - deelvenster Voor het balanceren van virtuele serverbinding](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Selecteer **Gereed voor gereedmaken** in het deelvenster **Virtuele serverconfiguratie verificatie.**

1. Als u uw wijzigingen wilt verifiëren, gaat u in een browser naar de URL van de toepassing. U moet uw tenantaanmeldingspagina zien in plaats van de niet-geverifieerde toegang die u eerder zou hebben gezien.

    ![Citrix NetScaler-configuratie - Een aanmeldingspagina in een webbrowser](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Citrix NetScaler SSO configureren voor verificatie op basis van kopteksten

### <a name="configure-citrix-adc"></a>Citrix ADC configureren

Als u Citrix ADC wilt configureren voor verificatie op basis van kopteksten, voert u de volgende secties in.

#### <a name="create-a-rewrite-action"></a>Een herschrijfactie maken

1. Ga naar **AppExpert** > **Herschrijfacties** > **opnieuw schrijven**.
 
    ![Citrix NetScaler-configuratie - deelvenster Acties herschrijven](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Selecteer **Toevoegen**en voer de volgende stappen uit:

    1. Voer voor **Naam**een naam in voor de herschrijfactie.

    1. Voer **voor Tekst** **INSERT_HTTP_HEADER**in .

    1. Voer **voor koptekstnaam**een koptekstnaam in (in dit voorbeeld gebruiken we _SecretID)._

    1. Voer **voor Expressie** **aaa in. Gebruiker. ATTRIBUTE("mySecretID")**, waarbij **mySecretID** de Azure AD SAML-claim is die naar Citrix ADC is verzonden.

    1. Selecteer **Maken**.

    ![Citrix NetScaler-configuratie - Deelvenster Actie opnieuw schrijven maken](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Een herschrijfbeleid maken

1.  Ga naar **AppExpert** > **Herschrijven** > **beleid**.
 
    ![Citrix NetScaler-configuratie - deelvenster Beleid herschrijven](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Selecteer **Toevoegen**en voer de volgende stappen uit:

    1. Voer voor **Naam**een naam in voor het herschrijfbeleid.

    1. Selecteer **voor Actie**de herschrijfactie die u in de vorige sectie hebt gemaakt.

    1. Voer **voor Expressie** **true**in.

    1. Selecteer **Maken**.

    ![Citrix NetScaler-configuratie - deelvenster Beleid opnieuw schrijven maken](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Een herschrijfbeleid binden aan een virtuele server

Ga als lid van het nieuwe schrijven van een herschrijfbeleid naar een virtuele server met de GUI:

1. Ga naar **Virtual** > Servers**voor het balanceren van** > het beheer van**het**laden .

1. Selecteer in de lijst met virtuele servers de virtuele server waaraan u het herschrijfbeleid wilt binden en selecteer **Vervolgens Openen**.

1. Selecteer In het deelvenster **Virtuele server laden** onder Geavanceerde **instellingen**de optie **Beleid**. Alle beleidsregels die zijn geconfigureerd voor uw NetScaler-instantie worden weergegeven in de lijst.
 
    ![Citrix NetScaler-configuratie - deelvenster Taakverdeling virtuele server](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler-configuratie - deelvenster Taakverdeling virtuele server](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Schakel het selectievakje in naast de naam van het beleid dat u aan deze virtuele server wilt binden.
 
    ![Citrix NetScaler-configuratie - deelvenster Voor het balanceren van het beleid voor virtuele serververkeer](./media/header-citrix-netscaler-tutorial/header08.png)

1. Ga als een van de **dialoogvenster Type kiezen:**

    1. Selecteer Verkeer voor **Kiezen:** **Selecteer Verkeer**.

    1. Selecteer **Aanvraag** **voor Tekst**kiezen .

    ![Citrix NetScaler-configuratie - dialoogvenster Beleid](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Selecteer **OK**. Een bericht op de statusbalk geeft aan dat het beleid is geconfigureerd.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>De SAML-server wijzigen om kenmerken uit een claim te extraheren

1.  Ga naar **Security** > **AAA - Application Traffic** > **Policies** > **Authentication** > Advanced**Policies** > **Actions** > **Servers**.

1.  Selecteer de juiste verificatie-SAML-server voor de toepassing.
 
    ![Citrix NetScaler-configuratie - deelvenster Verificatie SAML-server configureren](./media/header-citrix-netscaler-tutorial/header09.png)

1. Voer **in** de pijn met kenmerken de SAML-kenmerken in die u wilt extraheren, gescheiden door komma's. In ons voorbeeld voeren `mySecretID`we het kenmerk in.
 
    ![Citrix NetScaler-configuratie - deelvenster Kenmerken](./media/header-citrix-netscaler-tutorial/header10.png)

1. Als u de toegang wilt verifiëren, zoekt u bij de URL in een browser naar het SAML-kenmerk onder **Headers Collection**.

    ![Citrix NetScaler-configuratie - Headers Collection op de URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Een Citrix NetScaler-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Citrix NetScaler. Citrix NetScaler ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie voor u te nemen in deze sectie. Als een gebruiker nog niet bestaat in Citrix NetScaler, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [Citrix NetScaler client support team.](https://www.citrix.com/contact/technical-support.html)

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw Azure AD SSO-configuratie met behulp van het Access-paneel.

Wanneer u de Citrix NetScaler-tegel selecteert in het Access Panel, moet u automatisch worden aangemeld bij de Citrix NetScaler waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Citrix NetScaler uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Citrix NetScaler-aanmelding configureren voor verificatie op basis van Kerberos](citrix-netscaler-tutorial.md)
