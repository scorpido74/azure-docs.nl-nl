---
title: Overzicht van Azure-resource logboeken | Microsoft Docs
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/20/2019
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: bfcd2ded96c2679ba9177a760a8b11dc7d2c9a77
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262542"
---
# <a name="azure-resource-logs-overview"></a>Overzicht van Azure-resource logboeken
Azure-resource logboeken zijn [platform logboeken](platform-logs-overview.md) die worden verzonden door Azure-resources die hun interne bewerking beschrijven. Alle bron logboeken delen een gemeen schappelijk schema op het hoogste niveau met de flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken.

## <a name="collecting-resource-logs"></a>Bron logboeken verzamelen
Resource logboeken worden automatisch gegenereerd door ondersteunde Azure-resources, maar ze worden niet verzameld tenzij u ze configureert met een [Diagnostische instelling](diagnostic-settings.md). Maak een diagnostische instelling voor elke Azure-resource om de logboeken door te sturen naar de volgende bestemmingen:

| Destination | Scenario |
|:---|:---|:---|
| [Log Analytics-werkruimte](resource-logs-collect-storage.md) | Analyseer de logboeken met andere bewakings gegevens en gebruik Azure Monitor functies, zoals logboek query's en logboek waarschuwingen. |
| [Azure Storage](archive-diagnostic-logs.md) | Archiveer de logboeken voor controle of back-up. |
| [Event hub](resource-logs-stream-event-hubs.md) | De logboeken streamen naar logboek registraties en telemetrie-systemen van derden.  |

## <a name="compute-resources"></a>Rekenresources
Bron logboeken verschillen van Logboeken op het niveau van het gast besturingssysteem in azure Compute-resources. Voor reken resources is een agent vereist voor het verzamelen van Logboeken en metrische gegevens uit hun gast besturingssysteem, met inbegrip van een gebeurtenis logboek, syslog en prestatie meter items. Gebruik de [Diagnostische uitbrei ding](agents-overview.md#azure-diagnostic-extension) voor het routeren van logboek gegevens van virtuele machines van Azure en de [log Analytics-agent](agents-overview.md#log-analytics-agent) voor het verzamelen van Logboeken en metrieken van virtuele machines in azure, in andere Clouds en on-premises in een log Analytics-werk ruimte. Zie [bronnen van bewakings gegevens voor Azure monitor](data-sources.md) voor meer informatie.

## <a name="resource-logs-schema"></a>Schema voor bron logboeken
Elke Azure-service heeft een eigen schema wanneer bron logboeken naar een van de doelen worden geschreven, maar ze delen een schema op het hoogste niveau in de volgende tabel. Zie hieronder [service-specifieke schema's](#service-specific-schemas) voor koppelingen naar het schema voor elke service. 

| Name | Vereist/optioneel | Description |
|---|---|---|
| time | Vereist | De tijds tempel (UTC) van de gebeurtenis. |
| resourceId | Vereist | De resource-ID van de resource die de gebeurtenis heeft verzonden. Voor Tenant Services is dit de vorm/tenants/Tenant-id/providers/provider-name. |
| TenantId | Vereist voor Tenant logboeken | De Tenant-ID van de Active Directory-Tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op Tenant niveau, maar wordt niet weer gegeven in Logboeken op resource niveau. |
| operationName | Vereist | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam van de RBAC-bewerking (bijvoorbeeld Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normaal gesp roken gemodelleerd in de vorm van een resource manager-bewerking, zelfs als ze niet de werkelijke gedocumenteerde Resource Manager-bewerkingen (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Optioneel | De API-versie die aan de bewerking is gekoppeld als de bewerking is uitgevoerd met een API (bijvoorbeeld `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| category | Vereist | De logboek categorie van de gebeurtenis. Categorie is de granulariteit waarmee u Logboeken voor een bepaalde resource kunt in-of uitschakelen. De eigenschappen die worden weer gegeven in de BLOB eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboek categorie en hetzelfde resource type. Veelvoorkomende logboek categorieën zijn ' Auditing ' ' Execution ' en ' request '. |
| resultType | Optioneel | De status van de gebeurtenis. Typische waarden zijn gestart, in uitvoering, geslaagd, mislukt, actief en opgelost. |
| resultSignature | Optioneel | De substatus van de gebeurtenis. Als deze bewerking overeenkomt met een REST API-aanroep, is dit de HTTP-status code van de bijbehorende REST-aanroep. |
| resultDescription | Optioneel | De statische tekst beschrijving van deze bewerking, bijvoorbeeld. Opslag bestand ophalen. |
| durationMs | Optioneel | De duur van de bewerking in milliseconden. |
| callerIpAddress | Optioneel | Het IP-adres van de beller, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| correlationId | Optioneel | Een GUID die wordt gebruikt om een set gerelateerde gebeurtenissen samen te voegen. Normaal gesp roken, als twee gebeurtenissen dezelfde operationname hebben, maar twee verschillende statussen hebben (bijvoorbeeld ' Started ' en ' geslaagd '), delen ze dezelfde correlatie-ID. Dit kan ook andere relaties tussen gebeurtenissen vertegenwoordigen. |
| identity | Optioneel | Een JSON-BLOB waarmee de identiteit wordt beschreven van de gebruiker of toepassing die de bewerking heeft uitgevoerd. Dit omvat meestal de autorisatie en claims/JWT-token van Active Directory. |
| Niveau | Optioneel | Het Ernst niveau van de gebeurtenis. Moet een van de volgende informatie hebben: waarschuwing, fout of kritiek. |
| location | Optioneel | De regio van de resource waarmee de gebeurtenis wordt verzonden, bijvoorbeeld. "VS-Oost" of "Frankrijk-zuid" |
| properties | Optioneel | Alle uitgebreide eigenschappen die betrekking hebben op deze specifieke gebeurtenis categorie. Alle aangepaste/unieke eigenschappen moeten in dit deel B van het schema worden geplaatst. |

## <a name="service-specific-schemas"></a>Servicespecifieke schema's
Het schema voor de diagnostische logboeken van de resource is afhankelijk van het bron type dat `resourceId` door de eigenschap wordt `category` gedefinieerd en de eigenschappen. In de volgende lijst ziet u alle Azure-Services die ondersteuning bieden voor bron logboeken met koppelingen naar de service en het specifieke schema voor de categorie waar beschikbaar.

| Service | & Documenten voor schema |
| --- | --- |
| Azure Active Directory | [Overzicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema voor [audit logboeken](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) en [aanmeldingen](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Diagnostische logboeken API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Toepassingsgateways |[Diagnostische logboek registratie voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Diagnostische logboek registratie Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Diagnostische logboeken Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Diagnostische logboeken Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Diagnostische logboek registratie voor Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| CDN (Content Delivery Network) | [Diagnostische logboeken van Azure voor CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB logboek registratie](../../cosmos-db/logging.md) |
| Data Factory | [Gegevens fabrieken bewaken met behulp van Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Diagnostische logboeken openen voor Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Diagnostische logboeken openen voor Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Diagnostische logboeken van Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Het schema is niet beschikbaar. |
| Azure Firewall | Het schema is niet beschikbaar. |
| IoT Hub | [IoT Hub bewerkingen](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Logboekregistratie van Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Netwerktaakverdeler |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netwerkbeveiligingsgroepen |[Logboekanalyses voor netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection Standard beheren](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | [Diagnostische logboek registratie voor Power BI Embedded in azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Gegevens model voor Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Zoek Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md) |
| Service Bus |[Diagnostische logboeken Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Diagnostische logboek registratie Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Diagnostische logboeken voor taken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-logboek schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuele netwerken | Het schema is niet beschikbaar. |
| Virtuele netwerkgateways | Het schema is niet beschikbaar. |

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over andere Azure-platform logboeken](platform-logs-overview.md) die u kunt gebruiken om Azure te bewaken.
