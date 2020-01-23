---
title: Veelgestelde vragen over Azure Network Watcher | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Network Watcher-service.
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
ms.openlocfilehash: 856c249b72e9e0ff8667d10821ad14b3432b0775
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509186"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Veelgestelde vragen over Azure Network Watcher
De [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -service biedt een reeks hulpprogram ma's voor het bewaken, diagnosticeren, weer geven van metrische gegevens en het in-of uitschakelen van Logboeken voor bronnen in een virtueel Azure-netwerk. In dit artikel vindt u antwoorden op veelgestelde vragen over de service.

## <a name="general"></a>Algemeen

### <a name="what-is-network-watcher"></a>Wat is Network Watcher?
Network Watcher is ontworpen om de netwerk status van IaaS-onderdelen (Infrastructure-as-a-Service) te controleren en te herstellen, waaronder Virtual Machines, virtuele netwerken, toepassings gateways, load balancers en andere bronnen in een virtueel Azure-netwerk. Het is geen oplossing voor het bewaken van de PaaS-infra structuur (platform-as-a-Service) of voor het ophalen van Web/Mobile-analyses.

### <a name="what-tools-does-network-watcher-provide"></a>Welke hulpprogram ma's Network Watcher bieden?
Network Watcher biedt drie belang rijke sets mogelijkheden
* Controleren
  * In de [topologie weergave](https://docs.microsoft.com/azure/network-watcher/view-network-topology) ziet u de resources in uw virtuele netwerk en de relaties daartussen.
  * Met [verbindings monitor](https://docs.microsoft.com/azure/network-watcher/connection-monitor) kunt u de connectiviteit en latentie tussen een virtuele machine en een andere netwerk bron bewaken.
  * Met [netwerk prestatie meter](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) kunt u connectiviteit en latentie bewaken over hybride netwerk architecturen, Expressroute-circuits en service/toepassings eindpunten.  
* Diagnostics
  * Met [IP-stroom controle](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) kunt u problemen met verkeer filteren op een VM-niveau detecteren.
  * [Volgende hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) helpt u bij het controleren van verkeers routes en het detecteren van routerings problemen.
  * [Met verbindings problemen oplossen](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) kunt u een eenmalige verbinding en latentie controle tussen een virtuele machine en een andere netwerk bron.
  * Met [pakket opname](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) kunt u al het verkeer op een VM in uw virtuele netwerk vastleggen.
  * [Met VPN-problemen oplossen](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) voert u meerdere diagnostische controles uit op uw VPN-gateways en verbindingen om problemen met de fout op te lossen.
* Logboekregistratie
  * Met [NSG-stroom logboeken](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) kunt u al het verkeer in uw [netwerk beveiligings groepen registreren (nsg's)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) verwerkt de gegevens van uw NSG-stroom logboek om uw netwerk verkeer te visualiseren, op te vragen, te analyseren en te begrijpen.


Zie de [overzichts pagina van Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)voor meer informatie.


### <a name="how-does-network-watcher-pricing-work"></a>Hoe werkt Network Watcher prijzen?
Ga naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/network-watcher/) voor Network Watcher onderdelen en hun prijzen.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Welke regio's worden Network Watcher ondersteund/beschikbaar in?
U kunt de nieuwste regionale Beschik baarheid bekijken op de [pagina Beschik baarheid van Azure-service](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="what-are-resource-limits-on-network-watcher"></a>Wat zijn de limieten voor resources op Network Watcher?
Zie de pagina met [service beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) voor alle limieten.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Waarom is er slechts één exemplaar van Network Watcher per regio toegestaan?
Network Watcher hoeft alleen maar één keer te worden ingeschakeld voor een abonnement, maar dit is geen service limiet.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Waarom moet ik de Network Watcher-extensie installeren? 
De uitbrei ding Network Watcher is vereist voor elke functie die verkeer van een virtuele machine moet genereren of onderscheppen. 

### <a name="which-features-require-the-network-watcher-extension"></a>Voor welke functies is de Network Watcher-extensie vereist?
Alleen pakket opname, verbindings problemen oplossen en verbindings monitor moeten de Network Watcher extensie bevatten.

## <a name="nsg-flow-logs"></a>NSG-stroom logboeken

### <a name="what-does-nsg-flow-logs-do"></a>Wat gebeurt er met NSG-stroom logboeken?
Azure-netwerk bronnen kunnen worden gecombineerd en beheerd via [netwerk beveiligings groepen (nsg's)](https://docs.microsoft.com/azure/virtual-network/security-overview). Met NSG-stroom Logboeken kunt u 5-tuple-stroom gegevens registreren over al het verkeer via uw Nsg's. De onbewerkte stroom logboeken worden naar een Azure Storage-account geschreven, waar ze verder kunnen worden verwerkt, geanalyseerd, opgevraagd of geëxporteerd als dat nodig is.

### <a name="how-do-i-use-nsg-flow-logs-on-a-storage-account-with-a-firewall"></a>Hoe kan ik NSG-stroom Logboeken gebruiken op een opslag account met een firewall?

Als u een opslag account met een firewall wilt gebruiken, moet u een uitzonde ring voor vertrouwde micro soft-Services voor toegang tot uw opslag account opgeven:

* Navigeer naar het opslag account door de naam van het opslag account in de globale zoek opdracht op de portal of op de [pagina opslag accounts](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) te typen.
* Selecteer in de sectie **INSTELLINGEN** de optie **Firewalls en virtuele netwerken**
* Selecteer in ' toegang toestaan vanaf ' **geselecteerde netwerken**. Tik vervolgens onder **uitzonde ringen**op het vakje naast **' vertrouwde micro soft-Services toegang geven tot dit opslag account '** 
* Als deze optie al is geselecteerd, is er geen wijziging nodig.  
* Zoek uw doel-NSG op de [overzichts pagina van de NSG-stroom logboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) en schakel NSG-stroom Logboeken in met het hierboven geselecteerde opslag account.

U kunt de Storage-logboeken na enkele minuten controleren. U ziet dan een bijgewerkte tijdstempel of een nieuw JSON-bestand.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints-for-storage"></a>Hoe kan ik NSG-stroom Logboeken gebruiken met Service-eind punten voor opslag?

Raadpleeg de [zelf studie over het inschakelen van service-eind punten](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint). 


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Wat is het verschil tussen stroom logboeken versie 1 & 2?
Stroom logboeken versie 2 introduceert het concept van de *stroom status* & slaat informatie op over verzonden bytes en pakketten. [Meer informatie](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Volgende stappen
 - Ga naar de [pagina overzicht](https://docs.microsoft.com/azure/network-watcher/) van de documentatie voor enkele zelf studies om aan de slag te gaan met Network Watcher.
