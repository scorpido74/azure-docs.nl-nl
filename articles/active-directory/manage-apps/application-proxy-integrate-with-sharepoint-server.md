---
title: Externe toegang tot SharePoint met Azure AD-toepassingsproxy inschakelen | Microsoft Docs
description: Bevat de basisinformatie over het integreren van een on-premises SharePoint-server met Azure AD-toepassingsproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1265341ecfdb7f418ea89bb0ec848a20c6b430cd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127686"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Externe toegang tot SharePoint met Azure AD-toepassingsproxy inschakelen

In dit artikel wordt beschreven hoe u een on-premises SharePoint-server integreren met Azure Active Directory (Azure AD) Application Proxy.

Voor externe toegang tot SharePoint met Azure AD-toepassingsproxy, volgt u de secties in dit artikel stap voor stap.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u SharePoint 2013 of hoger al in uw omgeving hebt. Bovendien kunt u overwegen de volgende vereisten:

* SharePoint bevat systeemeigen ondersteuning van Kerberos. Daarom gebruikers die toegang hebben tot interne websites op afstand via Azure AD Application Proxy kunnen wordt ervan uitgegaan dat een ervaring voor eenmalige aanmelding (SSO).
* Dit scenario omvat configuratiewijzigingen naar uw SharePoint-server. We raden u aan met behulp van een faseringsomgeving. Op deze manier u kunt updates aanbrengen in uw testserver eerst, en vervolgens een testcyclus voordat u doorgaat naar de productie te vergemakkelijken.
* SSL op de gepubliceerde URL is vereist. SSL is ook vereist op de interne URL om ervoor te zorgen dat koppelingen correct worden verzonden/toegewezen.

> [!NOTE]
> Gebruik indien mogelijk aangepaste domeinen als best practice. Met een aangepast domein kunt u dezelfde URL configureren voor zowel de interne URL als de externe URL. Vervolgens kan dezelfde koppeling worden gebruikt voor toegang tot de toepassing vanaf binnen of buiten uw netwerk. Deze configuratie optimaliseert de ervaring voor gebruikers en andere toepassingen die toegang nodig hebben tot uw toepassing. Meer informatie over het [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md).

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Stap 1: Beperkte Kerberos-delegering (KCD) configureren

Voor on-premises toepassingen die gebruikmaken van Windows-verificatie, kunt u eenmalige aanmelding (SSO) met het Kerberos-verificatieprotocol en een functie met de naam van Kerberos-beperkte delegatie (KCD) bereiken. KCD, wanneer geconfigureerd, kunt de Application Proxy-connector op een Windows-token voor een gebruiker, zelfs als de gebruiker nog niet is aangemeld bij Windows rechtstreeks. Zie voor meer informatie over KCD [Kerberos-beperkte overdracht overzicht](https://technet.microsoft.com/library/jj553400.aspx).

Als u KCD instelt voor een SharePoint-server, gebruik de procedures in de volgende sequentiële secties:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Zorg ervoor dat de share point-webtoepassing wordt uitgevoerd onder een domein account

Controleer eerst of de share point-webtoepassing wordt uitgevoerd onder een domein account, niet lokaal systeem, lokale service of netwerk service. Dit kunt u doen om Spn's (Service Principal Names) aan dit account toe te voegen. SPN's zijn hoe het Kerberos-protocol wordt aangegeven met verschillende services. En u moet het account later naar de KCD configureren.

> [!NOTE]
> U moet beschikken over een eerder gemaakte Azure AD-account voor de service. Het is raadzaam dat u voor een automatische wachtwoordwijziging toestaan. Zie [Automatische wachtwoord wijziging configureren in share point](https://technet.microsoft.com/library/ff724280.aspx)voor meer informatie over de volledige set stappen en het oplossen van problemen.

Om ervoor te zorgen dat uw sites worden uitgevoerd onder een opgegeven service-account, moet u de volgende stappen uitvoeren:

1. Open de site **Centraal beheer van share point** .
1. Ga naar **Security** en selecteer **service-accounts configureren**.
1. Selecteer **Web-groep van toepassingen - SharePoint - 80**. De opties mogelijk enigszins verschillen op basis van de naam van uw web-groep, of als de webpool voor SSL wordt standaard gebruikt.

   ![Opties voor het configureren van een service-account](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Als **selecteert u een account voor dit onderdeel** veld is ingesteld op **lokale Service** of **netwerkservice**, moet u een account maken. Zo niet, u klaar bent en u kunt verplaatsen naar de volgende sectie.
1. Selecteer **nieuw beheerd account registreren**. Nadat uw account is gemaakt, moet u instellen **toepassingen** voordat u het account kunt gebruiken.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Een service-principal-naam voor de SharePoint-serviceaccount instellen

Voordat u KCD configureert, moet u het volgende doen:

* Identificeer het domein account waarop de share point-webtoepassing wordt uitgevoerd en die Azure AD-proxy beschikbaar maakt.
* Kies een interne URL die wordt geconfigureerd in zowel Azure AD proxy als share point. Deze interne URL mag niet al worden gebruikt in de webtoepassing en wordt nooit weer gegeven in de webbrowser.

Ervan uitgaande dat de gekozen interne <https://sharepoint>URL is, is de SPN:

```
HTTP/SharePoint
```

> [!NOTE]
> Volg deze aanbevelingen voor de interne URL:
> * HTTPS gebruiken
> * Geen aangepaste poorten gebruiken
> * Maak in de DNS een host (A) die verwijst naar share point WFE (of load balancer) en niet een alias (CName)

Als u deze SPN wilt registreren, voert u de volgende opdracht uit vanaf de opdracht prompt als beheerder van het domein:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Met deze opdracht wordt de SPN _http/share point_ ingesteld voor het account van de share point-toepassings groep _demo\spAppPoolAccount_.

Vervang _http/share point_ door de SPN voor uw interne URL en _demo\spAppPoolAccount_ met het account voor de groep van toepassingen in uw omgeving. Het Setspn-opdracht wordt gezocht naar de SPN-naam voordat deze wordt toegevoegd. Er wordt al een **dubbele SPN-waarde** weer geven. In dit geval kunt u de bestaande SPN verwijderen als deze niet is ingesteld onder het juiste account voor de groep van toepassingen.

U kunt controleren of de SPN is toegevoegd door de opdracht setspn uit te voeren met de optie-L. Zie voor meer informatie over deze opdracht, [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Zorg ervoor dat de connector wordt vertrouwd voor delegering naar de SPN die is toegevoegd aan het account van de share point-toepassings groep

Configureer de KCD zodat de Azure AD-toepassingsproxy-Service gebruikers identiteiten kan delegeren aan het account van de share point-toepassings groep. KCD configureren door de Application Proxy-connector om op te halen van Kerberos-tickets voor uw gebruikers die zijn geverifieerd in Azure AD in te schakelen. En vervolgens die server wordt de context in dit geval aan de doeltoepassing of SharePoint doorgegeven.

Voor het configureren van de KCD, herhaalt u de volgende stappen uit voor elke connector-machine:

1. Meld u aan als domeinadministrator op een domeincontroller en open vervolgens **Active Directory: gebruikers en Computers**.
1. Zoek de computer waarop de connector wordt uitgevoerd. In dit voorbeeld is de dezelfde SharePoint-server.
1. Dubbelklik op de computer en klik vervolgens op de **delegering** tabblad.
1. Zorg ervoor dat de delegatie-instellingen zijn ingesteld op **deze computer mag delegeren aan de opgegeven services alleen**. Selecteer **elk verificatieprotocol gebruiken**.
1. Klik op de knop **toevoegen** , klik op **gebruikers of computers**en zoek het account van de share point-toepassings groep, bijvoorbeeld _demo\spAppPoolAccount_.
1. Selecteer in de lijst van de SPN's die u eerder hebt gemaakt voor het serviceaccount.
1. Klik op **OK**. Klik op **OK** opnieuw de wijzigingen wilt opslaan.
  
   ![De delegatie-instellingen](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Stap 2: Azure AD-proxy configureren

Nu u KCD hebt geconfigureerd, bent u klaar om Azure AD-toepassingsproxy te configureren.

1. Publiceer uw SharePoint-site met de volgende instellingen. Zie voor stapsgewijze instructies [toepassingen publiceren met behulp van Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Interne URL**: De interne URL van share point die u eerder hebt **<https://SharePoint/>** gekozen, zoals.
   * **Methode voor verificatie vooraf**: Azure Active Directory
   * **URL vertalen in kopteksten**: NO

   > [!TIP]
   > SharePoint maakt gebruik van de _Host-Header_ waarde om te controleren of de site. Deze koppelingen op basis van deze waarde wordt ook gegenereerd. Het uiteindelijke resultaat is dat een koppeling die SharePoint genereert een gepubliceerde URL correct is ingesteld voor gebruik van de externe URL. Als de waarde instelt op **Ja** kunt u ook de connector voor het doorsturen van de aanvraag voor de back-endtoepassing. Als u echter de waarde instelt op **Nee** betekent dit dat de connector niet de naam van de interne wordt verzenden. De connector verzendt in plaats daarvan de host-header als de gepubliceerde URL naar de back-endtoepassing.

   ![SharePoint als toepassing publiceren](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Zodra uw app is gepubliceerd, moet u de instellingen voor eenmalige aanmelding configureren met de volgende stappen uit:

   1. Selecteer op de toepassingspagina in de portal, **eenmalige aanmelding**.
   1. Selecteer voor Single Sign-on modus **geïntegreerde Windows-verificatie**.
   1. SPN voor interne toepassing ingesteld op de waarde die u eerder hebt ingesteld. Voor dit voor beeld is dat **http/share point**.
   1. Selecteer in ' gedelegeerde aanmeldings-id ' de meest geschikte optie voor de configuratie van uw Active Directory-forest. Als u bijvoorbeeld één AD-domein in uw forest hebt, selecteert u de **naam van het on-premises SAM-account** (zoals hieronder wordt weer gegeven), maar als uw gebruikers zich niet in hetzelfde domein bevinden als share point en de connector servers van de app-proxy, selecteert u vervolgens **on-premises User Principal name** (niet weer gegeven).

   ![Geïntegreerde Windows-verificatie voor eenmalige aanmelding configureren](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Voor het voltooien van het instellen van uw toepassing, gaat u naar de **gebruikers en groepen** sectie en gebruikers toegang krijgen tot deze toepassing toe te wijzen. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Stap 3: Share point configureren voor het gebruik van Kerberos-en Azure AD-proxy-Url's

De volgende stap is het uitbreiden van de share point-webtoepassing naar een nieuwe zone, geconfigureerd met Kerberos en de juiste alternatieve toegangs toewijzing zodat share point binnenkomende aanvragen kan verwerken die zijn verzonden naar de interne URL en die reageert met koppelingen die zijn gemaakt voor de externe URL.

1. Start de **share point-beheer shell**.
1. Voer het volgende script uit om de webtoepassing uit te breiden naar een extranet zone en Kerberos-verificatie in te scha kelen:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Open de site **Centraal beheer van share point** .
1. Onder **systeeminstellingen**, selecteer **alternatieve toegangstoewijzingen configureren**. Hiermee opent u het vak Alternatieve toewijzingen voor toegang.
1. Selecteer uw site, bijvoorbeeld **share point-80**. Voor het moment beschikt de extranet zone nog niet over de interne URL die juist is ingesteld:

   ![Hiermee wordt het vak Alternatieve toegangs toewijzingen weer gegeven](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Klik op **interne Url's toevoegen**.
1. In het tekstvak **URL-protocol, host en poort** typt u de **interne URL** die in azure AD-proxy <https://SharePoint/>is geconfigureerd, bijvoorbeeld.
1. Selecteer zone **extranet** in de vervolg keuzelijst.
1. Klik op **Opslaan**.
1. De alternatieve toegangs toewijzingen moeten er nu als volgt uitzien:

    ![Juiste alternatieve toegangs toewijzingen](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Stap 4: Zorg ervoor dat er een HTTPS-certificaat is geconfigureerd voor de IIS-site van de extranet zone

De share point-configuratie is nu voltooid, maar omdat de interne URL van de <https://SharePoint/>extranet zone is, moet er een certificaat worden ingesteld voor deze site.

1. Open de Windows Power shell-console.
1. Voer het volgende script uit om een zelfondertekend certificaat te genereren en toe te voegen aan de computer mijn archief:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Zelfondertekende certificaten zijn alleen geschikt voor test doeleinden. In productie omgevingen wordt het ten zeerste aanbevolen certificaten te gebruiken die zijn uitgegeven door een certificerings instantie.

1. Open de console Internet Information Services Manager.
1. Vouw de server uit in de structuur weergave, vouw sites uit, selecteer de site share point-AAD-proxy en klik op **bindingen**.
1. Selecteer HTTPS-binding en klik op **bewerken...** .
1. Kies in het veld SSL-certificaat de optie **share point-** certificaat en klik op OK.

U kunt nu toegang tot de SharePoint-site extern via Azure AD-toepassingsproxy.

## <a name="next-steps"></a>Volgende stappen

* [Werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)
* [Meer informatie over Azure AD Application Proxy connectors](application-proxy-connectors.md)
