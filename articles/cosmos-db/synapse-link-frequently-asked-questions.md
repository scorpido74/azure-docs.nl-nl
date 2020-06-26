---
title: Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over de Synapse-koppeling voor Azure Cosmos DB op gebieden zoals facturering, analytische opslag, beveiliging, time to Live in een analytische opslag.
author: srchi
ms.author: srchi
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: d16da1588c2fa90c4a40a829ed660fd089ff29ac
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392366"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB

Met Azure Synapse link voor Azure Cosmos DB maakt u een nauwe integratie tussen Azure Cosmos DB en Azure Synapse Analytics. Hierdoor kunnen klanten bijna realtime analyses uitvoeren via hun operationele gegevens met volledige prestatie isolatie van hun transactionele werk belastingen en zonder een ETL-pijp lijn. In dit artikel vindt u antwoorden op veelgestelde vragen over de Synapse-koppeling voor Azure Cosmos DB.

## <a name="general-faq"></a>Veelgestelde algemene vragen

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Wordt de Synapse-koppeling ondersteund voor alle Api's van Azure Cosmos DB?
In de open bare preview-versie wordt Synapse link alleen ondersteund voor de Azure Cosmos DB SQL-API (core). Ondersteuning voor de API van Cosmos DB voor Mongo DB-& Cassandra-API is momenteel onder gated preview.  Neem contact op met het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com)om toegang aan te vragen voor gateded preview.

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Wordt de Synapse-koppeling ondersteund voor Azure Cosmos-accounts met meerdere regio's?
Ja, voor Azure Cosmos-accounts met meerdere regio's, worden de gegevens die zijn opgeslagen in de analytische opslag ook wereld wijd gedistribueerd. Analytische query's die vanuit Azure Synapse Analytics worden uitgevoerd, kunnen worden bediend vanuit de dichtstbijzijnde lokale regio, ongeacht de ene schrijf regio (Single Master) of meerdere schrijf regio's (ook wel bekend als multi-master).

Wanneer u van plan bent een Azure Cosmos-account met meerdere regio's te configureren met ondersteuning voor analytische opslag, is het raadzaam om alle benodigde regio's toe te voegen op het moment dat het account wordt gemaakt.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Kan ik kiezen om Synapse-koppeling in te scha kelen voor slechts bepaalde regio's en niet voor alle regio's in een account met meerdere regio's?
Wanneer de Synapse-koppeling is ingeschakeld voor een account met meerdere regio's in de preview-versie, wordt het analytische archief in alle regio's gemaakt. De onderliggende gegevens worden geoptimaliseerd voor de door Voer en transactionele consistentie in het transactionele archief.

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Kan ik de Synapse-koppelings functie voor mijn Azure Cosmos-account uitschakelen?
Nadat de Synapse Link-functie op accountniveau is ingeschakeld, kunt u deze momenteel niet uitschakelen.  Als u de mogelijkheid wilt uitschakelen, moet u een nieuw Azure Cosmos-account verwijderen en opnieuw maken.

Begrijp dat u **geen** facturerings implicaties hebt als de Synapse-koppelings mogelijkheid is ingeschakeld op account niveau, maar er geen containers voor het analytische archief zijn ingeschakeld.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB-analytische archief

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Kan ik het analytische archief op bestaande containers inschakelen?
Op dit moment kan de analytische opslag alleen worden ingeschakeld voor nieuwe containers (zowel in nieuwe als bestaande accounts).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Kan ik analytische opslag op mijn Azure Cosmos-containers uitschakelen nadat ik deze heb ingeschakeld tijdens het maken van de container?
Op dit moment kan de analytische opslag niet worden uitgeschakeld op een Azure Cosmos-container nadat deze is ingeschakeld tijdens het maken van de container.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>Wordt het analytische archief ondersteund voor Azure Cosmos-containers met een door Voer ingericht voor automatisch schalen?
Ja, de analytische opslag kan worden ingeschakeld op containers met de door Voer ingericht voor automatisch schalen.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Is er een effect op Azure Cosmos DB transactionele opslag met het RUs-aanbod?
Azure Cosmos DB garandeert de prestaties van de isolatie tussen transactionele en analytische werk belastingen. Het inschakelen van het analytische archief in een container heeft geen invloed op de RU/s die zijn ingericht in de Azure Cosmos DB transactionele Store. De trans acties (Lees & schrijven) en opslag kosten voor de analytische opslag worden afzonderlijk in rekening gebracht. Zie de [prijzen voor de Azure Cosmos DB-analytische opslag](analytical-store-introduction.md#analytical-store-pricing) voor meer informatie.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Worden de bewerkingen voor verwijderen en bijwerken in de transactionele Store weer gegeven in de analytische opslag? 
Ja, verwijderingen en updates van de gegevens in het transactionele archief worden weer gegeven in de analytische opslag. U kunt de Time to Live (TTL) configureren op de container om historische gegevens op te nemen, zodat de analytische opslag alle versies van items behoudt die voldoen aan de criteria van de analytische TTL. Zie het [overzicht van de analyse-TTL](analytical-store-introduction.md#analytical-ttl) voor meer informatie.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Kan ik verbinding maken met een analytische archief met andere analyse-engines dan Azure Synapse Analytics?
Het openen en uitvoeren van query's in de analytische opslag is alleen mogelijk met behulp van de verschillende runtimes die door Azure Synapse Analytics worden voorzien. De analytische opslag kan worden doorzocht en geanalyseerd met:

* Synapse Spark met volledige ondersteuning voor scala, Python, SparkSQL en C#. Synapse Spark neemt een centrale plaats in data engineering- en wetenschappelijke scenario's in
* Serverloze SQL met T-SQL-taal en ondersteuning voor bekende BI-hulpprogramma's (bijvoorbeeld Power BI Premium, enzovoort)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Kan ik verbinding maken met het analytische archief vanuit Synapse SQL ingericht?
Op dit moment is het analytische archief niet toegankelijk vanuit Synapse SQL provisioned.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Kan ik de resultaten van de query aggregatie terugschrijven van Synapse terug naar de analytische opslag?
Analytische opslag is een alleen-lezen archief in een Azure Cosmos-container. U kunt de aggregatie resultaten dus niet rechtstreeks naar de analytische opslag schrijven, maar ze kunnen wel naar het Azure Cosmos DB transactionele archief van een andere container schrijven, die later kunnen worden gebruikt als een te leveren laag.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Is de automatische sync-replicatie van transactionele opslag naar de analytische opslag asynchroon of synchroon en wat zijn de latenties? 
De replicatie is asynchroon en op dit moment is de verwachte latentie ongeveer 2 minuten.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Zijn er scenario's waarbij de items uit het transactionele archief niet automatisch worden door gegeven aan de analytische opslag?
Als bepaalde items in uw container het [goed gedefinieerde schema voor analyse](analytical-store-introduction.md#analytical-schema)schenden, worden ze niet opgenomen in de analytische opslag. Als u scenario's hebt geblokkeerd door goed gedefinieerd schema voor analyse, kunt u het [Azure Cosmos DB-team](mailto:cosmosdbsynapselink@microsoft.com) een e-mail sturen.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Kan ik de gegevens in de analytische opslag anders partitioneren dan transactie opslag?
De gegevens in analytische opslag zijn gepartitioneerd op basis van de horizontale partitionering van shards in de transactionele opslag. U kunt momenteel geen andere partitioneringsstrategie voor de analytische opslag kiezen.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Kan ik de manier aanpassen of negeren waarop transactionele gegevens worden omgezet in de kolom indeling in de analytische opslag?
Op dit moment kunt u de gegevens items niet transformeren wanneer ze automatisch worden door gegeven vanuit het transactionele archief naar de analytische opslag. Als u scenario's hebt geblokkeerd door deze beperking, moet u een e-mail sturen naar het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Analytische time to Live (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Is TTl voor analytische gegevens die worden ondersteund op container-en item niveau?
Op dit moment kan TTl voor analytische gegevens alleen worden geconfigureerd op container niveau en is er geen ondersteuning voor het instellen van de analyse-TTL op item niveau.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Kan ik na het instellen van de analyse-TTL op container niveau in een Azure Cosmos DB container later overschakelen naar een andere waarde?
Ja, de analyse-TTL kan worden bijgewerkt naar een wille keurige geldige waarde. Zie het artikel over de [analytische TTL](analytical-store-introduction.md#analytical-ttl) voor meer informatie over de analyse-TTL.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Kan ik een item uit het analytische archief bijwerken of verwijderen nadat het TTL is verwijderd uit het transactionele archief?
Alle transactionele updates en verwijderingen worden gekopieerd naar het analytische archief, maar als het item uit het transactionele archief is verwijderd, kan het niet worden bijgewerkt in de analytische opslag. Zie het artikel over de [analytische TTL](analytical-store-introduction.md#analytical-ttl) voor meer informatie.

## <a name="billing"></a>Billing

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Wat is het facturerings model van de Synapse-koppeling voor Azure Cosmos DB?
[Azure Cosmos DB Analytical Store](analytical-store-introduction.md) is beschikbaar in open bare preview zonder kosten voor de analytische opslag tot en met 30 augustus 2020. Synapse Spark en Synapse SQL worden gefactureerd via [Synapse-service verbruik](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Beveiliging

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Wat zijn de manieren om te verifiëren met de analytische opslag?
Verificatie met de analytische opslag is hetzelfde als een transactioneel archief. Voor een bepaalde data base kunt u verifiëren met de sleutel Master of alleen-lezen. U kunt gebruikmaken van gekoppelde services in Synapse Studio om te voor komen dat de Azure Cosmos DB sleutels in de Spark-notebooks worden geplakt. Toegang tot deze gekoppelde service is beschikbaar voor iedereen die toegang heeft tot de werk ruimte.

## <a name="synapse-run-times"></a>Synapse-uitvoerings tijden

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Wat zijn de momenteel ondersteunde Synapse-uitvoerings tijden om toegang te krijgen tot Azure Cosmos DB Analytical Store?

|Synapse-runtime |Huidige ondersteuning |
|---------|---------|
|Synapse Spark-Pools | Lezen, schrijven (via transactionele Store), tabel, tijdelijke weer gave |
|Synapse SQL serverloze    | Lezen, weer geven (geteste preview-versie)  |
|Synapse SQL ingericht   |  Niet beschikbaar |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Worden mijn Synapse Spark-tabellen gesynchroniseerd met mijn Synapse SQL serverloze tabellen op dezelfde manier als Azure Data Lake?
Deze functie is momenteel niet beschikbaar.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Kan ik Structured streaming streamen vanuit de analytische opslag?
Momenteel wordt de ondersteuning Azure Cosmos DB voor het gebruik van gewijzigde feeds van het transactionele Archief door Spark Structured streaming ondersteund en wordt het nog niet ondersteund vanuit de analytische opslag.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Hoe kan ik in Synapse Studio herkennen of ik verbinding heb met een Azure Cosmos DB-container terwijl het Analytics-archief is ingeschakeld?
Een Azure Cosmos DB container waarvoor een analytische opslag is ingeschakeld, heeft het volgende pictogram:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB container ingeschakeld met analytische archief-pictogram":::

Een transactionele opslag container wordt weer gegeven met het volgende pictogram:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB container ingeschakeld met transactionele Store-pictogram":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Hoe geeft u Azure Cosmos DB referenties door van Synapse Studio?
Er worden momenteel Azure Cosmos DB referenties door gegeven tijdens het maken van de gekoppelde service door de gebruiker die toegang heeft tot de Azure Cosmos DB-data bases. Toegang tot de Store is beschikbaar voor andere gebruikers die toegang hebben tot de werk ruimte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [voor delen van Synapse-koppeling](synapse-link.md#synapse-link-benefits)

* Meer informatie over de [integratie tussen de Synapse-koppeling en de Azure Cosmos DB](synapse-link.md#synapse-link-integration).
