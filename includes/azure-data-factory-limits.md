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
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086136"
---
Azure Data Factory is een multitenant-service met de volgende standaardlimieten om ervoor te zorgen dat klantabonnementen worden beschermd tegen elkaars workloads. Neem contact op met de ondersteuning om de limieten tot het maximum voor uw abonnement te verhogen.

### <a name="version-2"></a>Versie 2

| Resource | Standaardlimiet | Maximumaantal |
| -------- | ------------- | ------------- |
| Gegevensfabrieken in een Azure-abonnement | 800 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totaal aantal entiteiten, zoals pijplijnen, gegevenssets, triggers, gekoppelde services en gebruikstijden voor integratie, binnen een gegevensfabriek | 5.000 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totale CPU-cores voor Azure-SSIS-integratie-runtimes onder één abonnement | 256 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige pijplijn wordt uitgevoerd per gegevensfabriek die wordt gedeeld tussen alle pijplijnen in de fabriek | 10.000  | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige externe activiteit wordt uitgevoerd per abonnement per [Runtime-regio azure-integratie](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externe activiteiten worden beheerd op het gebied van integratieruntime, maar worden uitgevoerd op gekoppelde services, waaronder Databricks, opgeslagen procedure, HDInsights, Web en anderen.</small> | 3000 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige pijplijnactiviteit wordt uitgevoerd per abonnement per [Runtime-regio azure-integratie](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Pijplijnactiviteiten worden uitgevoerd op de uitvoering van integratie, waaronder Opzoeken, GetMetadata en Delete.</small>| 1000 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige bewerkingen per abonnement per [Runtime-regio azure-integratie](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Inclusief testverbinding, lijst met mappen en tabellijst bladeren, voorbeeldgegevens. | 200 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige data-integratieeenheden<sup>1</sup> verbruik per abonnement per [Runtime-regio azure-integratie](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regio groep 1<sup>2</sup>: 6000<br>Regio groep 2<sup>2</sup>: 3000<br>Regio groep 3<sup>2</sup>: 1500 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale activiteiten per pijpleiding, waaronder binnenactiviteiten voor containers | 40 | 40 |
| Maximaal aantal runtimes voor gekoppelde integratie die kunnen worden gemaakt tegen één zelf gehoste runtime voor integratie | 100 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale parameters per pijplijn | 50 | 50 |
| ForEach-items | 100.000 | 100.000 |
| ForEach parallellisme | 20 | 50 |
| Maximale wachtrijruns per pijplijn | 100 | 100 |
| Tekens per expressie | 8.192 | 8.192 |
| Minimale tumbling window trigger interval | 15 min | 15 min |
| Maximale time-out voor pijplijnactiviteit wordt uitgevoerd | 7 dagen | 7 dagen |
| Bytes per object voor pijplijnobjecten<sup>3</sup> | 200 KB | 200 KB |
| Bytes per object voor gegevensset en gekoppelde serviceobjecten<sup>3</sup> | 100 kB | 2.000 KB |
| Gegevensintegratie-eenheden<sup>1</sup> per kopieactiviteit uitvoeren | 256 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-aanroepen schrijven | 1.200/u<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet door Azure Data Factory. | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API-aanroepen lezen | 12.500/u<br/><br/> Deze limiet wordt opgelegd door Azure Resource Manager, niet door Azure Data Factory. | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Query's per minuut bewaken | 1000 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Crud-bewerkingen van entiteit en per minuut | 50 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale tijd van de foutopsporingssessie van de gegevensstroom | 8 uur | 8 uur |
| Gelijktijdig aantal gegevensstromen per fabriek | 50 | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdig aantal foutopsporingssessies gegevensstroom per gebruiker per fabriek | 3 | 3 |
| Azure IR TTL-limiet voor gegevensstroom | 4 uur | [Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> De data-integratie-eenheid (DIU) wordt gebruikt in een cloud-to-cloud-kopieerbewerking, meer informatie van [data-integratie-eenheden (versie 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Zie Azure Data [Factory-prijzen](https://azure.microsoft.com/pricing/details/data-factory/)voor informatie over facturering.

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) is wereldwijd [beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) om naleving van gegevens, efficiëntie en lagere netwerkverwijderingskosten te garanderen. 

| Regiogroep | Regio's | 
| -------- | ------ |
| Regiogroep 1 | Centraal VS, Oost-VS, Oost-VS2, Noord-Europa, West-Europa, West-VS, West US 2 |
| Regiogroep 2 | Australië Oost, Australië Zuidoost, Brazilië Zuid, Centraal-India, Japan Oost, Noordcentrale VS, Southcentral US, Zuidoost-Azië, West-Centraal VS |
| Regiogroep 3 | Canada Centraal, Oost-Azië, Frankrijk Centraal, Korea Centraal, VK-Zuid |

<sup>3</sup> Pijplijn-, gegevensset- en gekoppelde serviceobjecten vertegenwoordigen een logische groepering van uw werkbelasting. Limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om te schalen om petabytes aan gegevens te verwerken.

### <a name="version-1"></a>Versie 1

| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Pijpleidingen binnen een gegevensfabriek |2500 |[Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gegevenssets in een gegevensfabriek |5.000 |[Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gelijktijdige segmenten per gegevensset |10 |10 |
| Bytes per object voor pijplijnobjecten<sup>1</sup> |200 KB |200 KB |
| Bytes per object voor gegevensset en gekoppelde serviceobjecten<sup>1</sup> |100 kB |2.000 KB |
| Azure HDInsight on-demand clustercores binnen een abonnement<sup>2</sup> |60 |[Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Bewegingseenheden voor cloudgegevens per kopieeractiviteit uitvoeren<sup>3</sup> |32 |[Neem contact op met de ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Aantal opnieuw proberen voor pijplijnactiviteitwordt uitgevoerd |1000 |MaxInt (32 bit) |

<sup>1</sup> Pijplijn-, gegevensset- en gekoppelde serviceobjecten vertegenwoordigen een logische groepering van uw werkbelasting. Limieten voor deze objecten hebben geen betrekking op de hoeveelheid gegevens die u verplaatsen en verwerken met Azure Data Factory. Data Factory is ontworpen om te schalen om petabytes aan gegevens te verwerken.

<sup>2</sup> On-demand HDInsight-cores worden toegewezen uit het abonnement dat de gegevensfabriek bevat. Als gevolg hiervan is de vorige limiet de datafactory-afgedwongen kernlimiet voor on-demand HDInsight-cores. Het is anders dan de kernlimiet die is gekoppeld aan uw Azure-abonnement.

<sup>3</sup> De cloud data movement unit (DMU) voor versie 1 wordt gebruikt in een cloud-to-cloud copy-bewerking, meer informatie van [cloud data movement units (versie 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Zie Azure Data [Factory-prijzen](https://azure.microsoft.com/pricing/details/data-factory/)voor informatie over facturering.

| **Resource** | **Standaardondergrens** | **Minimumlimiet** |
| --- | --- | --- |
| Planningsinterval |15 minuten |15 minuten |
| Interval tussen pogingen opnieuw proberen |1 seconde |1 seconde |
| Time-outwaarde opnieuw proberen |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Limieten voor webserviceoproepen
Azure Resource Manager heeft limieten voor API-aanroepen. U API-aanroepen met een snelheid binnen de [Azure Resource Manager API-limieten](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
