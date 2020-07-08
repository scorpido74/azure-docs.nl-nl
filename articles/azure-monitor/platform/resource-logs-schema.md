---
title: Ondersteunde services en schema's voor Azure resource logs
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.subservice: logs
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 37c4093e52caf091489c60a8cdf497cffeffe9ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413855"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Algemeen en specifiek service schema voor Azure-resource logboeken

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken. De naam is in oktober 2019 gewijzigd, omdat de typen logboeken die door Azure Monitor zijn verzameld, meer dan alleen de Azure-resource bevatten. De lijst met bron logboek categorieën die u kunt verzamelen, wordt ook gebruikt om in dit artikel te worden weer gegeven. Ze zijn verplaatst naar [resource logboek categorieën](resource-logs-categories.md). 

[Azure monitor bron logboeken](../../azure-monitor/platform/platform-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in de `resourceId` eigenschap) en de `category` unieke identificatie van een schema. In dit artikel wordt het schema op het hoogste niveau voor resource logboeken en koppelingen naar de schema's voor elke service beschreven.


## <a name="top-level-common-schema"></a>Algemeen schema op het hoogste niveau

| Name | Vereist/optioneel | Description |
|---|---|---|
| tijd | Vereist | De tijds tempel (UTC) van de gebeurtenis. |
| resourceId | Vereist | De resource-ID van de resource die de gebeurtenis heeft verzonden. Voor Tenant Services is dit de vorm/tenants/Tenant-id/providers/provider-name. |
| tenantId | Vereist voor Tenant logboeken | De Tenant-ID van de Active Directory-Tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op Tenant niveau, maar wordt niet weer gegeven in Logboeken op resource niveau. |
| operationName | Vereist | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam van de RBAC-bewerking (bijvoorbeeld micro soft. Storage/Storage accounts/blobServices/blobs/Read). Normaal gesp roken gemodelleerd in de vorm van een resource manager-bewerking, zelfs als ze niet de werkelijke gedocumenteerde Resource Manager-bewerkingen ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Optioneel | De API-versie die aan de bewerking is gekoppeld als de bewerking is uitgevoerd met behulp van een API (bijvoorbeeld `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| category | Vereist | De logboek categorie van de gebeurtenis. Categorie is de granulariteit waarmee u Logboeken voor een bepaalde resource kunt in-of uitschakelen. De eigenschappen die worden weer gegeven in de BLOB eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboek categorie en hetzelfde resource type. Veelvoorkomende logboek categorieën zijn ' Auditing ' ' Execution ' en ' request '. |
| resultType | Optioneel | De status van de gebeurtenis. Typische waarden zijn gestart, in uitvoering, geslaagd, mislukt, actief en opgelost. |
| resultSignature | Optioneel | De substatus van de gebeurtenis. Als deze bewerking overeenkomt met een REST API aanroep, is dit veld de HTTP-status code van de bijbehorende REST-aanroep. |
| resultDescription | Optioneel | De statische tekst beschrijving van deze bewerking, bijvoorbeeld ' opslag bestand ophalen '. |
| durationMs | Optioneel | De duur van de bewerking in milliseconden. |
| callerIpAddress | Optioneel | Het IP-adres van de beller, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| correlationId | Optioneel | Een GUID die wordt gebruikt om een set gerelateerde gebeurtenissen samen te voegen. Als twee gebeurtenissen dezelfde operationname hebben maar twee verschillende statussen hebben (bijvoorbeeld ' gestart ' en ' geslaagd '), delen ze meestal dezelfde correlatie-ID. Dit kan ook andere relaties tussen gebeurtenissen vertegenwoordigen. |
| identity | Optioneel | Een JSON-BLOB waarmee de identiteit wordt beschreven van de gebruiker of toepassing die de bewerking heeft uitgevoerd. Dit veld bevat normaal gesp roken de autorisatie en claims/JWT-token van Active Directory. |
| Niveau | Optioneel | Het Ernst niveau van de gebeurtenis. Moet een van de volgende informatie hebben: waarschuwing, fout of kritiek. |
| location | Optioneel | De regio van de resource waarmee de gebeurtenis wordt verzonden, bijvoorbeeld ' vs-Oost ' of ' Frankrijk-zuid ' |
| properties | Optioneel | Alle uitgebreide eigenschappen die betrekking hebben op deze specifieke gebeurtenis categorie. Alle aangepaste/unieke eigenschappen moeten in dit deel B van het schema worden geplaatst. |

## <a name="service-specific-schemas"></a>Servicespecifieke schema's

Het schema voor resource logboeken varieert afhankelijk van de categorie resource en logboek. In deze lijst worden de services weer gegeven die beschik bare bron logboeken en koppelingen naar het service-en categorie-specifieke schema maken, waar beschikbaar. Deze lijst wordt gewijzigd wanneer er nieuwe services worden toegevoegd, dus als u hieronder niet ziet wat u nodig hebt, kunt u een zoek machine gebruiken om aanvullende documentatie te ontdekken. U kunt in dit artikel een GitHub-probleem openen zodat het kan worden bijgewerkt.

| Service | & documenten voor schema |
| --- | --- |
| Azure Active Directory | [Overzicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema voor [audit logboeken](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) en [aanmeldingen](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://docs.microsoft.com/azure/analysis-services/analysis-services-logging |
| API Management | [API Management bron logboeken](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Toepassingsgateways |[Logboek registratie voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch logboek registratie](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Diagnostische logboeken Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL logboeken](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Data Explorer | [Azure Data Explorer-logboeken](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Logboek registratie voor Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Logboek registratie voor Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Azure-logboeken voor CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB logboek registratie](../../cosmos-db/logging.md) |
| Data Factory | [Gegevens fabrieken bewaken met behulp van Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Toegang tot logboeken voor Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Toegang tot logboeken voor Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs-logboeken](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Het schema is niet beschikbaar. |
| Azure Firewall | Het schema is niet beschikbaar. |
| IoT Hub | [IoT Hub bewerkingen](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault logboek registratie](../../key-vault/general/logging.md) |
| Kubernetes-service |[Logboek registratie voor Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netwerkbeveiligingsgroepen |[Logboekanalyses voor netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection Standard beheren](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | [Logboek registratie voor Power BI Embedded in azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Gegevens model voor Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Zoek Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus logboeken](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database logboek registratie](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Taaklogboeken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-logboek schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuele netwerken | Het schema is niet beschikbaar. |
| Virtuele netwerkgateways | Het schema is niet beschikbaar. |


## <a name="next-steps"></a>Volgende stappen

* [Raadpleeg de resource logboek categorieën die u kunt verzamelen](resource-logs-categories.md)
* [Meer informatie over bron logboeken](../../azure-monitor/platform/platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
