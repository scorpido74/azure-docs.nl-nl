---
title: Inleiding tot Azure-beveiliging | Microsoft Documenten
description: Meer informatie over Azure Security, de services en hoe deze werken.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: a7957c7cbcfa511ea441d8c7bd4371f56ab87560
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461681"
---
# <a name="introduction-to-azure-security"></a>Inleiding tot Azure-beveiliging
## <a name="overview"></a>Overzicht
We weten dat beveiliging taak één is in de cloud en hoe belangrijk het is dat u nauwkeurige en tijdige informatie over Azure-beveiliging vindt. Een van de beste redenen om Azure te gebruiken voor uw toepassingen en services is om te profiteren van het brede scala aan beveiligingstools en -mogelijkheden. Deze tools en mogelijkheden maken het mogelijk om veilige oplossingen te maken op het beveiligde Azure-platform. Microsoft Azure biedt vertrouwelijkheid, integriteit en beschikbaarheid van klantgegevens, terwijl ook transparante verantwoording mogelijk wordt gemaakt.

In dit artikel wordt uitgebreid gekeken naar de beveiliging die beschikbaar is met Azure.

### <a name="azure-platform"></a>Azure-platform
Azure is een public cloud service platform dat een brede selectie van besturingssystemen, programmeertalen, frameworks, tools, databases en apparaten ondersteunt. Het kan Linux-containers uitvoeren met Docker-integratie; apps bouwen met JavaScript, Python, .NET, PHP, Java en Node.js; back-ends bouwen voor iOS-, Android- en Windows-apparaten.

Azure public cloud services ondersteunen dezelfde technologieën waar miljoenen ontwikkelaars en IT-professionals al vertrouwen op en vertrouwen. Wanneer u IT-assets bouwt of migreert naar een openbare cloudserviceprovider die u vertrouwt op de mogelijkheden van die organisatie om uw toepassingen en gegevens te beschermen met de services en de besturingselementen die ze bieden om de beveiliging van uw cloudgebaseerde assets te beheren.

De infrastructuur van Azure is ontworpen van faciliteit tot toepassingen voor het hosten van miljoenen klanten tegelijk en biedt een betrouwbare basis waarop bedrijven kunnen voldoen aan hun beveiligingsvereisten.

Daarnaast biedt Azure u een breed scala aan configureerbare beveiligingsopties en de mogelijkheid om deze te beheren, zodat u de beveiliging aanpassen aan de unieke vereisten van de implementaties van uw organisatie. Dit document helpt u te begrijpen hoe Azure-beveiligingsmogelijkheden u kunnen helpen aan deze vereisten te voldoen.

> [!Note]
> De primaire focus van dit document ligt op klantgerichte besturingselementen die u gebruiken om de beveiliging van uw toepassingen en services aan te passen en te verhogen.
>
> Zie [Azure-infrastructuurbeveiliging](infrastructure.md)voor informatie over hoe Microsoft het Azure-platform zelf beveiligt.

## <a name="summary-of-azure-security-capabilities"></a>Overzicht van azure-beveiligingsmogelijkheden

### <a name="features-to-secure-the-azure-platform"></a>Functies om het Azure-platform te beveiligen
De volgende functies zijn mogelijkheden die u controleren om de zekerheid te bieden dat het Azure-platform op een veilige manier wordt beheerd. Er zijn koppelingen voorzien voor verdere informatie over hoe Microsoft vragen over consumentenvertrouwen op vier gebieden aanpakt: veilig platform, privacy-& controles, naleving en transparantie.

| [Veilig platform](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Privacy &-besturingselementen](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Naleving](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Transparantie](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Security Development Cycle](https://www.microsoft.com/sdl/), Interne audits | [Beheer uw gegevens de hele tijd](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Vertrouwenscentrum](https://www.microsoft.com/trustcenter/default.aspx) |[Hoe Microsoft klantgegevens beveiligt in Azure-services](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Verplichte beveiligingstraining, antecedentenonderzoek](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Controle op de locatie van gegevens](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Gemeenschappelijke besturingshub](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Hoe Microsoft gegevenslocatie beheert in Azure-services](https://azuredatacentermap.azurewebsites.net/)|
| [Penetratietesten,](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) [inbraakdetectie, DDoS,](https://www.microsoft.com/trustcenter/Security/ThreatManagement) [Audits & logging](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Gegevenstoegang bieden op uw voorwaarden](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [De Due Diligence-checklist voor Cloud Services](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Wie in Microsoft heeft toegang tot uw gegevens onder welke voorwaarden](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [State of the art datacenter,](https://www.microsoft.com/cloud-platform/global-datacenters)fysieke beveiliging, [Secure Network](network-overview.md) | [Reageren op rechtshandhaving](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Compliance per service, locatie & Industrie](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Hoe Microsoft klantgegevens beveiligt in Azure-services](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Reactie op beveiligingsincident](https://aka.ms/SecurityResponsepaper), [gedeelde verantwoordelijkheid](https://aka.ms/sharedresponsibility) |[Strenge privacynormen](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Certificering voor Azure-services, Transparantiehub controleren](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Functies voor het beveiligen van gegevens en toepassingen
Afhankelijk van het cloudservicemodel is er een variabele verantwoordelijkheid voor wie verantwoordelijk is voor het beheer van de beveiliging van de toepassing of service. Er zijn mogelijkheden beschikbaar in het Azure-platform om u te helpen bij het voldoen aan deze verantwoordelijkheden via ingebouwde functies en via partneroplossingen die kunnen worden geïmplementeerd in een Azure-abonnement.

De ingebouwde mogelijkheden zijn georganiseerd in zes functionele gebieden: Operations, Applications, Storage, Networking, Compute en Identity. Aanvullende details over de functies en mogelijkheden die beschikbaar zijn in het Azure-platform in deze zes gebieden worden verstrekt via beknopte informatie.

## <a name="operations"></a>Bewerkingen
In dit gedeelte vindt u aanvullende informatie over belangrijke functies in beveiligingsbewerkingen en beknopte informatie over deze mogelijkheden.

### <a name="security-and-audit-dashboard"></a>Dashboard beveiliging en controle
De [oplossing voor beveiliging en audit](../../security-center/security-center-intro.md) biedt een uitgebreid overzicht van de IT-beveiligingshouding van uw organisatie met [ingebouwde zoekopdrachten](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) voor opmerkelijke problemen die uw aandacht vereisen. Het [dashboard Beveiliging en controle](https://technet.microsoft.com/library/mt484091.aspx) is het startscherm voor alles wat met beveiliging te maken heeft in Azure Monitor-logboeken. Het biedt inzicht op hoog niveau in de beveiligingsstatus van uw computers. Ook kunnen op het startscherm alle gebeurtenissen van de afgelopen 24 uur, 7 dagen of een ander tijdsbestek worden weergegeven.

Bovendien u Security & Compliance configureren om [automatisch specifieke acties uit](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) te voeren wanneer een specifieke gebeurtenis wordt gedetecteerd.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Met Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) u als groep werken met de resources in uw oplossing. U kunt alle resources voor uw oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. U gebruikt een [Azure Resource Manager-sjabloon](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) voor implementatie en die sjabloon kan werken voor verschillende omgevingen, zoals testen, fasering en productie. Resource Manager biedt beveiliging, controle en tagfuncties die u na de implementatie helpen bij het beheren van uw resources.

Azure Resource Manager-sjabloongebaseerde implementaties helpen de beveiliging van oplossingen die in Azure zijn geïmplementeerd te verbeteren, omdat standaardinstellingen voor beveiligingsbeheer en kunnen worden geïntegreerd in gestandaardiseerde implementaties op basis van sjablonen. Dit vermindert het risico op beveiligingsconfiguratiefouten die kunnen plaatsvinden tijdens handmatige implementaties.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) is een uitbreidbare Application Performance Management (APM) service voor webontwikkelaars. Met Application Insights u uw live webapplicaties monitoren en automatisch prestatieafwijkingen detecteren. Het bevat krachtige analysetools om u te helpen problemen te diagnosticeren en te begrijpen wat gebruikers daadwerkelijk met uw apps doen. Het controleert uw toepassing de hele tijd dat deze wordt uitgevoerd, zowel tijdens het testen als nadat u deze hebt gepubliceerd of geïmplementeerd.

Application Insights maakt grafieken en tabellen die u bijvoorbeeld laten zien op welke momenten van de dag u de meeste gebruikers krijgt, hoe responsief de app is en hoe goed deze wordt bediend door externe services waarvan deze afhankelijk is.

Als er crashes, storingen of prestatieproblemen zijn, u de telemetriegegevens in detail doorzoeken om de oorzaak vast te stellen. En de service stuurt u e-mails als er wijzigingen zijn in de beschikbaarheid en prestaties van uw app. Application Insight wordt dus een waardevolle beveiligingstool omdat het helpt bij de beschikbaarheid in de vertrouwelijkheid, integriteit en beschikbaarheid striade van de beveiliging.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) biedt visualisatie, query, routering, waarschuwing, automatische schaal en automatisering op gegevens, zowel vanuit de Azure-infrastructuur ([Activity Log](../../azure-monitor/platform/platform-logs-overview.md)) als elke afzonderlijke Azure-bron[(Diagnostische logboeken).](../../azure-monitor/platform/platform-logs-overview.md) U Azure Monitor gebruiken om u te waarschuwen voor beveiligingsgerelateerde gebeurtenissen die worden gegenereerd in Azure-logboeken.

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
[Azure Monitor-logboeken](https://azure.microsoft.com/documentation/services/log-analytics/) : biedt naast Azure-resources een IT-beheeroplossing voor zowel on-premises als externe cloudinfrastructuur (zoals AWS). Gegevens uit Azure Monitor kunnen rechtstreeks naar Azure Monitor-logboeken worden doorgestuurd, zodat u statistieken en logboeken voor uw hele omgeving op één plaats zien.

Azure Monitor-logboeken kunnen een handig hulpmiddel zijn in forensische en andere beveiligingsanalyses, omdat de tool u in staat stelt om snel grote hoeveelheden beveiligingsgerelateerde items te doorzoeken met een flexibele querybenadering. Bovendien kunnen on-premises [firewall- en proxylogboeken naar Azure worden geëxporteerd en beschikbaar worden gesteld voor analyse met Azure Monitor-logboeken.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../../advisor/index.yml) is een gepersonaliseerde cloudconsultant die u helpt bij het optimaliseren van uw Azure-implementaties. Advisor analyseert de configuratie van uw resources en de gebruiksgerelateerde telemetrie. Vervolgens worden oplossingen aanbevolen om de [prestaties,](../../advisor/advisor-performance-recommendations.md) [beveiliging](../../advisor/advisor-security-recommendations.md)en [hoge beschikbaarheid](../../advisor/advisor-high-availability-recommendations.md) van uw resources te verbeteren terwijl u op zoek bent naar mogelijkheden om uw [totale Azure-uitgaven](../../advisor/advisor-cost-recommendations.md)te verminderen. Azure Advisor biedt beveiligingsaanbevelingen, die uw algehele beveiligingshouding voor oplossingen die u implementeert in Azure aanzienlijk kunnen verbeteren. Deze aanbevelingen zijn afkomstig uit beveiligingsanalyses uitgevoerd door [Azure Security Center.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Azure Security Center
[Security Center](../../security-center/security-center-intro.md) helpt u bedreigingen te voorkomen, detecteren en erop te reageren met meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Bovendien helpt Security Center bij beveiligingsbewerkingen door u één dashboard te bieden dat waarschuwingen en aanbevelingen weergeeft waarop onmiddellijk kan worden gereageerd. Vaak u problemen met één klik oplossen in de console Beveiligingscentrum.
## <a name="applications"></a>Toepassingen
De sectie bevat aanvullende informatie over belangrijke functies in de beveiliging van toepassingen en beknopte informatie over deze mogelijkheden.

### <a name="web-application-vulnerability-scanning"></a>Scannen van kwetsbaarheden in webtoepassingen
Een van de eenvoudigste manieren om aan de slag te gaan met het testen op kwetsbaarheden in uw [App Service-app](../../app-service/overview.md) is door de [integratie met Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) te gebruiken om het scannen van kwetsbaarheden met één klik uit te voeren in uw app. U de testresultaten bekijken in een eenvoudig te begrijpen rapport en leren hoe u elk beveiligingslek oplossen met stapsgewijze instructies.

### <a name="penetration-testing"></a>Penetratietesten
Als u liever uw eigen penetratietests uitvoert of een andere scannersuite of provider wilt gebruiken, moet u het [goedkeuringsproces voor azure-penetratietesten](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) volgen en vooraf goedkeuring verkrijgen om de gewenste penetratietests uit te voeren.

### <a name="web-application-firewall"></a>Firewall voor webtoepassingen
De webapplication firewall (WAF) in [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) helpt webtoepassingen te beschermen tegen veelvoorkomende webaanvallen zoals SQL-injectie, cross-site scripting-aanvallen en sessiekaping. Het komt vooraf geconfigureerd met bescherming tegen bedreigingen geïdentificeerd door de [Open Web Application Security Project (OWASP) als de top 10 veelvoorkomende kwetsbaarheden](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Verificatie en autorisatie in Azure App Service
[App Service Authentication / Autorisatie](../../app-service/overview-authentication-authorization.md) is een functie die een manier biedt voor uw toepassing om zich aan te melden voor gebruikers, zodat u geen code hoeft te wijzigen op de backend van de app. Het biedt een eenvoudige manier om uw toepassing te beschermen en te werken met gegevens per gebruiker.

### <a name="layered-security-architecture"></a>Gelaagde beveiligingsarchitectuur
Aangezien [App-serviceomgevingen](../../app-service/environment/app-service-app-service-environment-intro.md) een geïsoleerde runtime-omgeving bieden die is geïmplementeerd in een [Azure Virtual Network,](../../virtual-network/virtual-networks-overview.md)kunnen ontwikkelaars een gelaagde beveiligingsarchitectuur maken die verschillende niveaus van netwerktoegang biedt voor elke toepassingslaag. Een veel voorkomende wens is om API back-ends te verbergen voor algemene internettoegang, en alleen API's toe te staan om te worden aangeroepen door upstream web apps. [Netwerkbeveiligingsgroepen (NSG's)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) kunnen worden gebruikt op subnetten van Azure Virtual Network met App-serviceomgevingen om de toegang van het publiek tot API-toepassingen te beperken.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Webserverdiagnostiek en toepassingsdiagnostiek
App Service-webapps bieden diagnostische functionaliteit voor het registreren van informatie van zowel de webserver als de webtoepassing. Deze zijn logisch gescheiden in [webserverdiagnostiek](../../app-service/troubleshoot-diagnostic-logs.md) en [toepassingsdiagnostiek.](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx) Webserver bevat twee belangrijke ontwikkelingen in het diagnosticeren en oplossen van problemen sites en toepassingen.

De eerste nieuwe functie is realtime statusinformatie over toepassingsgroepen, werkprocessen, sites, toepassingsdomeinen en uitvoerenvan aanvragen. De tweede nieuwe voordelen zijn de gedetailleerde traceringsgebeurtenissen die een aanvraag bijhouden tijdens het volledige aanvraag- en antwoordproces.

Om het verzamelen van deze traceringsgebeurtenissen mogelijk te maken, kan IIS 7 worden geconfigureerd om automatisch volledige traceerlogboeken vast te leggen, in XML-indeling, voor een bepaald verzoek op basis van verstreken tijd- of foutreactiecodes.

#### <a name="web-server-diagnostics"></a>Webserverdiagnostiek
U de volgende soorten logboeken in- of uitschakelen:

-   Gedetailleerde foutlogboekregistratie - Gedetailleerde foutgegevens voor HTTP-statuscodes die wijzen op een fout (statuscode 400 of hoger). Dit kan informatie bevatten die kan helpen bepalen waarom de server de foutcode heeft geretourneerd.

-   Tracering van mislukte aanvragen - Gedetailleerde informatie over mislukte aanvragen, inclusief een spoor van de IIS-componenten die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is genomen. Dit kan handig zijn als u de siteprestaties probeert te verhogen of wilt isoleren wat de oorzaak is van een specifieke HTTP-fout die moet worden geretourneerd.

-   Webserverlogboekregistratie - Informatie over HTTP-transacties met de uitgebreide w3C-bestandsindeling. Dit is handig bij het bepalen van algemene sitestatistieken, zoals het aantal afgehandelde aanvragen of het aantal aanvragen dat afkomstig is van een specifiek IP-adres.

#### <a name="application-diagnostics"></a>Toepassingsdiagnostiek
[Met toepassingsdiagnostiek](../../app-service/troubleshoot-diagnostic-logs.md) u informatie vastleggen die door een webtoepassing wordt geproduceerd. ASP.NET toepassingen de klasse [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) kunnen gebruiken om informatie aan te melden bij het logboek voor toepassingsdiagnostiek. Application Diagnostics kent twee belangrijke gebeurtenistypen, gebeurtenissen die gerelateerd zijn aan toepassingsprestaties en gebeurtenissen die gerelateerd zijn aan toepassingsstoringen en -fouten. De storingen en fouten kunnen verder worden onderverdeeld in problemen met de connectiviteit, problemen met de beveiliging en problemen door systeemfouten. Systeemfouten worden doorgaans veroorzaakt door een probleem met de toepassingscode.

In Application Diagnostics kunt u gebeurtenissen weergeven die op de volgende manieren zijn gegroepeerd:

-   Alle (alle gebeurtenissen worden weergegeven)
-   Toepassingsfouten (uitzonderingsgebeurtenissen worden weergegeven)
-   Prestaties (prestatiegebeurtenissen worden weergegeven)

## <a name="storage"></a>Storage
De sectie bevat aanvullende informatie over belangrijke functies in Azure-opslagbeveiliging en beknopte informatie over deze mogelijkheden.

### <a name="role-based-access-control-rbac"></a>RBAC (op rollen gebaseerd toegangsbeheer)
U uw opslagaccount beveiligen met RBAC (Role-Based Access Control). Het beperken van de toegang op basis van de [noodzaak om beveiligingsprincipes te kennen](https://en.wikipedia.org/wiki/Need_to_know) en de minste [bevoegdheden](https://en.wikipedia.org/wiki/Principle_of_least_privilege) te kennen, is noodzakelijk voor organisaties die beveiligingsbeleid voor gegevenstoegang willen afdwingen. Deze toegangsrechten worden verleend door de juiste RBAC-rol toe te staan aan groepen en toepassingen met een bepaald toepassingsgebied. U [ingebouwde RBAC-rollen](../../role-based-access-control/built-in-roles.md), zoals Opslagaccountbijdrager, gebruiken om bevoegdheden toe te wijzen aan gebruikers. Toegang tot de opslagsleutels voor een opslagaccount met behulp van het [Azure Resource Manager-model](../../storage/blobs/security-recommendations.md) kan worden beheerd via RBAC (Role-Based Access Control).

### <a name="shared-access-signature"></a>Handtekening voor gedeelde toegang
Een [SAS (Shared Access Signature; handtekening voor gedeelde toegang)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) biedt gedelegeerde toegang tot bronnen in uw opslagaccount. De SAS betekent dat u een client beperkte machtigingen verlenen aan objecten in uw opslagaccount voor een bepaalde periode en met een opgegeven set machtigingen. U deze beperkte machtigingen verlenen zonder dat u uw toegangssleutels voor uw account hoeft te delen.

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Versleuteling tijdens het transport is een mechanisme om gegevens te beschermen wanneer deze via netwerken worden verzonden. Met Azure Storage u gegevens beveiligen met:
-   [Versleuteling op transportniveau,](../../storage/blobs/security-recommendations.md)zoals HTTPS wanneer u gegevens naar of uit Azure Storage overbrengt.

-   [Draadversleuteling](../../storage/blobs/security-recommendations.md), zoals [SMB 3.0-versleuteling](../../storage/blobs/security-recommendations.md) voor [Azure File-shares](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Client-side encryptie, om de gegevens te versleutelen voordat het wordt overgedragen in de opslag en om de gegevens te decoderen nadat het is overgedragen uit de opslag.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
Voor veel organisaties is gegevensversleuteling in rust een verplichte stap in de richting van gegevensprivacy, naleving en gegevenssoevereiniteit. Er zijn drie Azure-opslagbeveiligingsfuncties die versleuteling bieden van gegevens die 'in rust' zijn:

-   [Met Storage Service Encryption](../../storage/common/storage-service-encryption.md) u vragen dat de opslagservice gegevens automatisch versleutelt wanneer u deze naar Azure Storage schrijft.

-   [Client-side Encryptie](../../storage/common/storage-client-side-encryption.md) biedt ook de functie van encryptie in rust.

-   [Met Azure Disk Encryption](../azure-security-disk-encryption-overview.md) u de besturingssysteemschijven en gegevensschijven versleutelen die door een virtuele IaaS-machine worden gebruikt.

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) voert logboekregistratie uit en biedt metrische gegevens voor een opslagaccount. U deze gegevens gebruiken om aanvragen te traceren, gebruikstrends te analyseren en problemen met uw opslagaccount te diagnosticeren. Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op een best-effort basis geregistreerd. De volgende typen geverifieerde aanvragen worden geregistreerd:
-   Succesvolle verzoeken.

-   Mislukte aanvragen, waaronder time-out, beperking, netwerk, autorisatie en andere fouten.

-   Aanvragen met behulp van een SAS (Shared Access Signature), inclusief mislukte en geslaagde aanvragen.

-   Verzoeken om analysegegevens.

### <a name="enabling-browser-based-clients-using-cors"></a>Browsergebaseerde clients inschakelen met CORS
[Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) is een mechanisme waarmee domeinen elkaar toestemming kunnen geven om toegang te krijgen tot elkaars bronnen. De user agent stuurt extra headers om ervoor te zorgen dat de JavaScript-code die vanuit een bepaald domein is geladen, toegang heeft tot bronnen in een ander domein. Dit laatste domein antwoordt vervolgens met extra headers die de oorspronkelijke domeintoegang tot de bronnen toestaan of weigeren.

Azure-opslagservices ondersteunen cors nu, zodat zodra u de CORS-regels voor de service hebt ingesteld, een correct geverifieerde aanvraag tegen de service vanuit een ander domein wordt geëvalueerd om te bepalen of dit is toegestaan volgens de regels die u hebt opgegeven.

## <a name="networking"></a>Netwerken
De sectie bevat aanvullende informatie over belangrijke functies in Azure-netwerkbeveiliging en beknopte informatie over deze mogelijkheden.

### <a name="network-layer-controls"></a>Netwerklaagbesturingselementen
Netwerktoegangscontrole is de handeling van het beperken van de connectiviteit van en naar specifieke apparaten of subnetten en vormt de kern van de netwerkbeveiliging. Het doel van netwerktoegangscontrole is ervoor te zorgen dat uw virtuele machines en services alleen toegankelijk zijn voor gebruikers en apparaten waarvoor u ze toegankelijk wilt hebben.

#### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Een [Network Security Group (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) is een eenvoudige stateful packet filtering firewall en het stelt u in staat om de toegang te beheren op basis van een [5-tuple](https://www.techopedia.com/definition/28190/5-tuple). NSGs bieden geen inspectie van de toepassingslaag of geverifieerde toegangscontroles. Ze kunnen worden gebruikt om verkeer te beheren dat zich verplaatst tussen subnetten binnen een Azure Virtual Network en verkeer tussen een Azure Virtual Network en het internet.

#### <a name="route-control-and-forced-tunneling"></a>Routecontrole en gedwongen tunneling
De mogelijkheid om het routeringsgedrag op uw Azure Virtual Networks te beheren, is een kritieke netwerkbeveiligings- en toegangscontrolemogelijkheid. Als u er bijvoorbeeld voor wilt zorgen dat al het verkeer van en naar uw Azure Virtual Network via dat virtuele beveiligingstoestel verloopt, moet u het routegedrag kunnen beheren en aanpassen. U dit doen door door door gebruikers gedefinieerde routes in Azure te configureren.

[Met door de gebruiker gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) u inkomende en uitgaande paden aanpassen voor verkeer dat zich van en naar afzonderlijke virtuele machines of subnetten verplaatst om de best mogelijke route te verzekeren. [Gedwongen tunneling](https://www.petri.com/azure-forced-tunneling) is een mechanisme dat u gebruiken om ervoor te zorgen dat uw services geen verbinding met apparaten op het internet mogen starten.

Dit is anders dan inkomende verbindingen kunnen accepteren en er vervolgens op kunnen reageren. Front-end webservers moeten reageren op verzoeken van internethosts, en dus is internetverkeer toegestaan inkomende naar deze webservers en kunnen de webservers reageren.

Gedwongen tunneling wordt vaak gebruikt om uitgaand verkeer naar het internet te dwingen om door on-premises beveiligingsproxy's en firewalls te gaan.

#### <a name="virtual-network-security-appliances"></a>Beveiligingsapparaten voor virtuele netwerken
Hoewel netwerkbeveiligingsgroepen, door de gebruiker gedefinieerde routes en gedwongen tunneling u een beveiligingsniveau bieden op het netwerk- en transportlagen van het [OSI-model,](https://en.wikipedia.org/wiki/OSI_model)kunnen er momenten zijn waarop u beveiliging op hogere niveaus van de stack wilt inschakelen. U hebt toegang tot deze verbeterde netwerkbeveiligingsfuncties met behulp van een Azure-oplossing voor het netwerkbeveiligingstoestel van Azure-partners. U de meest recente Azure-oplossingen voor netwerkbeveiliging van Azure vinden door naar de [Azure Marketplace](https://azure.microsoft.com/marketplace/) te gaan en te zoeken naar 'beveiliging' en 'netwerkbeveiliging'.

### <a name="azure-virtual-network"></a>Azure Virtual Network
Een virtueel Azure-netwerk (VNET) is een weergave van uw eigen netwerk in de cloud. Het is een logische isolatie van de Azure-netwerkstructuur die is gewijd aan uw abonnement. U kunt de IP-adresblokken, DNS-instellingen, beveiligingsbeleidsregels en routetabellen binnen dit netwerk volledig beheren. U uw VNet segmenteren in subnetten en Virtuele Azure IaaS-machines (VM's) en/of [Cloudservices (PaaS-rolexemplaren)](../../cloud-services/cloud-services-choose-me.md) op Azure Virtual Networks plaatsen.

Hiermee kunt u het virtuele netwerk via een van de beschikbare [verbindingsopties](../../vpn-gateway/index.yml) in Azure verbinden met uw on-premises netwerk. In wezen kunt u uw netwerk uitbreiden naar Azure met behoud van de volledige controle over IP-adresblokken en de schaalvoordelen van Azure voor ondernemingen.

Azure-netwerken ondersteunen verschillende veilige scenario's voor externe toegang. Enkele van deze omvatten:

-   [Afzonderlijke werkstations verbinden met een Virtueel Azure-netwerk](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [On-premises netwerk verbinden met een Azure Virtual Network met een VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [On-premises netwerk verbinden met een Azure Virtual Network met een speciale WAN-koppeling](../../expressroute/expressroute-introduction.md)

-   [Azure Virtual Networks met elkaar verbinden](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Als u netwerkverkeer wilt verzenden tussen uw Azure Virtual Network en uw on-premises site, moet u een VPN-gateway maken voor uw Azure Virtual Network. Een [VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) is een type virtuele netwerkgateway dat versleuteld verkeer via een openbare verbinding verzendt. U vpn-gateways ook gebruiken om verkeer tussen Azure Virtual Networks via de Azure-netwerkstructuur te verzenden.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) is een speciale WAN-koppeling waarmee u uw on-premises netwerken uitbreiden naar de Microsoft-cloud via een speciale privéverbinding die wordt gefaciliteerd door een connectiviteitsprovider.

![ExpressRoute](./media/overview/azure-security-fig1.png)

Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en CRM Online. Via een connectiviteitsprovider in een co-locatiefaciliteit is connectiviteit mogelijk vanuit een any-to-any (IP VPN) netwerk, een point-to-point Ethernet-netwerk of een virtuele overlappende verbinding.

ExpressRoute-verbindingen gaan niet via het openbare internet en kunnen dus als veiliger worden beschouwd dan VPN-gebaseerde oplossingen. Daardoor zijn ExpressRoute-verbindingen betrouwbaarder en sneller en hebben ze lagere latenties en betere beveiliging dan gewone verbindingen via internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](../../application-gateway/overview.md) biedt een Application Delivery Controller [(ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) als service, die verschillende mogelijkheden biedt voor het balanceren van laag 7 voor uw toepassing.

![Application Gateway](./media/overview/azure-security-fig2.png)

Hiermee u de productiviteit van webfarm's optimaliseren door CPU-intensieve TLS-beëindiging te ontladen naar de Application Gateway (ook wel bekend als TLS offload of TLS bridging). Het biedt ook andere Layer 7 routing mogelijkheden, waaronder round-robin distributie van inkomend verkeer, cookie-gebaseerde sessie affiniteit, URL pad-gebaseerde routing, en de mogelijkheid om meerdere websites te hosten achter een enkele Application Gateway. Azure Application Gateway is een load balancer in laag 7.

De gateway biedt opties voor failovers en het routeren van HTTP-aanvragen tussen servers (on-premises en in de cloud).

Toepassing biedt veel Application Delivery Controller (ADC) functies, waaronder HTTP load balancing, cookie-based sessie affinity, [TLS offload,](../../application-gateway/tutorial-restrict-web-traffic-powershell.md)custom health probes, ondersteuning voor multi-site, en vele anderen.

### <a name="web-application-firewall"></a>Web Application Firewall
Web Application Firewall is een functie van [Azure Application Gateway](../../application-gateway/overview.md) die bescherming biedt aan webtoepassingen die toepassingsgateway gebruiken voor adc-functies (Standard Application Delivery Control). Web Application Firewall doet dit door deze te beschermen tegen het grootste deel van de algemene internetbeveiligingsproblemen uit de OWASP top 10.

![Web Application Firewall](./media/overview/azure-security-fig1.png)

-   Beveiliging tegen SQL-injecties

-   Beveiliging tegen veelvoorkomende aanvallen via internet, zoals opdrachtinjectie, het smokkelen van HTTP-aanvragen, het uitsplitsen van HTTP-antwoorden en aanvallen waarbij een extern bestand wordt ingesloten

-   Beveiliging tegen schendingen van het HTTP-protocol

-   Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken

-   Beveiliging tegen bots, crawlers en scanners

-   Detectie van veelvoorkomende toepassingsverkeerde configuraties (dat wil zeggen Apache, IIS, enz.)

Een gecentraliseerde Web Application Firewall ter bescherming tegen aanvallen via internet maakt het beveiligingsbeheer veel eenvoudiger en biedt de toepassing meer veiligheid tegen de bedreigingen van indringers. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

### <a name="traffic-manager"></a>Traffic Manager
Met Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) u de distributie van gebruikersverkeer voor serviceeindpunten in verschillende datacenters beheren. Serviceeindpunten die worden ondersteund door Traffic Manager zijn Azure VM's, Web Apps en Cloudservices. U kunt Traffic Manager ook gebruiken met externe, niet-Azure-eindpunten. Traffic Manager gebruikt het Domain Name System (DNS) om clientaanvragen naar het meest geschikte eindpunt te leiden op basis van een [verkeersrouteringsmethode](../../traffic-manager/traffic-manager-routing-methods.md) en de status van de eindpunten.

Traffic Manager biedt een reeks verkeersrouteringsmethoden die passen bij verschillende toepassingsbehoeften, statusbewaking van [eindpunten](../../traffic-manager/traffic-manager-monitoring.md)en automatische failover. Traffic Manager is bestand tegen storingen, waaronder het uitvallen van een hele Azure-regio.

### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) zorgt dat uw toepassingen een hoge beschikbaarheid hebben en goede netwerkprestaties leveren. Het is een Layer 4 (TCP, UDP) load balancer die binnenkomend verkeer verdeelt over gezonde exemplaren van services die zijn gedefinieerd in een load-balanced set. Azure Load Balancer kan worden geconfigureerd om:

-   Load balance inkomend internetverkeer naar virtuele machines. Deze configuratie staat bekend als [internet-facing load balancing](../../load-balancer/concepts-limitations.md#publicloadbalancer).

-   Load balance verkeer tussen virtuele machines in een virtueel netwerk, tussen virtuele machines in cloudservices, of tussen on-premises computers en virtuele machines in een cross-premises virtueel netwerk. Deze configuratie staat bekend als [interne load balancing.](../../load-balancer/concepts-limitations.md#internalloadbalancer)

- Extern verkeer doorsturen naar een specifieke virtuele machine

### <a name="internal-dns"></a>Interne DNS
U de lijst met DNS-servers beheren die worden gebruikt in een VNet in de beheerportal of in het netwerkconfiguratiebestand. De klant kan maximaal 12 DNS-servers toevoegen voor elke VNet. Bij het opgeven van DNS-servers is het belangrijk om te controleren of u de DNS-servers van de klant in de juiste volgorde vermeldt voor de omgeving van de klant. DNS-serverlijsten werken niet round-robin. Ze worden gebruikt in de volgorde waarin ze zijn opgegeven. Als de eerste DNS-server in de lijst kan worden bereikt, gebruikt de client die DNS-server, ongeacht of de DNS-server goed functioneert of niet. Als u de DNS-servervolgorde voor het virtuele netwerk van de klant wilt wijzigen, verwijdert u de DNS-servers uit de lijst en voegt u deze weer toe in de volgorde die de klant wil. DNS ondersteunt het beschikbaarheidsaspect van de "CIA" security triade.

### <a name="azure-dns"></a>Azure DNS
Het [Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), of DNS, is verantwoordelijk voor het vertalen (of oplossen) van een website of servicenaam naar het IP-adres. [Azure DNS](../../dns/dns-overview.md) is een service voor het hosten van DNS-domeinen die naamomzetting biedt met behulp van de Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services. DNS ondersteunt het beschikbaarheidsaspect van de "CIA" security triade.

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor registreert NSGs
U de volgende diagnostische logboekcategorieën voor NSG inschakelen:

-   Gebeurtenis: bevat vermeldingen waarvoor NSG-regels worden toegepast op VM's en instantierollen op basis van MAC-adres. De status voor deze regels wordt elke 60 seconden verzameld.

-   Regelsteller: bevat vermeldingen voor hoe vaak elke NSG-regel wordt toegepast om verkeer te weigeren of toe te staan.

### <a name="security-center"></a>Security Center
[Azure Security Center](../../security-center/security-center-intro.md) analyseert continu de beveiligingsstatus van uw Azure-bronnen voor best practices voor netwerkbeveiliging. Wanneer Security Center potentiële beveiligingsproblemen identificeert, worden [er aanbevelingen](../../security-center/security-center-recommendations.md) gemaakt die u begeleiden bij het configureren van de benodigde besturingselementen om uw resources te verharden en te beschermen.

## <a name="compute"></a>Compute
De sectie bevat aanvullende informatie over belangrijke functies in dit gebied en beknopte informatie over deze mogelijkheden.

### <a name="antimalware--antivirus"></a>Antimalware & Antivirus
Met Azure IaaS u antimalwaresoftware van beveiligingsleveranciers zoals Microsoft, Symantec, Trend Micro, McAfee en Kaspersky gebruiken om uw virtuele machines te beschermen tegen schadelijke bestanden, adware en andere bedreigingen. [Microsoft Antimalware](antimalware.md) voor Azure Cloud Services en Virtuele Machines is een beveiligingsmogelijkheid die helpt bij het identificeren en verwijderen van virussen, spyware en andere schadelijke software. Microsoft Antimalware biedt configureerbare waarschuwingen wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of op uw Azure-systemen uitvoert. Microsoft Antimalware kan ook worden geïmplementeerd met Azure Security Center

### <a name="hardware-security-module"></a>Hardwarebeveiligingsmodule
Versleuteling en verificatie verbeteren de beveiliging niet, tenzij de sleutels zelf zijn beveiligd. U het beheer en de beveiliging van uw kritieke geheimen en sleutels vereenvoudigen door ze op te slaan in [Azure Key Vault.](../../key-vault/general/overview.md) Key Vault biedt de mogelijkheid om uw sleutels op te slaan in hardware Beveiligingsmodules (HSM's) die zijn gecertificeerd volgens FIPS 140-2 Level 2-standaarden. Uw SQL Server-versleutelingssleutels voor back-up of [transparante gegevensversleuteling](https://msdn.microsoft.com/library/bb934049.aspx) kunnen allemaal worden opgeslagen in Key Vault met sleutels of geheimen van uw toepassingen. Machtigingen en toegang tot deze beveiligde items worden beheerd via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Back-up van virtuele machines
[Azure Backup](../../backup/backup-overview.md) is een oplossing die uw toepassingsgegevens beschermt met nul kapitaalinvesteringen en minimale bedrijfskosten. Toepassingsfouten kunnen uw gegevens beschadigen en menselijke fouten kunnen bugs in uw toepassingen introduceren die tot beveiligingsproblemen kunnen leiden. Met Azure Backup zijn uw virtuele machines met Windows en Linux beveiligd.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Een belangrijk onderdeel van de [BCDR-strategie (Business Continuity/Disaster Recovery)](../../best-practices-availability-paired-regions.md) van uw organisatie is het uitzoeken hoe bedrijfsworkloads en -apps operationeel kunnen blijven wanneer geplande en ongeplande uitval plaatsvindt. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) helpt bij het orkestreren van replicatie, failover en herstel van workloads en apps, zodat ze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvalt.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Transparent data encryption (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) en column level encryption (CLE) zijn SQL server encryption features. Deze vorm van versleuteling vereist dat klanten de cryptografische sleutels beheren en opslaan die u voor versleuteling gebruikt.

De AKV-service (Azure Key Vault) is ontworpen om de beveiliging en het beheer van deze sleutels op een veilige en zeer beschikbare locatie te verbeteren. Met de SQL Server Connector kan SQL Server deze sleutels gebruiken vanuit Azure Key Vault.

Als u SQL Server met on-premises machines uitvoert, zijn er stappen die u volgen om toegang te krijgen tot Azure Key Vault vanaf uw on-premises SQL Server-machine. Maar voor SQL Server in Azure VM's u tijd besparen met de azure key vault-integratiefunctie. Met een paar Azure PowerShell-cmdlets om deze functie in te schakelen, u de configuratie automatiseren die nodig is voor een SQL VM om toegang te krijgen tot uw sleutelkluis.

### <a name="vm-disk-encryption"></a>VM-schijfversleuteling
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) is een nieuwe mogelijkheid waarmee u uw Windows- en Linux IaaS-schijven voor virtuele machines versleutelen. Het past de industrie standaard BitLocker functie van Windows en de DM-Crypt functie van Linux om volume encryptie voor het OS en de gegevens schijven te bieden. De oplossing is geïntegreerd met Azure Key Vault om u te helpen de schijfversleutelingssleutels en -geheimen in uw Key Vault-abonnement te beheren en te beheren. De oplossing zorgt er ook voor dat alle gegevens op de schijven van de virtuele machine in rust worden versleuteld in uw Azure-opslag.

### <a name="virtual-networking"></a>Virtueel netwerk
Virtuele machines hebben netwerkconnectiviteit nodig. Om die vereiste te ondersteunen, vereist Azure dat virtuele machines zijn aangesloten op een Azure Virtual Network. Een Azure Virtual Network is een logische constructie die is gebouwd bovenop de fysieke Azure-netwerkstructuur. Elk logisch [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) is geïsoleerd van alle andere Azure Virtual Networks. Deze isolatie helpt ervoor te zorgen dat netwerkverkeer in uw implementaties niet toegankelijk is voor andere Microsoft Azure-klanten.

### <a name="patch-updates"></a>Patchupdates
Patchupdates vormen de basis voor het vinden en oplossen van potentiële problemen en vereenvoudigen het software-updatebeheerproces, zowel door het aantal software-updates dat u in uw onderneming moet implementeren te verminderen als door uw vermogen om naleving te controleren te vergroten.

### <a name="security-policy-management-and-reporting"></a>Beheer en rapportage van beveiligingsbeleid
[Security Center](../../security-center/security-center-intro.md) helpt u bedreigingen te voorkomen, te detecteren en erop te reageren en biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden kunnen blijven en werkt met een breed ecosysteem van beveiligingsoplossingen.

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer
Het beveiligen van systemen, toepassingen en gegevens begint met identiteitsgebaseerde toegangscontroles. De functies voor identiteits- en toegangsbeheer die zijn ingebouwd in zakelijke producten en services van Microsoft helpen uw organisatorische en persoonlijke gegevens te beschermen tegen ongeautoriseerde toegang en maakt deze beschikbaar voor legitieme gebruikers, waar en wanneer ze deze nodig hebben.

### <a name="secure-identity"></a>Veilige identiteit
Microsoft gebruikt meerdere beveiligingspraktijken en -technologieën voor zijn producten en services om identiteit en toegang te beheren.

-   [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) vereist dat gebruikers meerdere methoden gebruiken voor toegang, on-premises en in de cloud. Het biedt sterke verificatie met een reeks eenvoudige verificatieopties, terwijl gebruikers worden voorzien van een eenvoudig aanmeldingsproces.

-   [Microsoft Authenticator](https://aka.ms/authenticator) biedt een gebruiksvriendelijke Multi-Factor Authentication-ervaring die werkt met zowel Microsoft Azure Active Directory- als Microsoft-accounts en ondersteuning biedt voor wearables en goedkeuringen op basis van vingerafdrukken.

-   [Handhaving van wachtwoordbeleid](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) verhoogt de beveiliging van traditionele wachtwoorden door lengte- en complexiteitsvereisten op te leggen, periodieke rotatie en accountuitsluiting na mislukte verificatiepogingen op te leggen.

-   [Verificatie op basis van tokens](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) maakt verificatie mogelijk via Azure Active Directory.

-   [Met RBAC (Role-based access control)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) u toegang verlenen op basis van de toegewezen rol van de gebruiker, waardoor het gemakkelijk is om gebruikers de hoeveelheid toegang te geven die ze nodig hebben om hun taken uit te voeren. U RBAC aanpassen volgens het bedrijfsmodel en risicotolerantie van uw organisatie.

-   [Geïntegreerd identiteitsbeheer (hybride identiteit)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) stelt u in staat om de controle over de toegang van gebruikers te behouden in interne datacenters en cloudplatforms, waardoor één gebruikersidentiteit wordt aangemaakte voor verificatie en autorisatie voor alle bronnen.

### <a name="secure-apps-and-data"></a>Apps en gegevens beveiligen
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), een uitgebreide cloudoplossing voor identiteits- en toegangsbeheer, helpt bij het beveiligen van de toegang tot gegevens in toepassingen op locatie en in de cloud en vereenvoudigt het beheer van gebruikers en groepen. Het combineert kerndirectoryservices, geavanceerd identiteitsbeheer, beveiliging en beheer van toepassingstoegang en maakt het voor ontwikkelaars gemakkelijk om op beleid gebaseerd identiteitsbeheer in hun apps in te bouwen. Om uw Azure Active Directory te verbeteren, kunt u betaalde mogelijkheden toevoegen met de Azure Active Directory Basic-, Premium P1- en Premium P2-edities.

| Gratis / gemeenschappelijke functies     | Basisfuncties    |Premium P1-functies |Premium P2-functies | Azure Active Directory Join – Alleen gerelateerde functies van Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Directory Objects](../../active-directory/active-directory-whatis.md), [User/Group Management (add/update/delete)/ User-based provisioning, Device registration](../../active-directory/active-directory-whatis.md), Single [Sign-On (SSO)](../../active-directory/active-directory-whatis.md), [Self-Service Password Change for cloud users](../../active-directory/active-directory-whatis.md), Connect [(Sync engine that extends on-premises directories to Azure Active Directory)](../../active-directory/active-directory-whatis.md), [Security / Usage Reports](../../active-directory/active-directory-whatis.md)       |   [Groepsgebaseerd toegangsbeheer / inrichting](../../active-directory/active-directory-whatis.md), [Self-Service Password Reset voor cloudgebruikers](../../active-directory/active-directory-whatis.md), [Bedrijfsbranding (aanmeldingspagina's/ aanpassing van het access panel)](../../active-directory/active-directory-whatis.md), [Application Proxy](../../active-directory/active-directory-whatis.md), [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  [Self-Service Group en app Management/Self-Service applicatie toevoegingen/dynamische groepen](../../active-directory/active-directory-whatis.md), [Self-Service Password Reset/Change/Unlock met on-premises write-back](../../active-directory/active-directory-whatis.md), [Multi-Factor Authentication (Cloud en On-premises (MFA Server))](../../active-directory/active-directory-whatis.md), [MIM CAL + MIM Server](../../active-directory/active-directory-whatis.md), Cloud App [Discovery](../../active-directory/active-directory-whatis.md), [Connect Health](../../active-directory/active-directory-whatis.md), Automatische [wachtwoord rollover voor groepsaccounts](../../active-directory/active-directory-whatis.md)|    [Identiteitsbescherming](../../active-directory/identity-protection/overview.md), [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Word lid van een apparaat naar Azure AD, Desktop SSO, Microsoft Passport voor Azure AD, Administrator BitLocker-herstel,](../../active-directory/active-directory-whatis.md) [MDM-automatische inschrijving, Self-Service BitLocker-herstel, extra lokale beheerders voor Windows 10-apparaten via Azure AD Join](../../active-directory/active-directory-whatis.md)|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) is een premium functie van Azure Active Directory waarmee u cloudtoepassingen identificeren die worden gebruikt door de werknemers in uw organisatie.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) is een beveiligingsservice die gebruikmaakt van Azure Active Directory-anomaliedetectiemogelijkheden om een geconsolideerd overzicht te bieden van risicodetecties en potentiële kwetsbaarheden die van invloed kunnen zijn op de identiteit van uw organisatie.

- [Met Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) u Azure VM's aansluiten bij een domein zonder domeincontrollers te hoeven implementeren. Gebruikers melden zich aan bij deze VM's met behulp van hun bedrijfsActive Directory-referenties en hebben naadloos toegang tot bronnen.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een zeer beschikbare, wereldwijde identiteitsbeheerservice voor apps die gericht zijn op apps die kunnen worden geschaald naar honderden miljoenen identiteiten en kunnen integreren op mobiele en webplatforms. Uw klanten kunnen zich aanmelden bij al uw apps via aanpasbare ervaringen die gebruikmaken van bestaande sociale media-accounts, of u nieuwe zelfstandige referenties maken.

- [Azure Active Directory B2B Collaboration](https://aka.ms/aad-b2b-collaboration) is een veilige partnerintegratieoplossing die uw relaties tussen bedrijven ondersteunt door partners in staat te stellen selectief toegang te krijgen tot uw bedrijfstoepassingen en -gegevens door hun zelfbeheerde identiteit te gebruiken.

- [Met Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) u cloudmogelijkheden uitbreiden naar Windows 10-apparaten voor gecentraliseerd beheer. Het maakt het mogelijk voor gebruikers om verbinding te maken met de bedrijfs- of organisatiecloud via Azure Active Directory en vereenvoudigt de toegang tot apps en bronnen.

- [Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) biedt SSO en veilige externe toegang voor webtoepassingen die on-premises worden gehost.

## <a name="next-steps"></a>Volgende stappen

- Begrijp uw [gedeelde verantwoordelijkheid in de cloud.](shared-responsibility.md)

- Ontdek hoe [Azure Security Center](https://azure.microsoft.com/services/security-center/) u kan helpen bedreigingen te voorkomen, detecteren en erop te reageren met meer zichtbaarheid en controle over de beveiliging van uw Azure-resources.
