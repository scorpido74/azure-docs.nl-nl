---
title: Rastoegang tot SharePoint inschakelen - Azure AD-toepassingsproxy
description: Behandelt de basisprincipes over het integreren van een on-premises SharePoint-server met Azure AD Application Proxy.
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
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481293"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Externe toegang tot SharePoint inschakelen met Azure AD-toepassingsproxy

In deze stapsgewijze handleiding wordt uitgelegd hoe u een on-premises SharePoint-farm integreren met Azure AD-toepassingsproxy (Azure Directory).

## <a name="prerequisites"></a>Vereisten

Als u de configuratie wilt uitvoeren, hebt u de volgende bronnen nodig:
- Een SharePoint 2013-farm of nieuwer.
- Een Azure AD-tenant met een abonnement dat toepassingsproxy bevat. Meer informatie over [Azure AD-abonnementen en -prijzen](https://azure.microsoft.com/pricing/details/active-directory/).
- Een [aangepast, geverifieerd domein](../fundamentals/add-custom-domain.md) in de Azure AD-tenant.
- On-premises Active Directory gesynchroniseerd met Azure AD Connect, waarmee gebruikers zich kunnen [aanmelden bij Azure.](../hybrid/plan-connect-user-signin.md)
- Een Application Proxy-connector die is geïnstalleerd en uitgevoerd op een machine binnen het bedrijfsdomein.

Voor het configureren van SharePoint met toepassingsproxy zijn twee URL's vereist:
- Een externe URL, zichtbaar voor eindgebruikers en bepaald in Azure AD. Deze URL kan een aangepast domein gebruiken. Meer informatie over [het werken met aangepaste domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- Een interne URL, alleen bekend binnen het bedrijfsdomein en nooit direct gebruikt.

> [!IMPORTANT]
> Volg deze aanbevelingen voor de interne URL om ervoor te zorgen dat de koppelingen correct zijn toegewezen:
> - Gebruik HTTPS.
> - Gebruik geen aangepaste poorten.
> - Maak in het DNS (Corporate Domain Name System) een host (A) die naar de SharePoint WFE (of load balancer) wijst en geen alias (CName).

In dit artikel worden de volgende waarden gebruikt:
- Interne URL:`https://sharepoint`
- Externe URL:`https://spsites-demo1984.msappproxy.net/`
- Toepassingsgroepaccount voor de SharePoint-webtoepassing:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Stap 1: Een toepassing configureren in Azure AD die toepassingsproxy gebruikt

In deze stap maakt u een toepassing in uw Azure Active Directory-tenant die toepassingsproxy gebruikt. U stelt de externe URL in en geeft de interne URL op, die beide later in SharePoint worden gebruikt.

1. Maak de app zoals beschreven met de volgende instellingen. Zie [Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor stapsgewijze instructies.
   * **Interne URL:** interne URL van SharePoint die later `https://sharepoint`in SharePoint wordt ingesteld, zoals .
   * **Pre-authenticatie:** Azure Active Directory
   * **URL's vertalen in kopteksten:** nee
   * **URL's vertalen in toepassingsinstantie:** nee

   ![SharePoint publiceren als toepassing](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Voer de volgende stappen uit om de instellingen voor één aanmelding te configureren nadat uw app is gepubliceerd:

   1. Selecteer op de toepassingspagina in de portal De optie **Eén aanmelding**.
   1. Selecteer **Geïntegreerde Windows-verificatie** **voor de modus**Eenmalig aanmelden .
   1. Stel **interne toepassing SPN** in op de waarde die u eerder instelt. In dit voorbeeld is `HTTP/sharepoint`de waarde .
   1. Selecteer **onder Gedelegeerde aanmeldingsidentiteit**de meest geschikte optie voor uw Active Directory-forestconfiguratie. Als u bijvoorbeeld één Active Directory-domein in uw forest hebt, selecteert u **De naam van on-premises SAM-account** (zoals in de volgende schermafbeelding wordt weergegeven). Maar als uw gebruikers zich niet in hetzelfde domein bevinden als SharePoint en de Application Proxy Connector-servers, selecteert u **On-premises gebruikersnaam** (niet weergegeven in de schermafbeelding).

   ![Geïntegreerde Windows-verificatie configureren voor SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Als u het instellen van uw toepassing wilt voltooien, gaat u naar de sectie **Gebruikers en groepen** en wijst u gebruikers toe om toegang te krijgen tot deze toepassing. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Stap 2: De SharePoint-webtoepassing configureren

De SharePoint-webtoepassing moet zijn geconfigureerd met Kerberos en de juiste alternatieve toegangstoewijzingen om correct te kunnen werken met Azure AD Application Proxy. Er zijn twee mogelijke opties:

- Maak een nieuwe webtoepassing en gebruik alleen de standaardzone. Dit is de voorkeursoptie, omdat het de beste ervaring biedt met SharePoint (bijvoorbeeld de koppelingen in de e-mailwaarschuwingen die door SharePoint worden gegenereerd, verwijzen altijd naar de standaardzone).
- Een bestaande webtoepassing uitbreiden om Kerberos in een niet-standaardzone te configureren.

> [!IMPORTANT]
> Ongeacht de zone die wordt gebruikt, moet het account van de groep van toepassingen van de SharePoint-webtoepassing een domeinaccount zijn dat Kerberos correct kan werken.

### <a name="provision-the-sharepoint-web-application"></a>De SharePoint-webtoepassing inrichten

- Als u een nieuwe webtoepassing maakt en alleen de standaardzone (voorkeursoptie) gebruikt:

    1. Start de **SharePoint Management Shell** en voer het volgende script uit:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Open de **sharepoint centrale beheersite.**
    1. Selecteer **onder Systeeminstellingen**de optie **Alternatieve toegangstoewijzingen configureren**. Het vak **Alternatieve toegangstoewijzingwordt** geopend.
    1. Filter het display met de nieuwe webtoepassing en bevestig dat u zoiets als volgt ziet:

       ![Alternatieve toegangstoewijzingen van webtoepassingen](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Als u een bestaande webtoepassing uitbreidt naar een nieuwe zone (voor het geval u de standaardzone niet gebruiken):

    1. Start de SharePoint Management Shell en voer het volgende script uit:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Open de **sharepoint centrale beheersite.**
    1. Selecteer **onder Systeeminstellingen**de optie **Alternatieve toegangstoewijzingen configureren**. Het vak **Alternatieve toegangstoewijzingwordt** geopend.
    1. Filter het display met de uitgebreide webtoepassing en bevestig dat u zoiets als volgt ziet:

        ![Alternatieve toegangstoewijzingen van uitgebreide toepassing](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Controleer of de SharePoint-webtoepassing wordt uitgevoerd onder een domeinaccount

Voer de volgende stappen uit om het account met de groep van toepassingen van de SharePoint-webtoepassing te identificeren en ervoor te zorgen dat het een domeinaccount is:

1. Open de **sharepoint centrale beheersite.**
1. Ga naar **Beveiliging** en selecteer **Serviceaccounts configureren**.
1. Selecteer **Groep van webtoepassingen - YourWebApplicationName**.

   ![Keuzes voor het configureren van een serviceaccount](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Controleer of **Selecteer een account voor deze component** een domeinaccount retourneert en onthoud het, omdat dit in de volgende stap nodig is.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Controleer of een HTTPS-certificaat is geconfigureerd voor de IIS-site van de Extranet-zone

Omdat de interne URL`https://SharePoint/`gebruik maakt van HTTPS-protocol ( ), moet een certificaat worden ingesteld op de IIS-site (Internet Information Services).

1. Open de Windows PowerShell-console.
1. Voer het volgende script uit om een zelfondertekend certificaat te genereren en toe te voegen aan de MY-winkel van de computer:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Zelfondertekende certificaten zijn alleen geschikt voor testdoeleinden. In productieomgevingen raden we u ten zeerste aan certificaten te gebruiken die zijn uitgegeven door een certificaatinstantie.

1. Open de console Internet Information Services Manager.
1. Vouw de server uit in de structuurweergave, vouw **Sites**uit, selecteer de **sharepoint - AAD-proxysite** en selecteer **Bindingen**.
1. Selecteer **https-binding** en selecteer **vervolgens Bewerken**.
1. Kies in het veld TLS/SSL-certificaat **SharePoint-certificaat** en selecteer **VERVOLGENS OK**.

U de SharePoint-site nu extern openen via Azure AD Application Proxy.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Stap 3: Kerberos Beperkte delegatie configureren

Gebruikers verifiëren in eerste instantie in Azure AD en vervolgens naar SharePoint met behulp van Kerberos via de Azure AD Proxy-connector. Als u wilt dat de connector namens de Azure AD-gebruiker een Kerberos-token kan verkrijgen, moet u De Constrained Delegation (KCD) van Kerberos configureren met protocolovergang. Zie Het overzicht van [de beperkte delegatie kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))voor meer informatie over KCD .

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>De SPN instellen voor het SharePoint-serviceaccount

In dit artikel is `https://sharepoint`de interne URL , en dus `HTTP/sharepoint`is de serviceprincipal name (SPN) . U moet deze waarden vervangen door de waarden die overeenkomen met uw omgeving.
Als u `HTTP/sharepoint` SPN wilt registreren `Contoso\spapppool`voor het SharePoint-groepgroepaccount, voert u de volgende opdracht uit vanuit een opdrachtprompt als beheerder van het domein:

`setspn -S HTTP/sharepoint Contoso\spapppool`

De `Setspn` opdracht zoekt naar de SPN voordat deze wordt toegevoegd. Als de SPN al bestaat, ziet u een fout **in De SPN-waarde dupliceren.** In dat geval u overwegen de bestaande SPN te verwijderen als deze niet is ingesteld onder het juiste account voor de groep van toepassingen. U controleren of de SPN `Setspn` is toegevoegd door de opdracht uit te voeren met de optie -L. Zie [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))voor meer informatie over deze opdracht.

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Controleer of de connector wordt vertrouwd voor de overdracht naar de SPN die is toegevoegd aan het SharePoint-toepassingsgroepaccount

Configureer de KCD zodat de Azure AD Application Proxy-service gebruikersidentiteiten kan delegeren aan het SharePoint-groepmetingsaccount. Configureer KCD door de toepassingsproxyconnector in te schakelen om Kerberos-tickets op te halen voor uw gebruikers die zijn geverifieerd in Azure AD. Vervolgens geeft die server de context door aan de doeltoepassing (SharePoint in dit geval).

Voer de volgende stappen uit voor elke connectormachine om de KCD te configureren:

1. Meld u aan bij een domeincontroller als domeinbeheerder en open Active Directory- gebruikers en computers.
1. Zoek de computer waarop de Azure AD-proxyconnector wordt uitgevoerd. In dit voorbeeld is het de SharePoint-server zelf.
1. Dubbelklik op de computer en selecteer het tabblad **Delegatie.**
1. Controleer of de delegatieopties zijn ingesteld op **Deze computer vertrouwen voor alleen overdracht naar de opgegeven services.** Selecteer vervolgens **Een verificatieprotocol gebruiken**.
1. Selecteer de knop **Toevoegen,** selecteer **Gebruikers of computers**en zoek het SharePoint-groepgroepaccount. Bijvoorbeeld: `Contoso\spapppool`.
1. Selecteer in de lijst met SPN's de lijst die u eerder hebt gemaakt voor het serviceaccount.
1. Selecteer **OK** en selecteer **opnieuw OK** om de wijzigingen op te slaan.
  
   ![Delegatie-instellingen](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

U bent nu klaar om u aan te melden bij SharePoint met behulp van de externe URL en om te verifiëren met Azure.

## <a name="troubleshoot-sign-in-errors"></a>Aanmeldingsfouten oplossen

Als aanmelden bij de site niet werkt, u meer informatie krijgen over het probleem in de connectorlogboeken: Vanaf de machine waarop de connector wordt uitgevoerd, opent u de gebeurtenisviewer, gaat u naar **Toepassingen en serviceslogboeken** > **Microsoft** > **AadApplicationProxy** > **Connector**en inspecteert u het logboek **beheerder.**

## <a name="next-steps"></a>Volgende stappen

* [Werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)
* [Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
