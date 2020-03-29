---
title: Informatie over netwerkbewaking in Azure-monitorlogboeken | Microsoft Documenten
description: Overzicht van netwerkbewakingsoplossingen, waaronder NPM, om netwerken te beheren in cloud-, on-premises en hybride omgevingen.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: 2912488286745bf8d2e567d09e445b0a44dc7c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67672179"
---
# <a name="network-monitoring-solutions"></a>Oplossingen voor netwerkbewaking 

Azure biedt een groot aantal oplossingen om uw netwerkassets te bewaken. Azure heeft oplossingen en hulpprogramma's om de netwerkconnectiviteit, de status van ExpressRoute-circuits te bewaken en netwerkverkeer in de cloud te analyseren.

## <a name="network-performance-monitor-npm"></a>Netwerkprestatiemonitor (NPM)

Network Performance Monitor (NPM) is een reeks mogelijkheden, die elk zijn gericht op het bewaken van de status van uw netwerk, netwerkconnectiviteit met uw toepassingen en biedt inzicht in de prestaties van uw netwerk. NPM is cloudgebaseerd en biedt een hybride netwerkbewakingsoplossing die de connectiviteit tussen:
 
* Cloudimplementaties en on-premises locaties
* Meerdere datacenters en bijkantoren
* Bedrijfskritische toepassingen met meerdere lagen/microservices
* Gebruikerslocaties en webgebaseerde toepassingen (HTTP/HTTP's) 

Performance Monitor, ExpressRoute Monitor en Service Connectivity Monitor zijn monitoringmogelijkheden binnen NPM en worden hieronder beschreven.

## <a name="performance-monitor"></a>Prestatiemeter

Performance Monitor is onderdeel van NPM en is netwerkbewaking voor cloud-, hybride- en on-premises omgevingen. U de netwerkconnectiviteit in externe filialen en veldkantoren, winkellocaties, datacenters en clouds controleren. U netwerkproblemen detecteren voordat uw gebruikers een klacht indienen. De belangrijkste voordelen zijn:

* Verlies en latentie bewaken in verschillende subnetten en waarschuwingen instellen
* Alle paden (inclusief redundante paden) op het netwerk bewaken
* Tijdelijke en point-in-time netwerkproblemen oplossen die moeilijk te repliceren zijn
* Bepaal het specifieke segment op het netwerk, dat verantwoordelijk is voor gedegradeerde prestaties
* De status van het netwerk bewaken, zonder dat snmp nodig is

![NPM-topologiekaart](./media/network-monitoring-overview/npm-topology-map.png) 

Bekijk voor meer informatie de volgende artikelen:

* [Een oplossing voor netwerkprestatiemonitor configureren in Azure Monitor-logboeken](../azure-monitor/insights/network-performance-monitor.md) 
* [Gebruiksvoorbeelden](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Productupdates:
  * [Februari 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Augustus 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute-monitor

NPM voor ExpressRoute biedt uitgebreide ExpressRoute-monitoring voor Azure Private peering- en Microsoft-peeringverbindingen. U de E2E-connectiviteit en -prestaties tussen uw filialen en Azure via ExpressRoute bewaken. De belangrijkste mogelijkheden zijn:

* Automatische detectie van ER-circuits die aan uw abonnement zijn gekoppeld
* Detectie van netwerktopologie van on-premises tot uw cloudtoepassingen
* Capaciteitsplanning, bandbreedtegebruiksanalyse
* Monitoring en waarschuwing op zowel primaire als secundaire paden
* Controle van de connectiviteit met Azure-services zoals Office 365, Dynamics 365, ... over ExpressRoute
* De afbraak van connectiviteit met VNets detecteren

![Geokaart met verkeer tussen regio's](./media/network-monitoring-overview/expressroute-topology-map.png) 

Raadpleeg voor meer informatie de volgende artikelen:

* [Netwerkprestatiemeter configureren voor ExpressRoute](../expressroute/how-to-npm.md)
* [Blogbericht](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Bewaking van serviceverbinding

Met Service Connectivity monitoring u nu de bereikbaarheid van toepassingen testen en prestatieknelpunten detecteren in on-premises, carriernetwerken en cloud/private datacenters.

* End-to-end netwerkconnectiviteit met toepassingen bewaken
* Correleer de levering van toepassingen met netwerkprestaties, detecteer nauwkeurige locatie van degradatie langs het pad tussen de gebruiker en de toepassing
* Bereikbaarheid van toepassingen testen vanaf meerdere gebruikerslocaties over de hele wereld
* Netwerklatentie en pakketverlies bepalen voor uw branche en SaaS-toepassingen
* Bepaal hotspots op het netwerk, die kunnen leiden tot slechte prestaties van toepassingen
* De bereikbaarheid van Office 365-toepassingen bewaken met behulp van ingebouwde tests voor Microsoft Office 365, Dynamics 365, Skype voor Bedrijven en andere Microsoft-services

Raadpleeg voor meer informatie de volgende artikelen:

* [Netwerkprestatiemeter configureren voor het bewaken van serviceeindpunten](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogbericht](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Traffic Analytics
Traffic Analytics is een cloudgebaseerde oplossing die inzicht biedt in de activiteit van gebruikers en toepassingen op uw cloudnetwerken. NSG Flow logs worden geanalyseerd om inzicht te geven in:

* Verkeersstromen over uw netwerken tussen Azure en internet, openbare cloudregio's, VNET's en subnetten
* Toepassingen en protocollen op uw netwerk, zonder de noodzaak van sniffers of speciale flow collector-apparaten
* Top praters, spraakzame applicaties, VM-gesprekken in de cloud, traffic hotspots
* Bronnen en bestemmingen van het verkeer over VNET's, onderlinge relaties tussen kritieke zakelijke diensten en toepassingen
* Beveiliging - kwaadwillig verkeer, poorten open voor het internet, toepassingen of VM's proberen toegang tot internet ...
* Capaciteitsbenutting - helpt u problemen van overprovisioning of onderbenutting te elimineren door de gebruikstrends van VPN-gateways en andere services te volgen

Traffic Analytics voorziet u van bruikbare informatie waarmee u de netwerkactiviteit van uw organisatie controleren, toepassingen en gegevens beveiligen, de werkbelastingprestaties optimaliseren en compliant blijven.

![Geokaart met verkeer tussen regio's](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Gerelateerde links:
* [Blogpost](https://aka.ms/trafficanalytics), [Documentatie](https://aka.ms/trafficanalyticsdocs), [FAQ](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-analyse
Deze oplossing is gebouwd voor DNS-beheerders en verzamelt, analyseert en correleert DNS-logboeken om beveiligings-, operationele en prestatiegerelateerde inzichten te bieden.  Enkele van de mogelijkheden zijn:

* Identificatie van clients die proberen op te lossen in schadelijke domeinen
* Identificatie van verouderde bronrecords
* Inzicht in veelgevraagde domeinnamen en spraakzame DNS-clients
* Inzicht in de aanvraagbelasting op DNS-servers
* Controle van dynamische DNS-registratiefouten

![DNS Analytics-dashboard](./media/network-monitoring-overview/dns-analytics-overview.png) 

Gerelateerde links:
* [Blogpost](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Documentatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Diversen

* [Nieuwe prijzen](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
