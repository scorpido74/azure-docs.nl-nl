---
title: Veelgestelde vragen over doorvoer in Azure Cosmos DB-automatische pilootmodus
description: Veelgestelde vragen over de automatische pilootmodus voor Azure Cosmos DB-databases en -containers
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483308"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Veelgestelde vragen over de ingerichte doorvoer in de automatische pilootmodus azure cosmos DB (Preview)
Met de automatische pilootmodus beheert en schaalt Azure Cosmos DB automatisch de RU/s van uw container of database op basis van het gebruik. Dit artikel beantwoordt veelgestelde vragen over de automatische pilootmodus. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Wordt de automatische pilootmodus ondersteund voor alle API's?
Ja, de automatische pilootmodus wordt ondersteund voor alle API's: Core (SQL), Gremlin, Table, Cassandra en API voor MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Wordt de automatische pilootmodus ondersteund voor multi-master accounts?
Ja, de automatische pilootmodus wordt ondersteund voor multi-master accounts. De max RU/s zijn beschikbaar in elke regio die wordt toegevoegd aan het Cosmos-account. 

### <a name="what-is-the-pricing-for-autopilot"></a>Wat is de prijsstelling voor de automatische piloot?
Raadpleeg de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/) azure cosmos DB voor meer informatie. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Hoe schakel ik automatische piloot in voor mijn containers of databases?
De automatische pilootmodus kan worden ingeschakeld op nieuwe containers en databases die zijn gemaakt met behulp van de Azure-portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Is er CLI- of SDK-ondersteuning voor het maken van containers of databases met de automatische pilootmodus?
Momenteel u in de preview-versie alleen resources maken met de automatische pilootmodus van de Azure-portal. Ondersteuning voor CLI en SDK is nog niet beschikbaar.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Kan ik automatische piloot inschakelen op een bestaande container of een database?
Momenteel u automatische piloot inschakelen op nieuwe containers en databases bij het maken ervan. Ondersteuning voor het inschakelen van de automatische pilootmodus op bestaande containers en databases is nog niet beschikbaar. U bestaande containers migreren naar een nieuwe container met [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) of de feed [wijzigen.](change-feed.md) 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Kan ik de automatische pilootmodus uitschakelen in een container of database?
Ja, u de automatische piloot uitschakelen door over te schakelen naar de optie 'Handmatig' voor de ingerichte doorvoer. In de preview-release, na het overschakelen van de automatische pilootmodus naar de handmatige modus, u de automatische piloot niet opnieuw inschakelen voor dezelfde bron. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Wordt de automatische pilootmodus ondersteund voor gedeelde doorvoerdatabases?
Ja, de automatische pilootmodus wordt ondersteund voor gedeelde doorvoerdatabases. Als u deze functie wilt inschakelen, selecteert u de automatische pilootmodus en de optie **Doorvoervoorziening** bij het maken van de database. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Wat is het aantal toegestane verzamelingen per gedeelde doorvoerdatabase wanneer de automatische piloot is ingeschakeld?
Voor gedeelde doorvoerdatabases met automatische pilootmodus ingeschakeld, is het aantal toegestane verzamelingen: MIN(25, Max RU/s van database / 1000). Als de maximale doorvoer van de database bijvoorbeeld 20.000 RU/s is, kan de database MIN(25, 20.000 RU/s / 1000) = 20 verzamelingen hebben. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Wat is de opslaglimiet die is gekoppeld aan elke optie voor max RU/s?  
De opslaglimiet in GB voor elke max RU/s is: Max RU/s database of container / 100. Als de maximale RU/s bijvoorbeeld 20.000 RU/s is, kan de resource 200 GB opslagruimte ondersteunen. Zie het [artikel over automatische pilootlimieten](provision-throughput-autopilot.md#autopilot-limits) voor de beschikbare max RU/s en opslagopties. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Wat gebeurt er als ik de opslaglimiet voor mijn maximale doorvoer verschik?
Als de opslaglimiet die is gekoppeld aan de maximale doorvoer van de database of container wordt overschreden, verhoogt Azure Cosmos DB automatisch de maximale doorvoer naar de op één na hoogste laag die dat opslagniveau kan ondersteunen. Stel dat een database of container is ingericht met de maximale doorvoeroptie van 4000 RU/s, die een opslaglimiet van 50 GB heeft. Als de opslag van de resource toeneemt tot 100 GB, worden de maximale RU/s van de database of container automatisch verhoogd tot 20.000 RU/s, wat tot 200 GB kan ondersteunen. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Hoe snel zal de automatische piloot op en neer schalen op basis van pieken in het verkeer?
Autopilot zal onmiddellijk opschalen of schalen van de RU / s binnen het minimum en maximale RU / s bereik, op basis van inkomende verkeer. Facturering gebeurt op een granulariteit van 1 uur, waarbij u in een bepaald uur kosten in rekening wordt gebracht voor de hoogste RU/s. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Kan ik een aangepaste maximumdoorvoerwaarde (RU/s) opgeven voor de automatische pilootmodus?
Momenteel u tijdens de preview-release kiezen uit [vier opties](provision-throughput-autopilot.md#autopilot-limits) voor maximale doorvoer (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Kan ik de maximale RU/s (naar een hogere laag) in de database of container verhogen? 
Ja. In de optie **Schalen & instellingen** voor uw container of de optie **Schalen** voor uw database u een hogere max RU/s voor de automatische piloot selecteren. Dit is een asynchrone scale-up bewerking die ergens kan duren om te voltooien (meestal 4-6 uur, afhankelijk van de RU / s geselecteerd) als de service voorzieningen meer middelen ter ondersteuning van de hogere schaal. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Kan ik de maximale RU/s (naar een lagere laag) in de database of container verlagen?
Ja. Zolang de huidige opslag van de database of container onder de [opslaglimiet](#what-is-the-storage-limit-associated-with-each-max-rus-option) zit die is gekoppeld aan de maximale RU/s-laag waarnaar u wilt schalen, u de maximale RU/s naar die laag verlagen. Als u bijvoorbeeld 20.000 RU/s als de max RU/s hebt geselecteerd, u de maximale RU/s naar 4000 RU/s schalen als u minder dan 50 GB opslagruimte hebt (de opslaglimiet die is gekoppeld aan 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Wat is de mapping tussen de max RU/s en fysieke partities?
Wanneer u de maximale RU/s voor het eerst selecteert, zal Azure Cosmos DB voorzien in: Max RU/s / 10.000 RU/s = # van fysieke partities. Elke [fysieke partitie](partition-data.md#physical-partitions) kan maximaal 10.000 RU/s en 50 GB opslag ondersteunen. Naarmate de opslaggrootte toeneemt, splitst Azure Cosmos DB automatisch de partities om meer fysieke partities toe te voegen om de opslagtoename te verwerken of verhoogt u de maximale RU/s-laag als de opslag [de bijbehorende limiet overschrijdt.](#what-is-the-storage-limit-associated-with-each-max-rus-option) 

De Max RU/s van de database of container is gelijkmatig verdeeld over alle fysieke partities. Dus, de totale doorvoer een enkele fysieke partitie kan schalen naar is: Max RU / s van database of container / # fysieke partities. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Wat gebeurt er als binnenkomende aanvragen de maximale RU/s van de database of container overschrijden?
Als de totale verbruikte RU/s de maximale RU/s van de container of database overschrijdt, worden aanvragen die de maximale RU/s overschrijden, beperkt en wordt een statuscode van 429 weergegeven. Verzoeken die resulteren in meer dan 100% genormaliseerd gebruik worden ook beperkt. Genormaliseerd gebruik wordt gedefinieerd als de max van het RU/s-gebruik in alle fysieke partities. Stel dat uw maximale doorvoer 20.000 RU/s bedraagt en u twee fysieke partities hebt, P_1 en P_2, die elk kunnen worden geschaald naar 10.000 RU/s. In een bepaalde seconde, als P_1 6000 RU's heeft gebruikt en P_2 8000 RU' s, is het genormaliseerde gebruik MAX(6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

> [!NOTE]
> De Azure Cosmos DB-client SDKs en hulpprogramma's voor gegevensinvoer (Azure Data Factory, bulkexecutorlibrary) proberen automatisch opnieuw op 429s, dus af en toe 429s zijn prima. Een aanhoudend hoog aantal 429s kan erop wijzen dat u de maximale RU/s moet verhogen of uw partitioneringsstrategie moet bekijken voor een [hete partitie.](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Is het nog steeds mogelijk om te zien 429s (beperking / tarief beperken) wanneer de automatische piloot is ingeschakeld? 
Ja. Het is mogelijk om 429s in twee scenario's te zien. Ten eerste, wanneer de totale verbruikte RU/s de maximale RU/s van de container of database overschrijdt, zal de service de aanvragen dienovereenkomstig beperken. 

Ten tweede, als er een hot partition is, d.w.z. een logische partitiesleutelwaarde met een onevenredig hoger aantal aanvragen in vergelijking met andere partitiesleutelwaarden, is het mogelijk dat de onderliggende fysieke partitie zijn RU/s-budget overschrijdt. Als een best practice, om hot partities te voorkomen, [kies een goede partitie sleutel](partitioning-overview.md#choose-partitionkey) die resulteert in een gelijkmatige verdeling van zowel opslag en doorvoer. 

Als u bijvoorbeeld de optie 20.000 RU/s max throughput selecteert en 200 GB opslagruimte hebt, met vier fysieke partities, kan elke fysieke partitie automatisch worden geschaald tot 5000 RU/s. Als er een hete partitie op een bepaalde logische partitiesleutel is geweest, ziet u 429s wanneer de onderliggende fysieke partitie zich in meer dan 5000 RU/s bevindt, d.w.z. meer dan 100% genormaliseerd gebruik.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inschakelen van automatische piloot in een Azure Cosmos-container of -database](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Meer informatie over de [voordelen van de automatische piloot](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Meer informatie over [logische en fysieke partities](partition-data.md).
