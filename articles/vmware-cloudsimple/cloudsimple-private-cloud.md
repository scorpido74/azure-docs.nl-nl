---
title: Azure VMware-oplossingen (AVS)-persoonlijke Clouds van AVS
description: Meer informatie over uw persoonlijke Clouds en concepten van AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024942"
---
# <a name="avs-private-cloud-overview"></a>Overzicht van de persoonlijke cloud van AVS

Met AVS worden VMware-workloads binnen enkele minuten getransformeerd en uitgebreid naar open bare Clouds. Met de AVS-service kunt u VMware systeem eigen implementeren op een Azure bare-metal infra structuur. Uw implementatie bevindt zich op Azure-locaties en integreert volledig met de rest van de Azure-Cloud.

De AVS-oplossing biedt volledige VMware-operationele continuïteit. Deze oplossing biedt u de voor delen van de open bare cloud van:

* Elasticiteit
* Innovatie
* Efficiëntie

Met AVS profiteert u van een model voor Cloud verbruik dat uw total cost of ownership verlaagt. Het biedt ook mogelijkheden voor inrichting op aanvraag, betalen per gebruik en capaciteits optimalisatie.

AVS is volledig compatibel met:

* Bestaande hulpprogram ma's
* Lacune
* Processen

Met deze compatibiliteit kunnen uw teams werk belastingen op de Azure-Cloud beheren zonder dat dit soort beleid wordt verstoord:

* Netwerk
* Beveiliging  
* Databeveiliging  
* Controleren

AVS beheert de infra structuur en alle benodigde netwerk-en beheer Services. Met de AVS-service kan uw team zich richten op het volgende:

* Bedrijfs waarde
* Toepassing inrichten
* Bedrijfscontinuïteit
* Ondersteuning
* Beleidsafdwinging

## <a name="avs-private-cloud-environment-overview"></a>Overzicht van de persoonlijke cloud omgeving van AVS

Een AVS-Privécloud is een geïsoleerde VMware-stack die ondersteuning biedt voor:

* ESXi-hosts
* vCenter
* vSAN
* NSX

Persoonlijke Clouds van AVS worden beheerd via de AVS-Portal. Ze hebben hun eigen vCenter-Server in een eigen beheer domein.

De stack wordt uitgevoerd op:

* Toegewezen knooppunten
* Geïsoleerde bare metal-hardwareprofielen

Gebruikers gebruiken de stack via systeem eigen VMware-hulpprogram ma's, waaronder:

* vCenter
* NSX Manager

U kunt toegewezen knoop punten implementeren op Azure-locaties. Vervolgens kunt u ze beheren met Azure en AVS. Een automatische AVS-Cloud bestaat uit een of meer vSphere-clusters en elk cluster bevat 3 tot 16 knoop punten.

U kunt een Privécloud maken met behulp van ingekochte, betalen naar gebruik-knoop punten of gereserveerde, toegewezen knoop punten.

U kunt de automatische AVS-cloud verbinden met uw on-premises omgeving en het Azure-netwerk met behulp van de volgende verbindingen:

* Veilig
* Particuliere VPN
* ExpressRoute van Azure

De cloud omgeving van de AVS is ontworpen om individuele storings punten te elimineren:

* ESXi-clusters zijn geconfigureerd met een hoge Beschik baarheid van vSphere en hebben een grootte van ten minste één reserve knooppunt voor tolerantie.
* vSAN biedt redundante primaire opslag. vSan vereist ten minste drie knoop punten om bescherming te bieden tegen één fout. U kunt vSAN configureren om hogere toleranties voor grotere clusters te bieden.
* U kunt vCenter-, PSC-en NSX-beheer machines configureren met RAID-10-opslag beleid om te beschermen tegen opslag storingen. vSphere HA beschermt op knoop punten en netwerk fouten.

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>Scenario's voor het implementeren van een AVS-Privécloud

Hier volgen enkele voor beelden van gebruiks voorbeelden voor de implementatie van een Privécloud in de Cloud.

### <a name="data-center-retirement-or-migration"></a>Buiten gebruik stellen of migreren van data centers

* Krijg extra capaciteit wanneer u de limieten bereikt van uw bestaande Data Center of hardware vernieuwt.
* Voeg de benodigde capaciteit toe in de Cloud en Elimineer het beheer van de hardware vernieuwt.
* Verminder het risico en de kosten voor Cloud migraties vergeleken met tijdrovende conversies of herarchitectuur.
* Gebruik vertrouwde VMware-hulpprogram ma's en-vaardig heden om Cloud migraties te versnellen. Gebruik Azure-Services in de cloud om uw toepassingen in uw tempo te moderniseren.

### <a name="expand-on-demand"></a>Op aanvraag uitvouwen

* Breid uit naar de cloud om te voldoen aan niet-verwachte behoeften, zoals nieuwe ontwikkelings omgevingen of bursts met een seizoen capaciteit.
* Maak op aanvraag nieuwe capaciteit en bewaar deze alleen zolang u dat nodig hebt.
* Verminder uw investering in de voor grond, Versnel snelheid van het inrichten en verminder de complexiteit met dezelfde architectuur en hetzelfde beleid in zowel on-premises als in de Cloud.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Herstel na nood gevallen en virtuele Bureau bladen in de Azure-Cloud

* Externe toegang tot gegevens, apps en Bureau bladen in de Azure-Cloud tot stand brengen. Met verbindingen met een hoge band breedte kunt u snel gegevens uploaden/downloaden om van incidenten te herstellen. Netwerken met lage latentie bieden snelle reactie tijden die gebruikers verwachten van een desktop-app.

* Repliceer al uw beleids regels en netwerken in de Cloud met behulp van de AVS-Portal en vertrouwde VMware-hulpprogram ma's. Replicatie vermindert de inspanning en het risico van het maken en beheren van DR-en VDI-implementaties.

### <a name="high-performance-applications-and-databases"></a>Toepassingen en data bases met hoge prestaties

* Voer uw meest veeleisende workloads uit met de hypergeconvergeerd-architectuur die door AVS wordt verschaft.
* Voer Oracle, micro soft SQL Server, middleware Systems en no-SQL-data bases met hoge prestaties uit.
* Ervaar de Cloud als uw eigen Data Center met hoge snelheid van 25 Gbps netwerk verbindingen. Met verbindingen met hoge snelheid kunt u hybride apps uitvoeren die on-premises, VMware op Azure en persoonlijke werk belastingen van Azure omvatten, zonder de prestaties te verbeteren.

### <a name="true-hybrid"></a>Waar hybride

* Verdeel DevOps tussen VMware en Azure-Services.
* Optimaliseer VMware-beheer voor Azure-Services en-oplossingen die in al uw workloads kunnen worden toegepast.
* Toegang tot open bare Cloud Services zonder uw Data Center uit te breiden of uw toepassingen opnieuw te ontwerpen.
* Centraliseer identiteiten, Toegangs beheer beleid, logboek registratie en controle voor VMware-toepassingen op Azure.

## <a name="limits"></a>Beperkingen

De volgende tabel bevat de limieten voor knoop punten van resources van een Privécloud in de Cloud.

| Bron | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een AVS-Privécloud | 3 |
| Maximum aantal knoop punten in een cluster op een Privécloud-Cloud | 16 |
| Maximum aantal knoop punten in een Privécloud in de Cloud | 64 |
| Minimum aantal knoop punten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een AVS-privécloud](create-private-cloud.md)
* Meer informatie over het [configureren van een persoonlijke cloud omgeving van een AVS](quickstart-create-private-cloud.md)
