---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 34e828a43567ffc701c1645c55f4ff0352b0e8d3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75773008"
---
Azure Data Factory is een service met meerdere tenants met de volgende standaard limieten om ervoor te zorgen dat klant abonnementen worden beschermd tegen de werk belastingen van elkaar. Neem contact op met de ondersteuning als u de limieten wilt verhogen tot het maximum voor uw abonnement.

### <a name="version-2"></a>Versie 2

| Bron | Standaardlimiet | Maximumaantal |
| -------- | ------------- | ------------- |
| Gegevens fabrieken in een Azure-abonnement | 50 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Het totale aantal entiteiten, zoals pijp lijnen, gegevens sets, triggers, gekoppelde services en Integration runtimes, binnen een data factory | 5\.000 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale CPU-kernen voor Azure-SSIS-integratie-runtime onder één abonnement | 256 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige pijplijn uitvoeringen per data factory die worden gedeeld tussen alle pijp lijnen in de fabriek | 10.000  | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige uitvoeringen van externe activiteiten per abonnement per [Azure Integration runtime regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externe activiteiten worden beheerd op integratie-runtime, maar worden uitgevoerd op gekoppelde services, waaronder Databricks, opgeslagen procedure, HDInsights, Internet en anderen.</small> | 3000 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige uitvoeringen van pijplijn activiteit per abonnement per [Azure Integration runtime regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Pijplijn activiteiten worden uitgevoerd op Integration runtime, inclusief lookup, GetMetadata en DELETE.</small>| 1000 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige ontwerp bewerkingen per abonnement per [Azure Integration runtime regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>, waaronder test verbinding, bladeren in mappen lijst en tabel lijst, voor beeld van gegevens. | 200 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige gegevens integratie-eenheden<sup>1</sup> verbruik per abonnement per [Azure Integration runtime regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regio groep 1<sup>2</sup>: 6000<br>Regio groep 2<sup>2</sup>: 3000<br>Regio groep 3<sup>2</sup>: 1500 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximum aantal activiteiten per pijp lijn, inclusief binnenste activiteiten voor containers | 40 | 40 |
| Maximum aantal gekoppelde integratie-Runtimes dat kan worden gemaakt met één zelf-hostende Integration runtime | 100 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximum aantal para meters per pijp lijn | 50 | 50 |
| ForEach-items | 100.000 | 100.000 |
| Parallelle uitvoering van ForEach | 20 | 50 |
| Maximum aantal uitvoeringen in wachtrij per pijp lijn | 100 | 100 |
| Tekens per expressie | 8\.192 | 8\.192 |
| Mini maal tumblingvenstertriggers-venster activerings interval | 15 min | 15 min |
| Maximale time-out voor de uitvoering van pijplijn activiteit | 7 dagen | 7 dagen |
| Bytes per object voor pijplijn objecten<sup>3</sup> | 200 KB | 200 KB |
| Bytes per object voor gegevensset en gekoppelde service objecten<sup>3</sup> | 100 kB | 2\.000 KB |
| Uitvoering van gegevens integratie-eenheden<sup>1</sup> per Kopieer activiteit | 256 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-aanroepen schrijven | 1200/h<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet Azure Data Factory. | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-aanroepen lezen | 12500/h<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet Azure Data Factory. | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Bewakings query's per minuut | 1000 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| RUWE bewerkingen entiteit per minuut | 50 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale tijd voor fout opsporing van gegevens stromen | 8 uur | 8 uur |
| Gelijktijdige aantal gegevens stromen per Factory | 50 | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige aantal gegevens stroom fout opsporing sessies per gebruiker per Factory | 3 | 3 |
| TTL-limiet Azure IR data flow | 4 uur | [Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> de gegevens integratie eenheid (DIU) wordt gebruikt in een Kopieer bewerking in de Cloud naar de Cloud, meer informatie uit de [gegevens integratie-eenheden (versie 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Zie voor meer informatie over facturering [Azure Data Factory prijzen](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) is [wereld wijd beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) om te zorgen voor de naleving van gegevens, de efficiëntie en verminderde netwerk kosten voor uitgaand verkeer. 

| Regio groep | Regio's | 
| -------- | ------ |
| Regio groep 1 | Central VS, VS-Oost, Oost-VS2, Europa-noord, Europa-west, VS-West, VS-West 2 |
| Regio groep 2 | Australië-oost, Australië-zuidoost, Brazilië-zuid, Centraal-India, Japan-Oost, Northcentral VS, Southcentral VS, Zuidoost-Azië, VS-West-Centraal |
| Regio groep 3 | Canada-centraal, Azië-oost, Frankrijk-centraal, Korea-centraal, UK-zuid |

<sup>3</sup> pijp lijn, data set en gekoppelde service objecten vertegenwoordigen een logische groepering van uw workload. De limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om de PETA bytes van gegevens te verwerken.

### <a name="version-1"></a>Versie 1

| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Pijp lijnen in een data factory |2,500 |[Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gegevens sets binnen een data factory |5\.000 |[Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige segmenten per gegevensset |10 |10 |
| Bytes per object voor pijplijn objecten<sup>1</sup> |200 KB |200 KB |
| Bytes per object voor gegevens verzameling en gekoppelde service objecten<sup>1</sup> |100 kB |2\.000 KB |
| Azure HDInsight-cluster kernen op aanvraag in een abonnement<sup>2</sup> |60 |[Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gegevens verplaatsings eenheden voor Clouds per Kopieer activiteit<sup>3</sup> uitvoeren |32 |[Contact opnemen met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Aantal pogingen voor het uitvoeren van pijplijn activiteit |1000 |MaxInt (32 bits) |

<sup>1</sup> pijp lijn, data set en gekoppelde service objecten vertegenwoordigen een logische groepering van uw workload. De limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om de PETA bytes van gegevens te verwerken.

<sup>2</sup> HDInsight-kernen op aanvraag worden toegewezen uit het abonnement dat de Data Factory bevat. Als gevolg hiervan is de vorige limiet de Data Factory afgedwongen kern limiet voor HDInsight-kernen op aanvraag. Het verschilt van de kern limiet die is gekoppeld aan uw Azure-abonnement.

<sup>3</sup> de Cloud gegevens verplaatsings eenheid (DMU) voor versie 1 wordt gebruikt in een kopie bewerking in de Cloud naar de Cloud, lees meer uit de [gegevens verplaatsings eenheden voor de Cloud (versie 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Zie voor meer informatie over facturering [Azure Data Factory prijzen](https://azure.microsoft.com/pricing/details/data-factory/).

| **Resource** | **Standaard ondergrens** | **Minimum limiet** |
| --- | --- | --- |
| Plannings interval |15 minuten |15 minuten |
| Interval tussen nieuwe pogingen |1 seconde |1 seconde |
| Time-outwaarde opnieuw proberen |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Oproep limieten voor webservices
Azure Resource Manager heeft limieten voor API-aanroepen. U kunt API-aanroepen maken met een snelheid binnen de [Azure Resource Manager API-limieten](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
