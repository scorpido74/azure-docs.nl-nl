---
title: Azure-abonnementslimieten en -quota
description: Biedt een lijst met algemene Azure-abonnements- en servicelimieten, quota en beperkingen. Dit artikel bevat informatie over hoe u limieten verhogen, samen met de maximumwaarden.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 397d6a719f64a49208e651469fab9cf841ccd4ad
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521359"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-abonnements- en servicelimieten, quota en beperkingen

In dit document worden enkele van de meest voorkomende Microsoft Azure-limieten weergegeven, die ook wel quota worden genoemd.

Zie overzicht van [Azure-prijzen](https://azure.microsoft.com/pricing/)voor meer informatie over Azure-prijzen. Daar u uw kosten schatten met behulp van de [prijscalculator.](https://azure.microsoft.com/pricing/calculator/) U ook naar de pagina met prijsdetails voor een bepaalde service gaan, bijvoorbeeld [Windows VM's.](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) Zie [Onverwachte kosten voorkomen met Azure-facturering en kostenbeheer](../../billing/billing-getting-started.md)voor tips om uw kosten te beheren.

## <a name="managing-limits"></a>Limieten beheren

> [!NOTE]
> Sommige services hebben aanpasbare limieten.
>
> Wanneer een service geen aanpasbare limieten heeft, worden in de volgende tabellen de **koplimiet gebruikt.** In die gevallen zijn de standaard- en de maximumlimieten hetzelfde.
>
> Wanneer de limiet kan worden aangepast, bevatten de tabellen **standaardlimiet** en **maximumlimietkoppen.** De limiet kan worden verhoogd boven de standaardlimiet, maar niet boven de maximumlimiet.
>
> Als u de limiet of het quotum boven de standaardlimiet wilt verhogen, [opent u kosteloos een online verzoek om klantenondersteuning.](../templates/error-resource-quota.md)

[Gratis proefabonnementen](https://azure.microsoft.com/offers/ms-azr-0044p) komen niet in aanmerking voor limiet- of quotaverhogingen. Als u een [gratis proefabonnement hebt,](https://azure.microsoft.com/offers/ms-azr-0044p)u upgraden naar een [betalen per gebruik-abonnement.](https://azure.microsoft.com/offers/ms-azr-0003p/) Zie [Uw Azure Free Proefabonnement-abonnement upgraden naar een abonnement op basis van betalen per gebruik](../../billing/billing-upgrade-azure-subscription.md) en de [veelgestelde vragen over een gratis proefabonnement.](https://azure.microsoft.com/free/free-account-faq)

Sommige limieten worden op regionaal niveau beheerd.

Laten we vCPU-quota als voorbeeld gebruiken. Als u een quotumverhoging wilt aanvragen met ondersteuning voor vCPU's, moet u bepalen hoeveel vCPU's u wilt gebruiken in welke regio's. Vervolgens maakt u een specifieke aanvraag voor vCPU-quota voor Azure-brongroepen voor de gewenste bedragen en regio's. Als u 30 vCPU's in West-Europa moet gebruiken om uw toepassing daar uit te voeren, vraagt u specifiek 30 vCPU's in West-Europa. Uw vCPU-quotum wordt in geen enkele andere regio verhoogd- alleen West-Europa heeft het 30-vCPU-quotum.

Bepaal daarom wat uw Azure-brongroepquota moeten zijn voor uw werkbelasting in één regio. Vraag vervolgens dat bedrag op in elke regio waarin u wilt implementeren. Zie [Fouten voor resourcequota oplossen](../templates/error-resource-quota.md)voor hulp bij het bepalen van uw huidige quota voor specifieke regio's.

## <a name="general-limits"></a>Algemene limieten

Zie [Naamgevingsregels en beperkingen voor Azure-resources voor](resource-name-rules.md)beperkingen voor resourcenamen.

Zie Vragen voor het lezen en schrijven [van Resource](request-limits-and-throttling.md)Manager voor informatie over lees- en schrijflimieten voor Resource Manager van Resource Manager .

### <a name="management-group-limits"></a>Limieten van managementgroepen

Voor [beheergroepen](../../governance/management-groups/overview.md)gelden de volgende limieten .

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Abonnementslimieten

De volgende limieten zijn van toepassing wanneer u Azure Resource Manager en Azure-brongroepen gebruikt.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limieten voor resourcegroepen

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory-limieten

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API-beheerlimieten

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limieten voor app-service

De volgende limieten voor App-service zijn limieten voor Web Apps, Mobiele Apps en API-apps.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automatiseringslimieten

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure-cache voor Redis-limieten

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure Cloud Services-limieten

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search-limieten

Prijsniveaus bepalen de capaciteit en limieten van uw zoekservice. Lagen omvatten:

* **Gratis** multi-tenant service, gedeeld met andere Azure-abonnees, is bedoeld voor evaluatie en kleine ontwikkelingsprojecten.
* **Basic** biedt speciale computerbronnen voor productieworkloads op kleinere schaal, met maximaal drie replica's voor zeer beschikbare queryworkloads.
* **Standard**, dat s1, S2, S3 en S3 High Density omvat, is voor grotere productieworkloads. Er bestaan meerdere niveaus binnen de standaardlaag, zodat u een resourceconfiguratie kiezen die het beste aansluit bij uw werkbelastingprofiel.

**Limieten per abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limieten per zoekservice**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Zie [Servicelimieten in Azure Cognitive Search](../../search/search-limits-quotas-capacity.md)voor meer informatie over limieten op een gedetailleerder niveau, zoals documentgrootte, query's per seconde, sleutels, aanvragen en antwoorden.

## <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services-limieten

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB-limieten

Zie [Limieten in Azure Cosmos DB](../../cosmos-db/concepts-limits.md)voor Azure Cosmos DB - Voor Azure Cosmos DB-limieten .

## <a name="azure-data-explorer-limits"></a>Azure Data Explorer-limieten

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Zie [Beperkingen in Azure Database voor MySQL](../../mysql/concepts-limits.md)voor Azure Database voor MySQL .

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Zie Beperkingen in Azure Database [voor PostgreSQL-limieten voor](../../postgresql/concepts-limits.md)Azure Database voor PostgreSQL .

## <a name="azure-functions-limits"></a>Azure-functieslimieten

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes-servicelimieten

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning-limieten

De nieuwste waarden voor Azure Machine Learning Compute-quota zijn te vinden op de [quotumpagina azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Azure Maps-limieten

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Monitor-limieten

### <a name="alerts"></a>Waarschuwingen

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Actiegroepen

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Logboekquery's en taal

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics-werkruimten

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure-beleidslimieten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure SignalR-servicelimieten

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Back-uplimieten

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batchlimieten

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Klassieke implementatiemodellimieten

Als u een klassiek implementatiemodel gebruikt in plaats van het Azure Resource Manager-implementatiemodel, gelden de volgende limieten.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limieten containerinstanties

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limieten containerregister

In de volgende tabel worden de functies en limieten van de serviceniveaus Basic, Standard en Premium [beschreven.](../../container-registry/container-registry-skus.md)

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limieten voor contentdeliverynetwerk

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limieten voor gegevensfabrieken

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics-limieten

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Limieten voor Gegevensmeerarchief

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limieten voor gegevensshare

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Servicelimieten voor databasemigratie

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Limieten voor gebeurtenisrasters

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limieten voor gebeurtenishubs

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Limieten voor identiteitsbeheer

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>IoT Centrale limieten
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT-hublimieten

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limieten voor het inrichten van IoT-hubapparaten

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limieten voor belangrijke kluizen

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Limieten voor mediaservices

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (verouderd)

Zie [Media Services v2 (legacy)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations) voor beperkingen die specifiek zijn voor Media Services v2 (legacy)

## <a name="mobile-services-limits"></a>Limieten voor mobiele diensten

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limieten voor verificatie met meerdere factoren

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Netwerklimieten

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute-limieten

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Virtuele WAN-limieten

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limieten voor toepassingsgateway

De volgende tabel is van toepassing op v1, v2, Standard en WAF SKU's, tenzij anders vermeld.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limieten voor netwerkwatcher

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link-limieten

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Limieten voor verkeersbeheer

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure Bastion-limieten

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS-limieten

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure Firewall-limieten

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure Front Door Service-limieten

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limieten voor meldingshubs

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Op rollen gebaseerde toegangscontrolelimieten

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Service Bus limieten

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery-limieten

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL-databaselimieten

Zie [SQL Database-bronlimieten voor afzonderlijke databases,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md) [SQL Database-bronlimieten voor elastische groepen en gepoolde databases](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)en SQL [Database-bronlimieten voor beheerde exemplaren](../../sql-database/sql-database-managed-instance-resource-limits.md)voor SQL Database-resourcelimieten .

## <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse-limieten

Zie [SQL Data Warehouse-resourcelimieten](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)voor SQL Data Warehouse-limieten.

## <a name="storage-limits"></a>Opslaglimieten

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts voor](../../storage/common/scalability-targets-standard-account.md)meer informatie over limieten voor standaardopslagaccounts.

### <a name="storage-resource-provider-limits"></a>Limieten voor opslagresources

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob-opslaglimieten

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure-bestandenlimieten

Zie schaalbaarheid en [prestatiedoelen voor Azure Files](../../storage/files/storage-files-scale-targets.md)voor meer informatie over azure-bestanden.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Azure File Sync-limieten

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure Queue-opslaglimieten

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure Table-opslaglimieten

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Schijflimieten voor virtuele machines

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Zie [Virtuele machineformaten](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.

### <a name="managed-virtual-machine-disks"></a>Beheerde virtuele machineschijven

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Onbeheerde virtuele machineschijven

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple-systeemlimieten

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limieten voor Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limieten voor virtuele machines

### <a name="virtual-machines-limits"></a>Limieten voor virtuele machines

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limieten voor virtuele machines - Azure Resource Manager

De volgende limieten zijn van toepassing wanneer u Azure Resource Manager en Azure-brongroepen gebruikt.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limieten voor gedeelde afbeeldingengalerij

Er zijn limieten per abonnement voor het implementeren van bronnen met gedeelde galerieën:

- 100 gedeelde beeldgalerijen, per abonnement, per regio
- 1.000 beelddefinities, per abonnement, per regio
- 10.000 afbeeldingsversies, per abonnement, per regio

## <a name="virtual-machine-scale-sets-limits"></a>Virtuele machineschaal stelt limieten in

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Zie ook

* [Azure-limieten en -verhogingen begrijpen](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Virtuele machine- en cloudserviceformaten voor Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Groottes voor Azure Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)
* [Naamgevingsregels en -beperkingen voor Azure-resources](resource-name-rules.md)
