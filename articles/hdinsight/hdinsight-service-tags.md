---
title: NSG-servicetags (Network Security Group) voor Azure HDInsight
description: Gebruik HDInsight-servicetags om binnenkomend verkeer toe te staan aan uw cluster vanuit hdinsight-status- en beheerservicesknooppunten, zonder expliciet IP-adressen toe te voegen aan uw netwerkbeveiligingsgroepen.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117233"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>NSG-servicetags (Network Security Group) voor Azure HDInsight

HDInsight-servicetags voor netwerkbeveiligingsgroepen (NSG's) zijn groepen IP-adressen voor gezondheids- en beheerservices. Deze groepen helpen de complexiteit voor het maken van beveiligingsregels te minimaliseren. [Servicetags](../virtual-network/security-overview.md#service-tags) bieden een alternatieve methode voor het toestaan van binnenkomend verkeer vanaf specifieke IP-adressen zonder elk van de [beheer-IP-adressen](hdinsight-management-ip-addresses.md) in uw netwerkbeveiligingsgroepen in te voeren.

Deze servicetags worden gemaakt en beheerd door de HDInsight-service. U uw eigen servicetag niet maken of een bestaande tag wijzigen. Microsoft beheert de adresvoorvoegsels die overeenkomen met de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

## <a name="getting-started-with-service-tags"></a>Aan de slag met servicetags

U hebt twee opties voor het gebruik van servicetags in uw netwerkbeveiligingsgroepen:

1. Gebruik één HDInsight-servicetag - deze optie opent uw virtuele netwerk voor alle IP-adressen die de HDInsight-service gebruikt om clusters in alle regio's te bewaken. Deze optie is de eenvoudigste methode, maar is mogelijk niet geschikt als u beperkende beveiligingsvereisten hebt.

1. Gebruik meerdere regionale servicetags - deze optie opent uw virtuele netwerk alleen voor de IP-adressen die HDInsight in die specifieke regio gebruikt. Als u echter meerdere regio's gebruikt, moet u meerdere servicetags toevoegen aan uw virtuele netwerk.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Gebruik één wereldwijde HDInsight-servicetag

De eenvoudigste manier om servicetags te gebruiken met uw HDInsight-cluster is door de globale tag `HDInsight` toe te voegen aan een regel voor netwerkbeveiligingsgroepen.

1. Selecteer uw netwerkbeveiligingsgroep in de [Azure-portal.](https://portal.azure.com/)

1. Selecteer **onder Instellingen** **binnenkomende beveiligingsregels**en selecteer **+ Toevoegen**.

1. Selecteer **Servicetag**in de vervolgkeuzelijst **Bron** .

1. Selecteer **HDInsight**in de vervolgkeuzelijst **Bronservicetag** .

    ![Azure-portal voegt servicetag toe](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Deze tag bevat de IP-adressen van gezondheids- en beheerservices voor alle regio's waar HDInsight beschikbaar is, en zorgt ervoor dat uw cluster kan communiceren met de nodige gezondheids- en beheerservices, ongeacht waar het is gemaakt.

## <a name="use-regional-hdinsight-service-tags"></a>Regionale HDInsight-servicetags gebruiken

Als optie één niet werkt omdat u meer beperkende machtigingen nodig hebt, u alleen de servicetags toestaan die van toepassing zijn op uw regio. De toepasselijke servicetags kunnen één, twee of drie servicetags zijn, afhankelijk van het gebied waar uw cluster wordt gemaakt.

Lees de volgende gedeelten van het document om erachter te komen welke servicetags u voor uw regio wilt toevoegen.

### <a name="use-a-single-regional-service-tag"></a>Eén regionale servicetag gebruiken

Als u de voorkeur geeft aan optie twee servicetag en uw cluster zich in een van de regio's in deze tabel bevindt, hoeft u slechts één regionale servicetag toe te voegen aan uw netwerkbeveiligingsgroep.

| Land | Regio | Servicetag |
| ---- | ---- | ---- |
| Australië | Australië - oost | HDInsight.AustraliaEast |
| &nbsp; | Australië - zuidoost | HDInsight.AustraliaZuidoost |
| &nbsp; | Australië - centraal | HDInsight.AustraliaCentraal |
| China | China Oost 2 | HDInsight.ChinaEast2 |
| &nbsp; | China Noord 2 | HDInsight.ChinaNorth2 |
| Verenigde Staten | VS - noord-centraal | HDInsight.NorthCentralUS |
| &nbsp; | VS - west 2 | HDInsight.WestUS2 |
| &nbsp; | VS - west-centraal | HDInsight.WestCentralUS |
| Canada | Canada - oost | HDInsight.CanadaEast |
| Brazilië | Brazilië - zuid | HDInsight.BrazilSouth HDInsight.BrazilSouth HDInsight.BrazilSouth HDInsight |
| Korea | Korea - centraal | HDInsight.KoreaCentraal |
| &nbsp; | Korea - zuid | HDInsight.KoreaZuid |
| India | India - centraal | HDInsight.CentralIndia |
| &nbsp; | India - zuid | HDInsight.SouthIndia HDInsight.SouthIndia |
| Japan | Japan - west | HDInsight.JapanWest |
| Frankrijk | Frankrijk - centraal| HDInsight.FranceCentraal |
| VK | Verenigd Koninkrijk Zuid | HDInsight.UKZuid |
| Azure Government | USDoD Centraal   | HDInsight.USDoDCentraal |
| &nbsp; | USGov - Texas | HDInsight.USGovTexas HDInsight.USGovTexas |
| &nbsp; | UsDod Oost | HDInsight.USDodEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Meerdere regionale servicetags gebruiken

Als u de voorkeur geeft aan optie twee servicetags en het gebied waar uw cluster is gemaakt, is hierboven niet vermeld, dan moet u meerdere regionale servicetags toestaan. De noodzaak om meer dan één te gebruiken is te wijten aan verschillen in de regeling van resource providers voor de verschillende regio's.

De overige regio's zijn onderverdeeld in groepen op basis van welke regionale servicetags ze gebruiken.

#### <a name="group-1"></a>Groep 1

Als uw cluster is gemaakt in een van de regio's in de onderstaande tabel, u de servicetags `HDInsight.WestUS` en `HDInsight.EastUS` de lijst met regionale servicetags toestaan. Voor regio's in deze sectie zijn drie servicetags vereist.

Als uw cluster bijvoorbeeld in `East US 2` de regio is gemaakt, moet u de volgende servicetags toevoegen aan uw netwerkbeveiligingsgroep:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Land | Regio | Servicetag |
| ---- | ---- | ---- |
| Verenigde Staten | VS - oost 2 | HDInsight.EastUS2 |
| &nbsp; | VS - centraal | HDInsight.CentralUS |
| &nbsp; | NorthCentral VS | HDInsight. NorthCentralUS NorthCentralUS |
| &nbsp; | VS - zuid-centraal | HDInsight.SouthCentralUS |
| &nbsp; | VS - oost | HDInsight.EastUS |
| &nbsp; | VS - west | HDInsight.WestUS HDInsight.WestUS |
| Japan | Japan - oost | HDInsight.JapanEast |
| Europa | Europa - noord | HDInsight.NorthEurope |
| &nbsp; | Europa -west| HDInsight.WestEurope |
| Azië | Azië - oost | HDInsight.EastAsia HDInsight.EastAsia |
| &nbsp; | Azië - zuidoost | HDInsight.SoutheastAsia HDInsight.SoutheastAsia |
| Australië | Australië - oost | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Groep 2

Clusters in de regio's **China Noord** en China `HDInsight.ChinaNorth` East `HDInsight.ChinaEast`, moeten twee service tags toestaan: en . **China East**

#### <a name="group-3"></a>Groep 3

Clusters in de regio's van **de Amerikaanse gouverneur Iowa** en de Amerikaanse gouverneur **Virginia**, moeten twee service tags toestaan: `HDInsight.USGovIowa` en `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Groep 4

Clusters in de regio's **Duitsland Centraal** en Duitsland `HDInsight.GermanyCentral` Noordoost `HDInsight.GermanyNorthEast`, moeten twee service tags toestaan: en . **Germany Northeast**

## <a name="next-steps"></a>Volgende stappen

- [Netwerkbeveiligingsgroepen - servicetags](../virtual-network/security-overview.md#security-rules)
- [Virtuele netwerken maken voor Azure HDInsight-clusters](hdinsight-create-virtual-network.md)
