---
title: Een on-premises app toevoegen - Application Proxy in Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) heeft een Application Proxy-service waarmee gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. In deze zelf studie wordt uitgelegd hoe u uw omgeving voorbereidt voor gebruik met toepassings proxy. Vervolgens wordt de Azure Portal gebruikt om een on-premises toepassing toe te voegen aan uw Azure AD-Tenant.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8d6297a32cd0f85cf354a93bfdac72cbad9603d
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062839"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in Azure Active Directory

Azure Active Directory (Azure AD) heeft een Application Proxy-service waarmee gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. In deze zelfstudie wordt uw omgeving voorbereid voor gebruik van Application Proxy. Zodra uw omgeving gereed is, gebruikt u de Azure-portal om een on-premises toepassing toe te voegen aan uw Azure AD-tenant.

Deze zelfstudie:

> [!div class="checklist"]
> * Opent poorten voor uitgaand verkeer en verschaft toegang tot specifieke URL 's
> * Installeert de connector op uw Windows-server en registreert deze bij Application Proxy
> * Controleert of de connector juist is geïnstalleerd en geregistreerd
> * Voegt een toepassing toe aan uw Azure AD-tenant
> * Hiermee wordt gecontroleerd of een test gebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

## <a name="before-you-begin"></a>Voordat u begint

Als u een on-premises toepassing wilt toevoegen aan Azure AD, hebt u het volgende nodig:

* Een [Microsoft Azure AD Premium-abonnement](https://azure.microsoft.com/pricing/details/active-directory)
* Een toepassings beheerders account
* Gebruikers identiteiten moeten worden gesynchroniseerd vanuit een on-premises Directory of rechtstreeks worden gemaakt in uw Azure AD-tenants. Met Identiteitssynchronisatie kan Azure AD vooraf gebruikers verifiëren voordat ze toegang krijgen tot toepassingen die zijn gepubliceerd met app proxy en de benodigde gebruikers-id hebben om eenmalige aanmelding (SSO) uit te voeren.

### <a name="windows-server"></a>Windows-server

Als u Application Proxy wilt gebruiken, hebt u een Windows-server nodig waarop Windows Server 2012 R2 of hoger wordt uitgevoerd. U installeert de Application Proxy-connector op de server. Deze connectorserver moet verbinding maken met de Application Proxy-service in Azure en met de on-premises toepassingen die u van plan bent te publiceren.

Om een hoge beschikbaarheid in uw productieomgeving te realiseren wordt aangeraden meer dan één Windows-server te gebruiken. Voor deze zelfstudie is één Windows-server toereikend.

#### <a name="recommendations-for-the-connector-server"></a>Aanbevelingen voor de connector server

1. Plaats de connectorserver fysiek dicht bij de toepassingsservers om de prestaties tussen de connector en de toepassing te optimaliseren. Zie [Overwegingen bij de netwerktopologie](application-proxy-network-topology.md) voor meer informatie.
1. De connector-server en de Web-toepassings servers moeten deel uitmaken van hetzelfde Active Directory domein of dezelfde vertrouwens domeinen. Het hebben van de servers in hetzelfde domein of het vertrouwen van domeinen is een vereiste voor het gebruik van eenmalige aanmelding (SSO) met geïntegreerde Windows-verificatie (IWA) en Kerberos-beperkte delegering (KCD). Als de connectorserver en de webtoepassingsservers zich in verschillende Active Directory-domeinen bevinden, moet u delegatie op basis van resources gebruiken voor eenmalige aanmelding. Zie [KCD voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) voor meer informatie.

> [!WARNING]
> Als u Azure AD-wacht woord voor wachtwoord beveiliging hebt geïmplementeerd, moet u Azure AD-toepassingsproxy-en Azure AD-wachtwoord beveiligings proxy niet samen op dezelfde computer installeren. Azure AD-toepassingsproxy en Azure AD-wachtwoord beveiligings proxy installeren verschillende versies van de Azure AD Connect agent Updater-Service. Deze verschillende versies zijn incompatibel wanneer ze samen op dezelfde computer worden geïnstalleerd.

#### <a name="tls-requirements"></a>TLS-vereisten

Voor de Windows-connectorserver moet TLS 1.2 zijn ingeschakeld voordat u de Application Proxy-connector installeert.

TLS 1.2 inschakelen:

1. Stel de volgende registersleutels in:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw op.

> [!IMPORTANT]
> Voor de beste versleuteling van onze klanten, beperkt de Application proxy-service de toegang tot alleen TLS 1,2-protocollen. Deze wijzigingen werden sinds 31 augustus 2019 geleidelijk uitgerold en zijn van kracht. Zorg ervoor dat alle combi Naties van client-server en browser server zijn bijgewerkt voor het gebruik van TLS 1,2 om de verbinding met de Application proxy-service te onderhouden. Dit zijn clients die uw gebruikers gebruiken om toegang te krijgen tot toepassingen die zijn gepubliceerd via de toepassings proxy. Zie voor bereidingen voor [TLS 1,2 in Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) voor nuttige Naslag informatie en bronnen.

## <a name="prepare-your-on-premises-environment"></a>Bereid uw on-premises omgeving voor

Begin door communicatie met Azure-data centers in te scha kelen om uw omgeving voor te bereiden op Azure AD-toepassingsproxy. Als er een firewall in het pad is, controleert u of deze is geopend. Met een open firewall kan de connector HTTPS-aanvragen (TCP) naar de toepassings proxy maken.

### <a name="open-ports"></a>Poorten openen

Open de volgende poorten voor **uitgaand** verkeer.

   | Poortnummer | Hoe dat wordt gebruikt |
   | --- | --- |
   | 80 | Het downloaden van certificaatintrekkingslijsten (CRL's) tijdens het valideren van het SSL-certificaat |
   | 443 | Alle uitgaande communicatie met de Application Proxy-service |

Als met uw firewall verkeer wordt afgedwongen op basis van de herkomst van gebruikers, open dan ook poorten 80 en 443 voor verkeer dat afkomstig is van Windows-services die als netwerkservice worden uitgevoerd.

### <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan

Sta toegang tot de volgende URL's toe:

| URL | Hoe dat wordt gebruikt |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Communicatie tussen de connector en de Application Proxy-cloudservice |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure gebruikt deze Url's om certificaten te verifiëren. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*. microsoftonline-p.com<br>\*. msauth.net<br>\*. msauthimages.net<br>\*. msecnd.net<br>\*. msftauth.net<br>\*. msftauthimages.net<br>\*. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | De connector gebruikt deze URL's tijdens het registratieproces. |

U kunt verbindingen met \*. msappproxy.net en \*. servicebus.windows.net toestaan als u met uw firewall of proxy een DNS-acceptatie lijst kunt configureren. Als dat niet het geval is, moet u toegang tot de [Azure IP-bereiken en de service Tags-open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519)toestaan. die overigens elke week worden bijgewerkt.

## <a name="install-and-register-a-connector"></a>Een connector installeren en registreren

Als u toepassings proxy wilt gebruiken, installeert u een connector op elke Windows-Server die u gebruikt met de service toepassings proxy. De connector is een agent die de uitgaande verbinding vanaf de on-premises toepassingsservers naar Application Proxy in Azure AD beheert. U kunt een connector installeren op servers waarop ook andere verificatie-agents zijn geïnstalleerd, zoals Azure AD Connect.

De connector installeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als een toepassingsbeheerder van de map die gebruikmaakt van Application Proxy. Als het domein van de tenant bijvoorbeeld contoso.com is, moet de beheerder admin@contoso.com of een andere beheerdersalias in dat domein zijn.
1. Selecteer uw gebruikers naam in de rechter bovenhoek. Controleer of u bent aangemeld bij een map die gebruikmaakt van de toepassings proxy. Als u mappen wilt wijzigen, selecteert u **Directory activeren** en kiest u een map die toepassings proxy gebruikt.
1. Selecteer in het navigatie venster aan de linkerkant **Azure Active Directory**.
1. Selecteer **toepassings proxy**onder **beheren**.
1. Selecteer **Connector service downloaden**.

    ![Connector service downloaden om de service voorwaarden te bekijken](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Lees de servicevoorwaarden. Wanneer u klaar bent, selecteert u **voor waarden accepteren & downloaden**.
1. Selecteer onder aan het venster **uitvoeren** om de connector te installeren. Er wordt een installatiewizard geopend.
1. Volg de instructies in de wizard om de service te installeren. Wanneer u wordt gevraagd de connector te registreren voor de Application Proxy voor uw Azure AD-tenant, geeft u uw gegevens als toepassingsbeheerder op.
    - Voor Internet Explorer (IE) geldt dat als **Verbeterde beveiliging van Internet Explorer** is ingesteld op **Aan**, het registratiescherm niet wordt weergegeven. Volg de instructies in het foutbericht om toegang te krijgen. Zorg ervoor dat **Verbeterde beveiliging van Internet Explorer** is ingesteld op **uit**.

### <a name="general-remarks"></a>Algemene opmerkingen

Als u al een connector hebt geïnstalleerd, voert u een nieuwe installatie uit met de meest recente versie. Zie [toepassings proxy: release geschiedenis](application-proxy-release-version-history.md)van de versie voor meer informatie over eerder uitgebrachte versies en de wijzigingen die ze bevatten.

Als u meer dan één Windows-server voor uw on-premises toepassingen wilt, moet u de connector op elke server installeren en registreren. U kunt de connectors onderverdelen in connectorgroepen. Zie [Connectorgroepen](application-proxy-connector-groups.md) voor meer informatie.

Als uw organisatie proxyservers gebruikt om verbinding met internet te maken, moet u ze configureren voor Application Proxy.  Zie [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md) voor meer informatie. 

Zie [Informatie over Azure AD Application Proxy-connectors](application-proxy-connectors.md) voor meer informatie over connectors, capaciteitsplanning en hoe connectors up-to-date blijven.

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Controleer of de connector juist is geïnstalleerd en geregistreerd

U kunt de Azure-portal of uw Windows-server gebruiken om te bevestigen dat er een nieuwe connector correct is geïnstalleerd.

### <a name="verify-the-installation-through-azure-portal"></a>Controleer de installatie via Azure Portal

Controleren of de connector juist is geïnstalleerd en geregistreerd:

1. Meld u aan bij uw tenantmap in de [Azure-portal](https://portal.azure.com).
1. Selecteer in het navigatie venster links de optie **Azure Active Directory**en selecteer vervolgens **toepassings proxy** in de sectie **beheren** . Al uw connectors en connectorgroepen worden op deze pagina weergegeven.
1. Bekijk een connector om de details ervan te controleren. De connectors moeten standaard worden uitgebreid. Als de connector die u wilt weer geven niet is uitgevouwen, vouwt u de connector uit om de details weer te geven. Een actief groen label geeft aan dat de connector verbinding kan maken met de service. Maar ook al is het label groen, toch kan een netwerkprobleem er nog steeds voor zorgen dat de connector geen berichten ontvangt.

    ![Azure AD-toepassingsproxy-connectors](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Zie probleem met het installeren van [de toepassings proxy connector](application-proxy-connector-installation-problem.md)voor meer informatie over het installeren van een connector.

### <a name="verify-the-installation-through-your-windows-server"></a>De installatie controleren via uw Windows-Server

Controleren of de connector juist is geïnstalleerd en geregistreerd:

1. Open Windows Services Manager door te klikken op de **Windows**-toets en *services.msc* in te voeren.
1. Controleer of de status van de volgende twee services **Wordt uitgevoerd** is.
   - **Micro soft Aad Application proxy connector** maakt connectiviteit mogelijk.
   - **Microsoft AAD Application Proxy Connector Updater** is een automatische updateservice. De updater controleert op nieuwe versies van de connector en werkt de connector bij als dat nodig is.

     ![Connectorservices voor toepassingsproxy - schermafbeelding](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Als de status van de services niet wordt **uitgevoerd**, klikt u met de rechter muisknop om elke service te selecteren en kiest u **starten**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Een on-premises app toevoegen aan Azure AD

Nu u uw omgeving hebt voorbereid en een connector hebt geïnstalleerd, kunt u on-premises toepassingen gaan toevoegen aan Azure AD.  

1. Meld u aan als beheerder in de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Azure Active Directory**in het navigatie venster aan de linkerkant.
3. Selecteer **bedrijfs toepassingen**en selecteer vervolgens **nieuwe toepassing**.
4. Selecteer in de sectie **on-premises toepassingen** de optie **een on-premises toepassing toevoegen**.
5. Geef in de sectie **uw eigen on-premises toepassing toevoegen** de volgende informatie over uw toepassing op:

    | Veld | Beschrijving |
    | :---- | :---------- |
    | **Naam** | De naam van de toepassing die wordt weergegeven in het toegangsvenster en in de Azure-portal. |
    | **Interne URL** | Dit is de URL voor toegang tot de toepassing vanuit uw particuliere netwerk. U kunt voor het publiceren een specifiek pad opgeven op de back-endserver, terwijl de rest van de server ongepubliceerd blijft. Op deze manier kunt u verschillende sites op dezelfde server als verschillende apps publiceren en elk daarvan een eigen naam en toegangsregels geven.<br><br>Als u een pad publiceert, moet u ervoor zorgen dat dit alle benodigde installatiekopieën, scripts en opmaakmodellen voor uw toepassing bevat. Als uw app bijvoorbeeld op https:\//yourapp/app en installatie kopieën gebruikt die zich bevinden op https:\//yourapp/media, moet u https:\//yourapp/als pad publiceren. Deze interne URL hoeft niet de bestemmingspagina te zijn die uw gebruikers te zien krijgen. Zie [Een aangepaste startpagina voor gepubliceerde apps instellen](application-proxy-configure-custom-home-page.md) voor meer informatie. |
    | **Externe URL** | Het adres voor gebruikers om toegang te krijgen tot de app van buiten uw netwerk. Als u het standaarddomein voor Application Proxy niet wilt gebruiken, lees dan de informatie over [aangepaste domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Verificatie vooraf** | De manier waarop gebruikers door Application Proxy worden geverifieerd voordat ze toegang krijgen tot uw toepassing.<br><br>**Azure Active Directory**: de gebruikers worden omgeleid door Application Proxy zodat ze zich kunnen aanmelden met Azure AD. Hierbij worden hun machtigingen geverifieerd voor de map en de toepassing. U wordt aangeraden deze optie als standaard in te stellen, zodat u kunt profiteren van Azure AD-beveiligings functies zoals voorwaardelijke toegang en Multi-Factor Authentication. **Azure Active Directory** is vereist voor het bewaken van de toepassing met Microsoft Cloud Application Security.<br><br>**Passthrough** : gebruikers hoeven zich niet te verifiëren bij Azure AD om toegang te krijgen tot de toepassing. U kunt nog steeds verificatievereisten op de back-end instellen. |
    | **Connectorgroep** | Connectors verwerken de externe toegang tot uw toepassing en met connectorgroepen kunt u connectors en toepassingen indelen per regio, netwerk of doel. Als u nog geen connectorgroepen hebt gemaakt, wordt uw toepassing toegewezen als **Standaard**.<br><br>Als uw toepassing gebruikmaakt van WebSockets om verbinding te maken, moeten alle connectors in de groep versie 1.5.612.0 of hoger hebben.|

6. Configureer zo nodig**aanvullende instellingen**. Voor de meeste toepassingen moet u voor deze instellingen de standaardwaarden behouden. 

    | Veld | Beschrijving |
    | :---- | :---------- |
    | **Toepassingstime-out voor de back-end** | Stel deze waarde alleen in op **Lang** als uw toepassing traag is met verifiëren en verbinding maken. De back-end-time-out van de toepassing heeft standaard een lengte van 85 seconden. Als deze waarde is ingesteld op Long, wordt de time-out voor de back-end verhoogd tot 180 seconden. |
    | **Alleen-HTTP-cookies gebruiken** | Stel deze waarde in op **Ja** om ervoor te zorgen dat Application Proxy-cookies de HTTPOnly-vlag in de HTTP-antwoordheader bevatten. Als u Extern bureaublad-services gebruikt, stelt u deze waarde in op **Nee**.|
    | **Beveiligde cookies gebruiken**| Stel deze waarde in op **Ja** om cookies te verzenden via een beveiligd kanaal, zoals een versleutelde HTTPS-aanvraag.
    | **Permanente cookies gebruiken**| Houd deze waarde ingesteld op **Nee**. Gebruik deze instelling alleen voor toepassingen die geen cookies tussen processen kunnen delen. Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)voor meer informatie over cookie-instellingen.
    | **URL's in headers vertalen** | Laat deze waarde op **Ja** staan, tenzij voor uw toepassing de oorspronkelijke host-header in de verificatieaanvraag moet zijn opgenomen. |
    | **URL's vertalen in de hoofdtekst van de toepassing** | Bewaar deze waarde alleen als u hardcoded HTML-koppelingen naar andere on-premises toepassingen hebt **en geen aangepaste** domeinen gebruikt. Zie [Vertaling koppelen aan Application Proxy](application-proxy-configure-hard-coded-link-translation.md) voor meer informatie.<br><br>Stel deze waarde in op **Ja** als u van plan bent om deze toepassing te bewaken met Microsoft Cloud App Security (MCAS). Zie [realtime toegang tot toepassingen configureren met Microsoft Cloud app Security en Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md)voor meer informatie. |

7. Selecteer **Toevoegen**.

## <a name="test-the-application"></a>De toepassing testen

U kunt nu testen of de toepassing correct is toegevoegd. Hiervoor gaat u in de volgende stappen een gebruikersaccount toevoegen aan de toepassing en u vervolgens proberen aan te melden.

### <a name="add-a-user-for-testing"></a>Een gebruiker toevoegen voor het testen

Controleer voordat u een gebruiker aan de toepassing toevoegt of het gebruikersaccount al machtigingen heeft voor toegang tot de toepassing vanuit het bedrijfsnetwerk.

Een testgebruiker toevoegen:

1. Selecteer **bedrijfs toepassingen**en selecteer vervolgens de toepassing die u wilt testen.
2. Selecteer **aan de slag**en selecteer vervolgens **een gebruiker toewijzen voor testen**.
3. Selecteer **gebruiker toevoegen**onder **gebruikers en groepen**.
4. Onder **toewijzing toevoegen**selecteert u **gebruikers en groepen**. De sectie **gebruiker en groepen** wordt weer gegeven.
5. Kies het account dat u wilt toevoegen.
6. Kies **selecteren**en selecteer vervolgens **toewijzen**.

### <a name="test-the-sign-on"></a>De aanmelding testen

De aanmelding bij de toepassing testen:

1. Navigeer in uw browser naar de externe URL die u hebt geconfigureerd tijdens de stap voor het publiceren. Als het goed is, ziet u nu het startscherm.
1. Meld u aan als de gebruiker die u hebt gemaakt in de vorige sectie.

Zie [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md) voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw on-premises omgeving voorbereid om te werken met Application Proxy en hebt u vervolgens de Application Proxy-connector geïnstalleerd en geregistreerd. Vervolgens hebt u een toepassing toegevoegd aan uw Azure AD-tenant. U hebt ook gecontroleerd of een gebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

U hebt het volgende gedaan:
> [!div class="checklist"]
> * Poorten geopend voor uitgaand verkeer en toegang verschaft tot specifieke URL 's
> * De connector geïnstalleerd op uw Windows-server en deze geregistreerd bij Application Proxy
> * Gecontroleerd of de connector juist is geïnstalleerd en geregistreerd
> * Een toepassing toegevoegd aan uw Azure AD-tenant
> * Gecontroleerd of een test gebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

U kunt de toepassing nu gaan configureren voor eenmalige aanmelding. Gebruik de volgende koppeling om een methode voor eenmalige aanmelding te kiezen en zelf studies voor eenmalige aanmelding te vinden.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
