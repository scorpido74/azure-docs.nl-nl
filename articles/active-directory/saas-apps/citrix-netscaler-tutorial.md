---
title: 'Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met Citrix NetScaler (verificatie op basis van Kerberos) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Citrix NetScaler.
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
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431418"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met Citrix NetScaler (verificatie op basis van Kerberos)

In deze zelf studie leert u hoe u Citrix NetScaler integreert met Azure Active Directory (Azure AD). Wanneer u Citrix NetScaler integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Citrix NetScaler.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Citrix NetScaler met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding voor Citrix NetScaler (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Citrix NetScaler ondersteunt door **SP** GEÏNITIEERDe SSO

* Citrix NetScaler ondersteunt **just-in-time** -gebruikers inrichting

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van een header](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler toevoegen vanuit de galerie

Als u de integratie van Citrix NetScaler wilt configureren in azure AD, moet u Citrix NetScaler van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Citrix NetScaler** in het zoekvak.
1. Selecteer **Citrix NetScaler** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Eenmalige aanmelding van Azure AD voor Citrix NetScaler configureren en testen

Azure AD SSO met Citrix NetScaler configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Citrix NetScaler.

Als u Azure AD SSO wilt configureren en testen met Citrix NetScaler, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Citrix NetScaler SSO configureren](#configure-citrix-netscaler-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. U kunt de **[gebruiker van Citrix NetScaler testen](#create-citrix-netscaler-test-user)** een equivalent van B. Simon in Citrix NetScaler maken die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina Toepassings integratie van **Citrix NetScaler** , zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<<Your FQDN>>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    > [!NOTE]
    > Als u SSO wilt gebruiken, moeten deze Url's toegankelijk zijn vanaf open bare sites. U moet de firewall of andere beveiligings instellingen op NetScaler inschakelen voor enble Azure AD om het token te plaatsen op de geconfigureerde ACS-URL.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekening certificaat** en zoek naar de URL voor de **federatieve meta gegevens**van de app, kopieer de URL en sla deze op in het klad blok.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Citrix NetScaler instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Citrix NetScaler.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Citrix NetScaler**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-citrix-netscaler-sso"></a>Citrix NetScaler SSO configureren

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van een header](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Webserver publiceren 

1. Maak een **virtuele server**.

    a. Ga naar **Traffic Management > load balancing > Services**.
    
    b. Klik op **Add**.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/web01.png)

    c. Geef de details op van de webserver waarop de toepassingen hieronder worden uitgevoerd:
    * **Service naam**
    * **Server-IP/bestaande server**
    * **Protocol**
    * **Poort**

     ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Load Balancer configureren

1. Voer de volgende stappen uit om Load Balancer te configureren:

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/load01.png)

    a. Ga naar **Traffic Management > load balancing > virtuele servers**.

    b. Klik op **Add**.

    c. Geef de details op:

    * **Naam**
    * **Protocol**
    * **IP-adres**
    * **Poort**
    * Klik op **OK**

### <a name="bind-virtual-server"></a>Virtuele server binden

Bind de Load Balancer met de virtuele server die u eerder hebt gemaakt.

![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/bind01.png)

![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Binding certificaat

Omdat we deze service publiceren als SSL-binding van het server certificaat en vervolgens uw toepassing testen.

![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/bind03.png)

![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profiel

### <a name="create-authentication-policy"></a>Verificatie beleid maken

1. Ga naar **beveiliging > AAA: toepassings verkeer > beleids regels > verificatie > verificatie beleid**.

2. Klik op **toevoegen** en geef details op.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/policy01.png)

    a. Naam voor het **verificatie beleid**.

    b. Expressie: **True**.

    c. Actie type- **SAML**.

    d. Actie = klik op **toevoegen** (volg de wizard verificatie van de SAML-server maken).
    
    e. Klik op maken in het **verificatie beleid**.

### <a name="create-authentication-saml-server"></a>SAML-server voor verificatie maken

1. Voer de volgende stappen uit:

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/server01.png)

    a. Geef de **naam**op.

    b. Meta gegevens importeren (Geef de URL voor federatieve meta gegevens op uit de Azure SAML-gebruikers interface die u hierboven hebt gekopieerd).
    
    c. De **naam**van de verlener opgeven.

    d. Klik op **maken**.

### <a name="create-authentication-virtual-server"></a>Virtuele verificatie server maken

1.  Ga naar **beveiliging > AAA-toepassingen verkeer > > verificatie van virtuele servers**.

2.  Klik op **toevoegen** en voer de volgende stappen uit:

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/server02.png)

    a.  Geef een **naam**.

    b.  Kies **niet-adresseerbaar**.

    c.  **SSL**-protocol.

    d.  Klik op **OK**.

    e.  Klik op **Doorgaan**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>De virtuele verificatie server configureren voor het gebruik van Azure AD

U moet de twee secties van de virtuele verificatie server wijzigen.

1.  **Geavanceerde verificatie beleidsregels**

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual01.png)

    a. Selecteer het **verificatie beleid** dat u eerder hebt gemaakt.

    b. Klik op **verbinden**.

      ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual02.png)

2. **Op formulieren gebaseerde virtuele servers**

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  U moet een **FQDN** opgeven, omdat deze wordt afgedwongen door de gebruikers interface.

    b.  Kies de **virtuele-Server Load Balancer** die u wilt beveiligen met Azure AD-verificatie.

    c.  Klik op **verbinden**.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Zorg ervoor dat u ook op **gereed** klikt op de pagina configuratie van virtuele server voor verificatie.

3. Controleer de wijzigingen. Blader naar de URL van de toepassing. U ziet de aanmeldings pagina voor de Tenant in plaats van eerder niet-geverifieerde toegang.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Een Kerberos-delegering-account maken voor Citrix ADC

1. Maak een gebruikers account (in dit voor beeld AppDelegation).

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos01.png)

2. Stel een SPN voor de HOST in voor deze accounts.

    * setspn-S HOST/AppDelegation. IDENTT. werk identt\appdelegation
    
        In het bovenstaande voor beeld

        a. Identt. work (FQDN van domein)

        b. Identt (NetBIOS-naam van domein)

        c. AppDelegation (delegering gebruikers account name)

3. Overdracht voor de webserver configureren 
 
    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >In het bovenstaande voor beeld is de naam van de interne webserver waarop de WIA-site wordt uitgevoerd cweb2

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA KCD (Kerberos delegerings accounts)

1.  Ga naar **Citrix Gateway > AAA KCD-accounts (Kerberos-beperkte overdracht)** .

2.  Klik op toevoegen en geef de onderstaande Details op:

    a.  Geef een **naam**op.

    b.  **Realm**.

    c.  **SPN** -`http/<host/fqdn>@DOMAIN.COM`van service.
    
    >[!NOTE]
    >@DOMAIN.com is verplicht en in hoofd letters.

    d.  Geef een **gedelegeerd gebruikers account**op.

    e.  Controleer het wacht woord voor de gemachtigde gebruiker en geef het **wacht woord**op.

    f.  Klik op **OK**.
 
    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Beleid voor Citrix-verkeer en verkeers profielen

1.  Ga naar **beveiliging > AAA-toepassingen verkeer > beleids regels > Traffic-beleid, profielen en ProfilesTraffic-beleid voor formulier SSO**.

2.  Selecteer **verkeers profielen**.

3.  Klik op **Add**.

4.  Verkeers profiel configureren.

    a.  Geef een **naam**op.

    b.  **Eenmalige aanmelding**opgeven.

    c.  Geef het **KCD-account** op dat u in de vorige stap hebt gemaakt in de vervolg keuzelijst.

    d.  Klik op **OK**.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  Selecteer **Traffic Policy**.

6.  Klik op **Add**.

7.  Traffic-beleid configureren.

    a.  Geef een **naam**op.

    b.  Kies het eerder gemaakte **verkeers profiel** in de vervolg keuzelijst.

    c.  Stel de expressie in op **waar**.

    d.  Klik op **OK**.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>Citrix BIND Traffic-beleid met virtuele servers

Een verkeers beleid binden aan een specifieke virtuele server door gebruik te maken van de gebruikers interface.

* Navigeer naar **Traffic Management > load balancing > virtuele servers**.

* Selecteer in de lijst Details van virtuele servers de **virtuele server** waaraan u het herschrijf beleid wilt koppelen en klik vervolgens op **openen**.

* Selecteer in het dialoog venster virtuele server configureren (taak verdeling) het **tabblad beleid**. Alle beleids regels die zijn geconfigureerd op uw netschaalr, worden weer gegeven in de lijst.
 
    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Schakel het **selectie vakje in** naast de naam van het beleid dat u aan deze virtuele server wilt koppelen.
 
    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Alleen het beleid is gebonden, klikt u op **gereed**.
 
    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Testen met behulp van de geïntegreerde Windows-website.

    ![Configuratie van Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>Een gebruiker voor het testen van Citrix NetScaler maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Citrix NetScaler. Citrix NetScaler ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Citrix NetScaler, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, moet u contact opnemen met het [ondersteunings team van de Citrix NetScaler-client](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Citrix NetScaler in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Citrix NetScaler waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Citrix NetScaler met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor verificatie op basis van een header](header-citrix-netscaler-tutorial.md)
