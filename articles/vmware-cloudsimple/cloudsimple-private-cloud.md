---
title: Azure VMware-oplossing door CloudSimple - Private Clouds
description: Meer informatie over CloudSimple Private Clouds en concepten.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024942"
---
# <a name="cloudsimple-private-cloud-overview"></a>Overzicht van CloudSimple Private Cloud

CloudSimple transformeert en breidt VMware-workloads binnen enkele minuten uit naar openbare clouds. Met de CloudSimple-service u VMware native implementeren op azure bare metal-infrastructuur. Uw implementatie leeft op Azure-locaties en integreert volledig met de rest van de Azure-cloud.

De CloudSimple-oplossing biedt volledige operationele continuïteit van VMware. Deze oplossing biedt u de voordelen van de public cloud van:

* Elasticiteit
* Innovatie
* Efficiëntie

Met CloudSimple profiteert u van een cloudconsumptiemodel dat uw totale eigendomskosten verlaagt. Het biedt ook on-demand provisioning, pay-as-you-grow en capaciteitsoptimalisatie.

CloudSimple is volledig compatibel met:

* Bestaande hulpprogramma's
* Vaardigheden
* Processen

Met deze compatibiliteit kunnen uw teams workloads beheren in de Azure-cloud, zonder dit soort beleidsregels te verstoren:

* Netwerk
* Beveiliging  
* Gegevensbeveiliging  
* Controleren

CloudSimple beheert de infrastructuur en alle benodigde netwerk- en beheerdiensten. De CloudSimple-service stelt uw team in staat zich te concentreren op:

* Bedrijfswaarde
* Toepassingsinrichting
* Bedrijfscontinuïteit
* Ondersteuning
* Beleidsafdwinging

## <a name="private-cloud-environment-overview"></a>Overzicht van de Private Cloud-omgeving

Een Private Cloud is een geïsoleerde VMware-stack die ondersteunt:

* ESXi-hosts
* vCenter
* vSAN
* NSX (NSX)

Private Clouds worden beheerd via de CloudSimple portal. Ze hebben hun eigen vCenter server in een eigen beheerdomein.

De stapel draait op:

* Toegewezen knooppunten
* Geïsoleerde bare metal hardwareknooppunten

Gebruikers gebruiken de stack via native VMware-tools, waaronder:

* vCenter
* NSX Manager

U dedicated nodes implementeren in Azure-locaties. Vervolgens u ze beheren met Azure en CloudSimple. Een Private Cloud bestaat uit een of meer vSphere-clusters en elk cluster bevat 3 tot 16 knooppunten.

U een private cloud maken met gekochte, betalenper-je-gaan-knooppunten of gereserveerde, speciale knooppunten.

U de Private Cloud verbinden met uw on-premises omgeving en het Azure-netwerk via de volgende verbindingen:

* Beveiligen
* Privé VPN
* Azure ExpressRoute

De Private Cloud-omgeving is ontworpen om afzonderlijke uitvalpunten te elimineren:

* ESXi-clusters zijn geconfigureerd met vSphere-hoge beschikbaarheid en hebben een formaat om ten minste één reserveknooppunt voor tolerantie te hebben.
* vSAN biedt redundante primaire opslag. vSan vereist ten minste drie knooppunten om bescherming te bieden tegen een enkele storing. U vSAN configureren om een hogere tolerantie te bieden voor grotere clusters.
* U vCenter-, PSC- en NSX-beheerVM's configureren met RAID-10-opslagbeleid om te beschermen tegen opslagfouten. vSphere HA beschermt tegen knooppunt- en netwerkstoringen.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenario's voor het implementeren van een Private Cloud

Hier volgen enkele voorbeeldvoorbeelden voor Private Cloud-implementatie.

### <a name="data-center-retirement-or-migration"></a>Pensioen of migratie van datacenters

* Profiteer van extra capaciteit wanneer u de limieten van uw bestaande datacenter bereikt of hardware vernieuwt.
* Voeg de benodigde capaciteit toe in de cloud en elimineer de hoofdpijn van het beheren van hardwarevernieuwingen.
* Verlaag het risico en de kosten van cloudmigraties in vergelijking met tijdrovende conversies of rearchitectuur.
* Gebruik bekende VMware-tools en -vaardigheden om cloudmigraties te versnellen. Gebruik in de cloud Azure-services om uw toepassingen op uw tempo te moderniseren.

### <a name="expand-on-demand"></a>Uitbreiden op aanvraag

* Breid uit naar de cloud om te voldoen aan onverwachte behoeften, zoals nieuwe ontwikkelomgevingen of seizoenscapaciteitsuitbarstingen.
* Creëer nieuwe capaciteit op aanvraag en bewaar deze alleen zolang als u die nodig hebt.
* Verlaag uw investering vooraf, versnel de beprovisioningsnelheid en verminder de complexiteit met dezelfde architectuur en hetzelfde beleid in zowel on-premises als de cloud.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Disaster recovery en virtuele desktops in de Azure-cloud

* Externe toegang tot gegevens, apps en bureaubladen instellen in de Azure-cloud. Met verbindingen met een hoge bandbreedte uploadt/ downloadt u snel gegevens om te herstellen van incidenten. Netwerken met lage latentie bieden u snelle responstijden die gebruikers verwachten van een desktop-app.

* Repliceer al uw beleid en netwerken in de cloud met behulp van de CloudSimple-portal en bekende VMware-tools. Replicatie vermindert de inspanning en het risico van het maken en beheren van DR- en VDI-implementaties.

### <a name="high-performance-applications-and-databases"></a>Krachtige toepassingen en databases

* Voer uw meest veeleisende workloads uit met de hyperconverged architectuur van CloudSimple.
* Voer Oracle, Microsoft SQL-server, middleware-systemen en krachtige no-SQL-databases uit.
* Ervaar de cloud als uw eigen datacenter met snelle 25-Gbps netwerkverbindingen. Met snelle verbindingen u hybride apps uitvoeren die on-premises, VMware op Azure en Azure-privéworkloads omvatten, zonder dat dit ten koste gaat van de prestaties.

### <a name="true-hybrid"></a>Echte hybride

* Unify DevOps voor VMware- en Azure-services.
* Optimaliseer VMware-beheer voor Azure-services en -oplossingen die kunnen worden toegepast op al uw workloads.
* Krijg toegang tot openbare cloudservices zonder dat u uw datacenter hoeft uit te breiden of uw toepassingen opnieuw hoeft te ontwerpen.
* Identiteiten, toegangscontrolebeleid, logboekregistratie en bewaking voor VMware-toepassingen op Azure centraliseren.

## <a name="limits"></a>Limieten

In de volgende tabel worden de knooppuntlimieten voor resources van een private cloud weergegeven.

| Resource | Limiet |
|----------|-------|
| Minimum aantal knooppunten om een private cloud te maken | 3 |
| Maximaal aantal knooppunten in een cluster op een private cloud | 16 |
| Maximaal aantal knooppunten in een private cloud | 64 |
| Minimumaantal knooppunten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een private cloud](create-private-cloud.md)
* Meer informatie over het [configureren van een Private Cloud-omgeving](quickstart-create-private-cloud.md)
