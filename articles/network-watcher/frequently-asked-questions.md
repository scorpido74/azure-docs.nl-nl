---
title: Veelgestelde vragen (FAQ) over Azure Network Watcher | Microsoft Documenten
description: In dit artikel worden veelgestelde vragen over de Azure Network Watcher-service beantwoord.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471853"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Veelgestelde vragen (FAQ) over Azure Network Watcher
De [Azure Network Watcher-service](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) biedt een reeks hulpprogramma's voor het bewaken, diagnosticeren, bekijken van statistieken en het in- of uitschakelen van logboeken voor resources in een virtueel Azure-netwerk. In dit artikel worden veelgestelde vragen over de service beantwoord.

## <a name="general"></a>Algemeen

### <a name="what-is-network-watcher"></a>Wat is Network Watcher?
Network Watcher is ontworpen om de netwerkstatus van IaaS-componenten (Infrastructure-as-a-Service) te bewaken en te herstellen, waaronder virtuele machines, virtuele netwerken, toepassingsgateways, load-balancers en andere bronnen in een virtueel Azure-netwerk. Het is geen oplossing voor het monitoren van PaaS (Platform-as-a-Service) infrastructuur of het verkrijgen van web/mobile analytics.

### <a name="what-tools-does-network-watcher-provide"></a>Welke tools biedt Network Watcher?
Network Watcher biedt drie belangrijke sets van mogelijkheden
* Bewaking
  * [De topologieweergave](https://docs.microsoft.com/azure/network-watcher/view-network-topology) toont u de bronnen in uw virtuele netwerk en de relaties tussen deze netwerken.
  * [Met Connection Monitor](https://docs.microsoft.com/azure/network-watcher/connection-monitor) u de connectiviteit en latentie tussen een VM en een andere netwerkbron controleren.
  * [Met de netwerkprestatiemonitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) u de connectiviteit en latencies bewaken voor hybride netwerkarchitecturen, Expressroute-circuits en service-/toepassingseindpunten.  
* Diagnostiek
  * [Met IP Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) u problemen met het filteren van verkeer op VM-niveau detecteren.
  * [Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) helpt u bij het verifiëren van verkeersroutes en het detecteren van routeringsproblemen.
  * [Verbindingsproblemen](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) maken een eenmalige connectiviteits- en latentiecontrole mogelijk tussen een VM en een andere netwerkbron.
  * [Packet Capture](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) stelt u in staat om al het verkeer op een VM in uw virtuele netwerk vast te leggen.
  * [VPN Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) voert meerdere diagnostische controles uit op uw VPN-gateways en verbindingen om problemen met foutopsporing te helpen.
* Logboekregistratie
  * [Met NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) u al het verkeer registreren in uw [Network Security Groups (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) verwerkt uw NSG Flow Log-gegevens waarmee u uw netwerkverkeer visualiseren, opvragen, analyseren en begrijpen.


Zie de [overzichtspagina van Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)voor meer gedetailleerde informatie.


### <a name="how-does-network-watcher-pricing-work"></a>Hoe werkt de prijzen van Network Watcher?
Ga naar [de prijspagina](https://azure.microsoft.com/pricing/details/network-watcher/) voor network watcher-componenten en de prijzen ervan.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>In welke regio's wordt Network Watcher ondersteund/beschikbaar in welke regio's?
U de nieuwste regionale beschikbaarheid bekijken op de [beschikbaarheidspagina van Azure Service](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Welke machtigingen zijn nodig om Network Watcher te gebruiken?
Bekijk de lijst met [RBAC-machtigingen die nodig zijn om Network Watcher te gebruiken.](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions) Voor het implementeren van resources hebt u machtigingen voor bijdragen aan de NetworkWatcherRG nodig (zie hieronder).

### <a name="how-do-i-enable-network-watcher"></a>Hoe kan ik Network Watcher inschakelen?
De Network Watcher-service is [automatisch ingeschakeld](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) voor elk abonnement.

### <a name="what-is-the-network-watcher-deployment-model"></a>Wat is het implementatiemodel van Network Watcher?
De bovenliggende bron Network Watcher wordt geïmplementeerd met een unieke instantie in elke regio. Naamgevingsindeling: NetworkWatcher_RegionName. Voorbeeld: NetworkWatcher_centralus is de Netwerkwatcher-bron voor de regio 'Centraal VS'.

### <a name="what-is-the-networkwatcherrg"></a>Wat is de NetworkWatcherRG?
Network Watcher-bronnen bevinden zich in de verborgen **NetworkWatcherRG-brongroep** die automatisch wordt gemaakt. De NSG Flow Logs-bron is bijvoorbeeld een onderliggende bron van Network Watcher en is ingeschakeld in de NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Waarom moet ik de Network Watcher-extensie installeren? 
De Network Watcher-extensie is vereist voor elke functie die verkeer van een vm moet genereren of onderscheppen. 

### <a name="which-features-require-the-network-watcher-extension"></a>Welke functies vereisen de Network Watcher-extensie?
De functies Packet Capture, Connection Troubleshoot en Connection Monitor hebben de Network Watcher-extensie nodig.

### <a name="what-are-resource-limits-on-network-watcher"></a>Wat zijn resourcelimieten voor Network Watcher?
Zie de pagina [Servicelimieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) voor alle limieten.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Waarom is slechts één exemplaar van Network Watcher toegestaan per regio? 
Network Watcher hoeft alleen maar eenmaal worden ingeschakeld voor een abonnement voor zijn functies om te werken, dit is een niet een service limiet.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Hoe kan ik de Network Watcher Resource beheren? 
De Network Watcher-bron vertegenwoordigt de backendservice voor Network Watcher en wordt volledig beheerd door Azure. Klanten hoeven het niet te beheren. Bewerkingen zoals verplaatsen worden niet ondersteund op de resource. De [bron kan](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal)echter worden verwijderd . 

## <a name="nsg-flow-logs"></a>NSG-stroomlogboeken

### <a name="what-does-nsg-flow-logs-do"></a>Wat doet NSG Flow Logs?
Azure-netwerkbronnen kunnen worden gecombineerd en beheerd via [Network Security Groups (NSGs).](https://docs.microsoft.com/azure/virtual-network/security-overview) Met NSG Flow Logs u 5-tuple flow informatie registreren over al het verkeer via uw NSG's. De raw flow logs worden naar een Azure Storage-account geschreven van waaruit ze naar behoefte verder kunnen worden verwerkt, geanalyseerd, opgevraagd of geëxporteerd.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Hoe gebruik ik NSG Flow Logs met een Storage account achter een firewall?

Als u een opslagaccount achter een firewall wilt gebruiken, moet u een uitzondering maken voor Trusted Microsoft Services om toegang te krijgen tot uw opslagaccount:

* Navigeer naar het opslagaccount door de naam van het opslagaccount te typen in de globale zoekopdracht op de portal of op de [pagina Opslagaccounts](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Selecteer in de sectie **INSTELLINGEN** de optie **Firewalls en virtuele netwerken**
* Selecteer Geselecteerde netwerken selecteren in 'Toegang toestaan **vanuit'**. Vink vervolgens onder **Uitzonderingen**het vakje aan naast **'Vertrouwde Microsoft-services toestaan toegang te krijgen tot dit opslagaccount'.** 
* Als deze optie al is geselecteerd, is er geen wijziging nodig.  
* Zoek uw doel NSG op de [overzichtspagina van NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) en schakel NSG Flow Logs in met het bovenstaande opslagaccount geselecteerd.

U kunt de Storage-logboeken na enkele minuten controleren. U ziet dan een bijgewerkte tijdstempel of een nieuw JSON-bestand.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Hoe gebruik ik NSG Flow Logs met een Opslagaccount achter een Service Endpoint?

NSG Flow Logs zijn compatibel met Service Endpoints zonder extra configuratie. Zie de [zelfstudie over het inschakelen van Service-eindpunten](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) in uw virtuele netwerk.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Wat is het verschil tussen flow logs versies 1 & 2?
Flow Logs versie 2 introduceert het concept van *Flow State* & slaat informatie op over verzonden bytes en pakketten. [Lees meer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Volgende stappen
 - Ga naar onze [documentatieoverzichtspagina](https://docs.microsoft.com/azure/network-watcher/) voor een aantal tutorials om je op weg te helpen met Network Watcher.
