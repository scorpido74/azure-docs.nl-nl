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
ms.openlocfilehash: bfb42e0ba81eef145932db072c8a80a2f8e00932
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424962"
---
Azure Data Factory is een service met meerdere tenants met de volgende standaardlimieten om ervoor te zorgen dat klantabonnementen worden beschermd tegen elkaars werkbelastingen. Neem contact op met de ondersteuning als u de limieten wilt verhogen tot de maximumwaarde voor uw abonnement.

### <a name="version-2"></a>Versie 2

| Resource | Standaardlimiet | Maximumaantal |
| -------- | ------------- | ------------- |
| Data factory's in een Azure-abonnement | 800 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Het totale aantal entiteiten, zoals pijplijnen, gegevenssets, triggers, gekoppelde services, privé-eindpunten en integratieruntimes, binnen een data factory | 5\.000 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale aantal CPU-kernen voor Azure-SSIS IR voor één abonnement | 256 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige pijplijnuitvoeringen per data factory die worden gedeeld tussen alle pijplijnen in de factory | 10.000  | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige uitvoeringen van externe activiteiten per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externe activiteiten worden beheerd op integratieruntime, maar worden uitgevoerd op gekoppelde services, waaronder Databricks, opgeslagen procedure, HDInsights, Web en overige.</small> | 3000 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige uitvoeringen van pijplijnactiviteit per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Pijplijnactiviteiten worden uitgevoerd op integratieruntime, waaronder Lookup, GetMetadata en Delete. </small>| 1000 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige creatiebewerkingen per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Waaronder testverbinding, bladeren in mappenlijst en tabellijst, gegevens vooraf bekijken. | 200 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige verbruik van gegevensintegratie-eenheden<sup>1</sup> per abonnement per [Azure Integration Runtime-regio](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regiogroep 1<sup>2</sup>: 6000<br>Regiogroep 2<sup>2</sup>: 3000<br>Regiogroep 3<sup>2</sup>: 1500 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximum aantal activiteiten per pijplijn, inclusief interne activiteiten voor containers | 40 | 40 |
| Maximum aantal gekoppelde integratieruntimes dat kan worden gemaakt voor één zelf-hostende IR | 100 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximum aantal parameters per pijplijn | 50 | 50 |
| ForEach-items | 100.000 | 100.000 |
| Parallelle ForEach-uitvoering | 20 | 50 |
| Maximum aantal uitvoeringen in de wachtrij per pijplijn | 100 | 100 |
| Tekens per expressie | 8\.192 | 8\.192 |
| Minimum aantal intervallen voor tumblingvenstertrigger | 15 min | 15 min |
| Maximale time-out voor uitvoeringen van pijplijnactiviteit | 7 dagen | 7 dagen |
| Bytes per object voor pijplijnobjecten<sup>3</sup> | 200 kB | 200 kB |
| Bytes per object voor gegevensset en gekoppelde serviceobjecten<sup>3</sup> | 100 kB | 2000 kB |
| Gegevensintegratie-eenheden<sup>1</sup> per uitvoering van de kopieeractiviteit | 256 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-aanroepen schrijven | 1200/u<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet door Azure Data Factory. | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-aanroepen lezen | 12.500/u<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet door Azure Data Factory. | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Bewakingsquery's per minuut | 1000 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| CRUD-bewerkingen voor entiteiten per minuut | 50 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale tijd voor foutopsporingssessie van gegevensstroom | 8 uur | 8 uur |
| Gelijktijdig aantal gegevensstromen per integratieruntime | 50 | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdig aantal foutopsporingssessies van gegevensstroom per gebruiker per factory | 3 | 3 |
| Azure IR TTL-limiet voor gegevensstroom | 4 uur | [Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> De gegevensintegratie-eenheid (DIU) wordt gebruikt in een kopieerbewerking van de cloud naar de cloud. Zie [Gegevensintegratie-eenheden (versie 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) voor meer informatie. Zie [Prijzen voor Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/)voor meer informatie over facturering.

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) is [algemeen beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) om de gegevensnaleving, efficiëntie en verminderde kosten voor uitgaand netwerkverkeer te garanderen. 

| Regiogroep | Regio's | 
| -------- | ------ |
| Regiogroep 1 | US - centraal, US - oost 2, Europa - noord, Europa - west, US - west, US - west 2 |
| Regiogroep 2 | Australië - oost, Australië - zuidoost, Brazilië - zuid, India - centraal, Japan - oost, US - noord-centraal, US - zuid-centraal, Azië - zuidoost, US - west-centraal |
| Regiogroep 3 | Canada - centraal, Azië - oost, Frankrijk - centraal, Korea - centraal, UK - zuid |

<sup>3</sup> Pijplijn-, gegevensset- en gekoppelde serviceobjecten vertegenwoordigen een logische groepering van uw werkbelasting. De limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om petabytes aan gegevens te kunnen verwerken.

### <a name="version-1"></a>Versie 1

| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Pijplijnen in een data factory |2500 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gegevenssets binnen een data factory |5\.000 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige segmenten per gegevensset |10 |10 |
| Bytes per object voor pijplijnobjecten<sup>1</sup> |200 kB |200 kB |
| Bytes per object voor gegevensset en gekoppelde serviceobjecten<sup>1</sup> |100 kB |2000 kB |
| Azure HDInsight-clusterkernen op aanvraag in een abonnement<sup>2</sup> |60 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Eenheden voor cloudgegevensverplaatsing per uitvoering van een kopieeractiviteit<sup>3</sup> |32 |[Neem contact op met ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Aantal pogingen voor uitvoeringen van pijplijnactiviteit |1000 |MaxInt (32 bits) |

<sup>1</sup> Pijplijn-, gegevensset- en gekoppelde serviceobjecten vertegenwoordigen een logische groepering van uw werkbelasting. De limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u kunt verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om petabytes aan gegevens te kunnen verwerken.

<sup>2</sup> HDInsight-kernen op aanvraag worden toegewezen uit het abonnement dat de data factory bevat. Als gevolg hiervan is de vorige limiet de door Data Factory afgedwongen kernlimiet voor HDInsight-kernen op aanvraag. Deze verschilt van de kernlimiet die is gekoppeld aan uw Azure-abonnement.

<sup>3</sup> De cloudgegevensverplaatsingseenheid (DMU) voor versie 1 wordt gebruikt in een kopieerbewerking van de cloud naar de cloud. Meer informatie vindt u in [Cloudgegevensverplaatsingseenheden (versie 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Zie [Prijzen voor Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) voor meer informatie over facturering.

| **Resource** | **Standaardondergrens** | **Minimumlimiet** |
| --- | --- | --- |
| Planningsinterval |15 minuten |15 minuten |
| Interval tussen nieuwe pogingen |1 seconde |1 seconde |
| Time-outwaarde voor opnieuw proberen |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Aanroeplimieten voor webservices
Azure Resource Manager heeft limieten voor API-aanroepen. U kunt API-aanroepen maken met een snelheid binnen de [Azure Resource Manager API-limieten](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
