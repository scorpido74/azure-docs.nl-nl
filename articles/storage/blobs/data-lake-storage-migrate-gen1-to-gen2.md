---
title: Azure Data Lake Storage migreren van Gen1 naar Gen2
description: Migreer Azure Data Lake Storage van Gen1 naar Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fb982324b66c5ac0d2db00eb906ed850827bc72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533280"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Azure Data Lake Storage migreren van Gen1 naar Gen2

U uw gegevens, workloads en toepassingen migreren van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 is gebouwd op [Azure Blob-opslag](storage-blobs-introduction.md) en biedt een reeks mogelijkheden die zijn gewijd aan big data-analyses. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combineert functies van [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals bestandssysteemsemantiek, directory- en bestandsniveaubeveiliging en -schaal met lage kosten, gelaagde opslag, mogelijkheden met hoge beschikbaarheid/noodherstel van Azure [Blob-opslag](storage-blobs-introduction.md).

> [!NOTE]
> Voor eenvoudiger lezen gebruikt dit artikel de term *Gen1* om te verwijzen naar Azure Data Lake Storage Gen1 en de term *Gen2* om te verwijzen naar Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Aanbevolen aanpak

Om te migreren naar Gen2, raden we de volgende aanpak aan.

:heavy_check_mark: Stap 1: Beoordeel de bereidheid

:heavy_check_mark: Stap 2: Voorbereiden om te migreren

:heavy_check_mark: Stap 3: Werkbelasting voor gegevens en toepassingen migreren

:heavy_check_mark: Stap 4: Cutover van Gen1 naar Gen2

> [!NOTE]
> Gen1 en Gen2 zijn verschillende diensten, er is geen in-place upgrade ervaring, opzettelijke migratie-inspanning vereist. 

### <a name="step-1-assess-readiness"></a>Stap 1: Beoordeel de gereedheid

1. Meer informatie over het [Data Lake Storage Gen2-aanbod;](https://azure.microsoft.com/services/storage/data-lake-storage/) het zijn voordelen, kosten en algemene architectuur. 

2. [Vergelijk de mogelijkheden](#gen1-gen2-feature-comparison) van Gen1 met die van Gen2. 

3. Bekijk een lijst met [bekende problemen](data-lake-storage-known-issues.md) om eventuele hiaten in functionaliteit te beoordelen.

4. Gen2 ondersteunt Blob-opslagfuncties zoals [diagnostische logboekregistratie,](../common/storage-analytics-logging.md) [toegangslagen](storage-blob-storage-tiers.md)en [blob-opslaglevenscyclusbeheerbeleid](storage-lifecycle-management-concepts.md). Als u interessant bent in het gebruik van een van deze functies, bekijkt u het [huidige ondersteuningsniveau.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)

5. Bekijk de huidige status van [Azure-ecosysteemondersteuning](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) om ervoor te zorgen dat Gen2 alle services ondersteunt waarvan uw oplossingen afhankelijk zijn.

### <a name="step-2-prepare-to-migrate"></a>Stap 2: Voorbereiden om te migreren

1. Identificeer de gegevenssets die u migreert.

   Maak van deze gelegenheid gebruik om gegevenssets op te schonen die u niet meer gebruikt. Tenzij u van plan bent om al uw gegevens in één keer te migreren, neemt u deze tijd om logische groepen gegevens te identificeren die u gefaseerd migreren.
   
2. Bepaal de impact die een migratie heeft op uw bedrijf.

   Bedenk bijvoorbeeld of u zich downtime veroorloven terwijl de migratie plaatsvindt. Deze overwegingen kunnen u helpen om een geschikt migratiepatroon te identificeren en de meest geschikte tools te kiezen.

3. Maak een migratieplan. 

   Wij raden deze [migratiepatronen](#migration-patterns)aan. U een van deze patronen kiezen, ze combineren of een eigen patroon ontwerpen.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Stap 3: Gegevens, workloads en toepassingen migreren

Migreer gegevens, workloads en toepassingen met het patroon dat u verkiest. We raden u aan scenario's stapsgewijs te valideren.

1. [Maak een opslagaccount](data-lake-storage-quickstart-create-account.md) en schakel de functie hiërarchische naamruimte in. 

2. Migreer uw gegevens. 

3. Configureer [services in uw workloads](data-lake-storage-integrate-with-azure-services.md) om naar uw Gen2-eindpunt te wijzen. 
   
4. Toepassingen bijwerken om Gen2 API's te gebruiken. Zie gidsen voor [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python,](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) en [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Scripts bijwerken om Gegevens Lake Storage Gen2 [PowerShell-cmdlets](data-lake-storage-directory-file-acl-powershell.md)en [Azure CLI-opdrachten](data-lake-storage-directory-file-acl-cli.md)te gebruiken.
   
6. Zoek naar URI-verwijzingen die `adl://` de tekenreeks bevatten in codebestanden of in Databricks-notitieblokken, Apache Hive HQL-bestanden of een ander bestand dat wordt gebruikt als onderdeel van uw workloads. Vervang deze verwijzingen door de [Gen2-geformatteerde URI](data-lake-storage-introduction-abfs-uri.md) van uw nieuwe opslagaccount. Bijvoorbeeld: de Gen1 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` URI: `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`zou kunnen worden . 

7. Configureer de beveiliging van uw account om [RBAC-rollen (Role-based access control),](../common/storage-auth-aad-rbac-portal.md) [beveiliging op bestands- en mapniveau](data-lake-storage-access-control.md)en [Azure Storage-firewalls en virtuele netwerken](../common/storage-network-security.md)op te nemen.

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Stap 4: Cutover van Gen1 naar Gen2

Nadat u er zeker van bent dat uw toepassingen en workloads stabiel zijn op Gen2, u Gen2 gaan gebruiken om aan uw bedrijfsscenario's te voldoen. Schakel de resterende pijplijnen die op Gen1 worden uitgevoerd uit en schakel uw Gen1-account uit. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2-mogelijkheden

Deze tabel vergelijkt de mogelijkheden van Gen1 met die van Gen2.

|Onderwerp |Gen1 Gen1   |Gen2 Gen2 |
|---|---|---|
|Gegevensorganisatie|[Hiërarchische naamruimte](data-lake-storage-namespace.md)<br>Ondersteuning voor bestanden en mappen|[Hiërarchische naamruimte](data-lake-storage-namespace.md)<br>Ondersteuning voor container-, bestands- en map |
|Geo-redundantie| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[Aad beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Serviceprincipals](../../active-directory/develop/app-objects-and-service-principals.md)|[Aad beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Serviceprincipals](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Gedeelde toegangssleutel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autorisatie|Management - [RBAC](../../role-based-access-control/overview.md)<br>Gegevens – [ACL's](data-lake-storage-access-control.md)|Management – [RBAC](../../role-based-access-control/overview.md)<br>Gegevens - [ACL's](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Versleuteling – Gegevens in rust|Serverzijde – met [door Microsoft beheerde](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) of [door de klant beheerde](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) sleutels|Serverzijde – met [door Microsoft beheerde](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) of [door de klant beheerde](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) sleutels|
|Ondersteuning voor VNET|[VNET-integratie](../../data-lake-store/data-lake-store-network-security.md)|[Serviceeindpunten](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Privéeindpunten (openbare preview)](../common/storage-private-endpoints.md)|
|Ervaring met ontwikkelaars|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md) (In openbare preview)|
|Diagnostische logboeken|Klassieke logboeken<br>[Azure Monitor geïntegreerd](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klassieke logboeken](../common/storage-analytics-logging.md) (In openbare preview)<br>Azure-monitorintegratie – tbd-tijdlijn|
|Ecosysteem|[HDInsight (3.6),](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) [Azure Databricks (3.1 en hoger),](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html) [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0),](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) [Azure Databricks (5.1 en hoger),](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2) [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 naar Gen2 patronen

Kies een migratiepatroon en wijzig dat patroon indien nodig.

|||
|---|---|
|**Lift en Shift**|Het eenvoudigste patroon. Ideaal als uw gegevenspijplijnen downtime kunnen veroorloven.|
|**Incrementele kopie**|Vergelijkbaar met *tillen en schakelen,* maar met minder downtime. Ideaal voor grote hoeveelheden gegevens die langer duren om te kopiëren.|
|**Dubbele pijplijn**|Ideaal voor pijpleidingen die zich geen downtime kunnen veroorloven.|
|**Bidirectionele synchronisatie**|Vergelijkbaar met *dual pipeline,* maar met een meer gefaseerde aanpak die geschikt is voor meer gecompliceerde pijpleidingen.|

Laten we eens een kijkje nemen op elk patroon.
 
### <a name="lift-and-shift-pattern"></a>Lift- en schakelpatroon

Dit is het eenvoudigste patroon.

1. Stop alle schrijft aan Gen1.

2. Gegevens verplaatsen van Gen1 naar Gen2. We raden [Azure Data Factory aan.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL's kopiëren met de gegevens.

3. Wijs bewerkingen en workloads aan Gen2.

4. Ontmanteld gen1.

> [!div class="mx-imgBorder"]
> ![lift- en schakelpatroon](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Overwegingen voor het gebruik van het lift- en ploegenpatroon

:heavy_check_mark: Cutover van Gen1 naar Gen2 voor alle workloads tegelijk.

:heavy_check_mark: Verwacht downtime tijdens de migratie en de cutover periode.

:heavy_check_mark: Ideaal voor pijplijnen die downtime kunnen veroorloven en alle apps kunnen in één keer worden geüpgraded.

### <a name="incremental-copy-pattern"></a>Incrementeel kopieerpatroon

1. Begin met het verplaatsen van gegevens van Gen1 naar Gen2. We raden [Azure Data Factory aan.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL's kopiëren met de gegevens.

2. Kopieer stapsgewijs nieuwe gegevens van Gen1.

3. Nadat alle gegevens zijn gekopieerd, stopt u alle schrijft naar Gen1 en wijs workloads naar Gen2.

4. Ontmanteld gen1.

> [!div class="mx-imgBorder"]
> ![Incrementeel kopieerpatroon](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Overwegingen voor het gebruik van het incrementele kopieerpatroon:

:heavy_check_mark: Cutover van Gen1 naar Gen2 voor alle workloads tegelijk.

:heavy_check_mark: Verwacht alleen downtime tijdens de cutover-periode.

:heavy_check_mark: Ideaal voor pijplijnen waarbij alle apps in één keer zijn bijgewerkt, maar de gegevenskopie meer tijd nodig heeft.

### <a name="dual-pipeline-pattern"></a>Patroon van dubbele pijpleidingen

1. Gegevens verplaatsen van Gen1 naar Gen2. We raden [Azure Data Factory aan.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL's kopiëren met de gegevens.

2. Nieuwe gegevens inslikken voor zowel Gen1 als Gen2.

3. Richt workloads op Gen2.

4. Stop alle schrijft aan Gen1 en dan ontmantelen Gen1.

> [!div class="mx-imgBorder"]
> ![Patroon van dubbele pijpleidingen](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Overwegingen voor het gebruik van het dubbele pijplijnpatroon:

:heavy_check_mark: Gen1- en Gen2-pijpleidingen lopen naast elkaar.

:heavy_check_mark: Ondersteunt nul downtime.

:heavy_check_mark: Ideaal in situaties waarin uw workloads en applicaties zich geen downtime kunnen veroorloven en u beide opslagaccounts opnemen.

### <a name="bi-directional-sync-pattern"></a>Bidirectioneel synchronisatiepatroon

1. Bidirectionele replicatie instellen tussen Gen1 en Gen2. Wij raden [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)aan. Het biedt een reparatiefunctie voor bestaande gegevens.

3. Wanneer alle zetten zijn voltooid, stopt u alle schrijft naar Gen1 en schakelt u bidirectionele replicatie uit.

4. Ontmanteld gen1.

> [!div class="mx-imgBorder"]
> ![Bidirectioneel patroon](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Overwegingen voor het gebruik van het bidirectionele synchronisatiepatroon:

:heavy_check_mark: Ideaal voor complexe scenario's waarbij een groot aantal pijplijnen en afhankelijkheden betrokken zijn waarbij een gefaseerde aanpak logischer kan zijn.  

:heavy_check_mark: Migratie-inspanning is hoog, maar het biedt side-by-side ondersteuning voor Gen1 en Gen2.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de verschillende onderdelen van het instellen van beveiliging voor een opslagaccount. Zie [azure storage-beveiligingshandleiding](../common/storage-security-guide.md).
- Optimaliseer de prestaties voor uw Data Lake Store. Zie [Azure Data Lake Storage Gen2 optimaliseren voor prestaties](data-lake-storage-performance-tuning-guidance.md)
- Bekijk de aanbevolen procedures voor het beheren van uw Data Lake Store. Aanbevolen [procedures voor het gebruik van Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

