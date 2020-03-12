---
title: NSG-service tags (netwerk beveiligings groep) voor Azure HDInsight
description: Gebruik HDInsight-service tags om inkomend verkeer naar uw cluster toe te staan van de knoop punten HDInsight Health en Management Services, zonder expliciet IP-adressen toe te voegen aan uw netwerk beveiligings groepen.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117233"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>NSG-service tags (netwerk beveiligings groep) voor Azure HDInsight

HDInsight-service tags voor netwerk beveiligings groepen (Nsg's) zijn groepen met IP-adressen voor status-en beheer Services. Deze groepen helpen de complexiteit te minimaliseren voor het maken van de beveiligings regel. [Service Tags](../virtual-network/security-overview.md#service-tags) bieden een alternatieve methode voor het toestaan van inkomend verkeer van specifieke IP-adressen zonder dat u elk van de [beheer-IP-adressen](hdinsight-management-ip-addresses.md) in uw netwerk beveiligings groepen hoeft in te voeren.

Deze service tags worden gemaakt en beheerd door de HDInsight-service. U kunt geen eigen servicetag maken of een bestaande tag wijzigen. Micro soft beheert de adres voorvoegsels die overeenkomen met het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen.

## <a name="getting-started-with-service-tags"></a>Aan de slag met Service Tags

U hebt twee opties voor het gebruik van service tags in uw netwerk beveiligings groepen:

1. Eén HDInsight-service label gebruiken: met deze optie wordt het virtuele netwerk geopend voor alle IP-adressen die de HDInsight-service gebruikt om clusters in alle regio's te bewaken. Deze optie is de eenvoudigste methode, maar is mogelijk niet geschikt als u strenge beveiligings vereisten hebt.

1. Meerdere regionale service tags gebruiken: met deze optie wordt het virtuele netwerk alleen geopend voor de IP-adressen die door HDInsight worden gebruikt in die specifieke regio. Als u echter meerdere regio's gebruikt, moet u meerdere service Tags toevoegen aan het virtuele netwerk.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Eén algemeen HDInsight-service label gebruiken

De eenvoudigste manier om te beginnen met het gebruik van service tags met uw HDInsight-cluster is het toevoegen van de globale tag `HDInsight` aan een regel voor een netwerk beveiligings groep.

1. Selecteer uw netwerk beveiligings groep in het [Azure Portal](https://portal.azure.com/).

1. Selecteer bij **instellingen**de optie **regels voor binnenkomende beveiliging**en selecteer vervolgens **+ toevoegen**.

1. Selecteer in de vervolg keuzelijst **bron** de optie **service label**.

1. Selecteer **HDInsight**in de vervolg keuzelijst **bron service label** .

    ![Service label Azure Portal toevoegen](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Deze tag bevat de IP-adressen van de status-en beheer Services voor alle regio's waar HDInsight beschikbaar is, en zorgt ervoor dat uw cluster kan communiceren met de benodigde status-en beheer Services, ongeacht waar deze zijn gemaakt.

## <a name="use-regional-hdinsight-service-tags"></a>Regionale HDInsight-service tags gebruiken

Als optie Eén niet werkt omdat u meer beperkende machtigingen nodig hebt, kunt u alleen de service Tags toestaan die van toepassing zijn op uw regio. De toepasselijke service tags kunnen een, twee of drie service tags zijn, afhankelijk van de regio waar uw cluster is gemaakt.

Lees de volgende secties van het document om erachter te komen welke service tags voor uw regio moeten worden toegevoegd.

### <a name="use-a-single-regional-service-tag"></a>Eén regionale servicetag gebruiken

Als u liever de service label optie twee en uw cluster bevindt zich in een van de regio's die in deze tabel worden vermeld, hoeft u slechts één regionale servicetag toe te voegen aan uw netwerk beveiligings groep.

| Land | Regio | Servicetag |
| ---- | ---- | ---- |
| Australië | Australië - oost | HDInsight. AustraliaEast |
| &nbsp; | Australië - zuidoost | HDInsight. AustraliaSoutheast |
| &nbsp; | Australië - centraal | HDInsight. AustraliaCentral |
| China | China-oost 2 | HDInsight. ChinaEast2 |
| &nbsp; | China-noord 2 | HDInsight. ChinaNorth2 |
| Verenigde Staten | VS - noord-centraal | HDInsight. NorthCentralUS |
| &nbsp; | VS - west 2 | HDInsight. WestUS2 |
| &nbsp; | VS - west-centraal | HDInsight. WestCentralUS |
| Canada | Canada - oost | HDInsight. CanadaEast |
| Brazilië | Brazilië - zuid | HDInsight. BrazilSouth |
| Korea | Korea - centraal | HDInsight. KoreaCentral |
| &nbsp; | Korea - zuid | HDInsight. KoreaSouth |
| India | India - centraal | HDInsight. CentralIndia |
| &nbsp; | India - zuid | HDInsight. SouthIndia |
| Japan | Japan - west | HDInsight. JapanWest |
| Frankrijk | Frankrijk - centraal| HDInsight. FranceCentral |
| VK | Verenigd Koninkrijk Zuid | HDInsight. UKSouth |
| Azure Government | USDoD-centraal   | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD-Oost | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Meerdere regionale service tags gebruiken

Als u liever de service label optie twee hebt, en de regio waar uw cluster is gemaakt, niet hierboven is vermeld, moet u meerdere regionale service Tags toestaan. De nood zaak om meer dan één te gebruiken, is te wijten aan verschillen in de rang schikking van resource providers voor de verschillende regio's.

De overige regio's worden onderverdeeld in groepen op basis van de regionale service tags die ze gebruiken.

#### <a name="group-1"></a>Groep 1

Als uw cluster wordt gemaakt in een van de regio's in de onderstaande tabel, kunt u naast de regionale servicetag vermeld de service Tags `HDInsight.WestUS` en `HDInsight.EastUS`. Voor regio's in deze sectie zijn drie service Tags vereist.

Als uw cluster bijvoorbeeld is gemaakt in de regio `East US 2`, moet u de volgende service Tags toevoegen aan de netwerk beveiligings groep:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Land | Regio | Servicetag |
| ---- | ---- | ---- |
| Verenigde Staten | VS - oost 2 | HDInsight. EastUS2 |
| &nbsp; | VS - centraal | HDInsight. Centraalus |
| &nbsp; | NorthCentral | HDInsight. NorthCentralUS |
| &nbsp; | VS - zuid-centraal | HDInsight. SouthCentralUS |
| &nbsp; | VS - oost | HDInsight. Oostelijkeus |
| &nbsp; | VS - west | HDInsight. Westelijkeus |
| Japan | Japan - oost | HDInsight. JapanEast |
| Europa | Europa - noord | HDInsight. NorthEurope |
| &nbsp; | Europa -west| HDInsight. Europa West |
| Azië | Azië - oost | HDInsight. EastAsia |
| &nbsp; | Azië - zuidoost | HDInsight. SoutheastAsia |
| Australië | Australië - oost | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Groep 2

Clusters in de regio's van **China-Noord** en **China-Oost**moeten twee service Tags toestaan: `HDInsight.ChinaNorth` en `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Groep 3

Clusters in de regio's van **US gov-Iowa** en **US gov-Virginia**moeten twee service Tags toestaan: `HDInsight.USGovIowa` en `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Groep 4

Clusters in de regio's van **Duitsland-centraal** en **Duitsland-Noordoost**moeten twee service Tags toestaan: `HDInsight.GermanyCentral` en `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Volgende stappen

- [Netwerk beveiligings groepen-service Tags](../virtual-network/security-overview.md#security-rules)
- [Virtuele netwerken voor Azure HDInsight-clusters maken](hdinsight-create-virtual-network.md)
