---
title: NSG-servicetags (Network Security Group) voor Azure HDInsight
description: Gebruik HDInsight-servicetags om binnenkomend verkeer naar uw cluster toe te staan vanaf knooppunten voor status- en beheerservices, zonder IP-adressen toe te voegen aan uw NSG's.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: 34ec05a8362f5947cb61924b19c6b1a52e5d91a4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437679"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>NSG-servicetags voor Azure HDInsight

Azure HDInsight-servicetags voor netwerkbeveiligingsgroepen (NSG's) zijn groepen IP-adressen voor status- en beheerservices. Deze groepen helpen de complexiteit voor het maken van beveiligingsregels te minimaliseren. [Servicetags](../virtual-network/security-overview.md#service-tags) bieden een alternatieve methode voor het toestaan van binnenkomend verkeer vanaf specifieke IP-adressen zonder elk van de [beheer-IP-adressen](hdinsight-management-ip-addresses.md) in uw NGB's in te voeren.

De HDInsight-service beheert deze servicetags. U uw eigen servicetag niet maken of een bestaande tag wijzigen. Microsoft beheert de adresvoorvoegsels die overeenkomen met de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

## <a name="get-started-with-service-tags"></a>Aan de slag met servicetags

U hebt twee opties voor het gebruik van servicetags in uw netwerkbeveiligingsgroepen:

- **Gebruik één globale HDInsight-servicetag:** met deze optie opent u uw virtuele netwerk voor alle IP-adressen die de HDInsight-service gebruikt om clusters in alle regio's te bewaken. Deze optie is de eenvoudigste methode, maar is mogelijk niet geschikt als u beperkende beveiligingsvereisten hebt.

- **Meerdere regionale servicetags gebruiken:** met deze optie wordt uw virtuele netwerk alleen geopend op de IP-adressen die HDInsight in die specifieke regio gebruikt. Als u echter meerdere regio's gebruikt, moet u meerdere servicetags toevoegen aan uw virtuele netwerk.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Gebruik één wereldwijde HDInsight-servicetag

De eenvoudigste manier om servicetags te gebruiken met uw HDInsight-cluster is door de globale tag `HDInsight` toe te voegen aan een NSG-regel.

1. Selecteer uw netwerkbeveiligingsgroep in de [Azure-portal.](https://portal.azure.com/)

1. Selecteer **onder Instellingen** **binnenkomende beveiligingsregels**en selecteer **+ Toevoegen**.

1. Selecteer **Servicetag**in de vervolgkeuzelijst **Bron** .

1. Selecteer **HDInsight**in de vervolgkeuzelijst **Bronservicetag** .

    ![Een servicetag toevoegen vanuit de Azure-portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Deze tag bevat de IP-adressen van gezondheids- en beheerservices voor alle regio's waar HDInsight beschikbaar is. De tag zorgt ervoor dat uw cluster kan communiceren met de benodigde gezondheids- en beheerservices, ongeacht waar het is gemaakt.

## <a name="use-regional-hdinsight-service-tags"></a>Regionale HDInsight-servicetags gebruiken

Als de globale tagoptie niet werkt omdat u meer beperkende machtigingen nodig hebt, u alleen de servicetags toestaan die van toepassing zijn op uw regio. Er kunnen één, twee of drie toepasselijke servicetags zijn, afhankelijk van het gebied waar uw cluster wordt gemaakt.

Lees de volgende gedeelten van het artikel om erachter te komen welke servicetags u voor uw regio wilt toevoegen.

### <a name="use-a-single-regional-service-tag"></a>Eén regionale servicetag gebruiken

Als u liever regionale servicetags gebruikt en uw cluster zich in een van de regio's in deze tabel bevindt, hoeft u slechts één regionale servicetag toe te voegen aan uw netwerkbeveiligingsgroep.

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
| Azure Government | USDoD Centraal | HDInsight.USDoDCentraal |
| &nbsp; | USGov - Texas | HDInsight.USGovTexas HDInsight.USGovTexas |
| &nbsp; | UsDod Oost | HDInsight.USDodEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Meerdere regionale servicetags gebruiken

Als u liever regionale servicetags gebruikt, maar het gebied waar uw cluster wordt gemaakt, is niet in de vorige tabel opgenomen, moet u meerdere regionale servicetags toestaan. De noodzaak om meer dan één te gebruiken is te wijten aan verschillen in de regeling van resource providers voor de verschillende regio's.

De overige regio's zijn onderverdeeld in groepen op basis van welke regionale servicetags ze gebruiken.

#### <a name="group-1"></a>Groep 1

Als uw cluster is gemaakt in een van de regio's in de volgende tabel, u de servicetags `HDInsight.WestUS` en `HDInsight.EastUS` de lijst met regionale servicetags toestaan. Voor regio's in deze sectie zijn drie servicetags vereist.

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

Clusters in de regio's *China Noord* en China `HDInsight.ChinaNorth` `HDInsight.ChinaEast` *East* moeten twee service tags toestaan: en .

#### <a name="group-3"></a>Groep 3

Clusters in de regio's van *de Amerikaanse gouverneur Iowa* en de Amerikaanse gouverneur *Virginia* moeten twee service tags toestaan: `HDInsight.USGovIowa` en `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Groep 4

Clusters in de regio's *Duitsland Centraal* en Duitsland `HDInsight.GermanyCentral` `HDInsight.GermanyNortheast` *Noordoost* moeten twee service tags toestaan: en .

## <a name="next-steps"></a>Volgende stappen

- [Netwerkbeveiligingsgroepen: servicetags](../virtual-network/security-overview.md#security-rules)
- [Virtuele netwerken maken voor Azure HDInsight-clusters](hdinsight-create-virtual-network.md)
