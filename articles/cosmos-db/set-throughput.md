---
title: Door Voer in te richten op Azure Cosmos-containers en-data bases
description: Meer informatie over het instellen van ingerichte door Voer voor uw Azure Cosmos-containers en-data bases.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 050da712df6dad872fc03bd6ca79bbdf2a3e1753
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563205"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Inleiding tot ingerichte door Voer in Azure Cosmos DB

Met Azure Cosmos DB kunt u een ingerichte door Voer instellen voor uw data bases en containers. Er zijn twee soorten ingerichte door Voer, standaard (hand matig) of automatisch schalen. In deze artikelen vindt u een overzicht van de werking van ingerichte door voer. 

Een Azure Cosmos-database is een beheereenheid voor een set containers. Een database bestaat uit een set schema-agnostische containers. Een Azure Cosmos-container is de eenheid van schaalbaarheid voor zowel de doorvoer als de opslag. Een container is horizontaal gepartitioneerd over een set machines binnen een Azure-regio, en wordt gedistribueerd in alle Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-account.

Met Azure Cosmos DB kunt u de door Voer op twee nauw keurigheid inrichten:
 
- Azure Cosmos-containers
- Azure Cosmos-databases

## <a name="set-throughput-on-a-container"></a>Door Voer voor een container instellen  

De door Voer die is ingericht voor een Azure Cosmos-container is exclusief gereserveerd voor die container. De container ontvangt de ingerichte door Voer altijd. De ingerichte door Voer voor een container wordt financieel ondersteund door service overeenkomsten. Zie de [door Voer inrichten op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie over het configureren van de standaard doorvoer (hand matig) voor een container. Zie automatisch [schalen door Voer inrichten](how-to-provision-autoscale-throughput.md)voor meer informatie over het configureren van de door Voer van automatisch schalen op een container.

Het instellen van een ingerichte door Voer voor een container is de meest gebruikte optie. U kunt de door Voer voor een container elastisch schalen door een wille keurige hoeveelheid door Voer in te stellen met behulp van [aanvraag eenheden (RUs)](request-units.md). 

De door Voer die is ingericht voor een container wordt gelijkmatig verdeeld over de fysieke partities en er wordt ervan uitgegaan dat er een goede partitie sleutel is die de logische partities gelijkmatig verdeelt tussen de fysieke partities. de door Voer wordt ook gelijkmatig verdeeld over alle logische partities van de container. U kunt de door Voer voor logische partities niet selectief opgeven. Omdat een of meer logische partities van een container worden gehost door een fysieke partitie, behoren de fysieke partities uitsluitend tot de container en ondersteunen ze de door Voer ingericht op de container. 

Als de werk belasting die wordt uitgevoerd op een logische partitie, groter is dan de door Voer die is toegewezen aan de onderliggende fysieke partitie, is het mogelijk dat uw bewerkingen een beperkt aantal zijn. Wat bekend staat als een _hete partitie_ , treedt op wanneer één logische partitie onevenredig meer aanvragen heeft dan andere partitie sleutel waarden.

Wanneer de snelheids beperking optreedt, kunt u de ingerichte door Voer voor de gehele container verhogen of de bewerking opnieuw uitvoeren. U moet er ook voor zorgen dat u een partitie sleutel kiest waarmee de opslag en het aanvraag volume gelijkmatig worden verdeeld. Zie [partitioneren en horizon taal schalen in azure Cosmos DB](partition-data.md)voor meer informatie over partitioneren.

We raden u aan om door voer te configureren bij de container granulatie wanneer u gegarandeerde prestaties voor de container wilt.

In de volgende afbeelding ziet u hoe een fysieke partitie als host fungeert voor een of meer logische partities van een container:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Fysieke partitie" border="false":::

## <a name="set-throughput-on-a-database"></a>Door Voer instellen voor een Data Base

> [!NOTE]
> Het inrichten van een door Voer voor een Azure Cosmos-data base is momenteel niet mogelijk in accounts waarin door de [klant beheerde sleutels](how-to-setup-cmk.md) zijn ingeschakeld.

Wanneer u de door Voer inricht in een Azure Cosmos-data base, wordt de door Voer gedeeld via alle containers (shared data base-containers genoemd) in de-data base. Een uitzonde ring hierop is als u een ingerichte door Voer hebt opgegeven voor specifieke containers in de data base. Het delen van de ingerichte door Voer op database niveau tussen de containers is vergelijkbaar met het hosten van een Data Base op een cluster machines. Omdat alle containers in een Data Base de resources delen die beschikbaar zijn op een computer, kunt u natuurlijk geen voorspel bare prestaties op een specifieke container krijgen. Zie [ingerichte door Voer configureren voor een Azure Cosmos-data base](how-to-provision-database-throughput.md)voor meer informatie over het configureren van een ingerichte door Voer voor een Data Base. Zie automatisch [schalen door Voer inrichten](how-to-provision-autoscale-throughput.md)voor meer informatie over het configureren van de door Voer van automatisch schalen op een Data Base.

Door de door Voer voor een Azure Cosmos-data base in te stellen, zorgt u ervoor dat u de ingerichte door Voer voor de Data Base op elk moment ontvangt. Omdat alle containers in de data base de ingerichte door voer hebben gedeeld, biedt Azure Cosmos DB geen voorspel bare doorvoer garanties voor een bepaalde container in die data base. Het gedeelte van de door voer dat een specifieke container kan ontvangen, is afhankelijk van het volgende:

* Het aantal containers.
* De keuze van partitie sleutels voor verschillende containers.
* De verdeling van de werk belasting over verschillende logische partities van de containers. 

We raden u aan de door Voer voor een Data Base te configureren als u de door voer wilt delen in meerdere containers, maar niet de door voer wilt toewijzen aan een bepaalde container. 

De volgende voor beelden laten zien waar het voor keur is om de door Voer in te richten op het niveau van de Data Base:

* Het delen van de ingerichte door Voer van een data base in een set containers is handig voor een multi tenant-toepassing. Elke gebruiker kan worden vertegenwoordigd door een afzonderlijke Azure Cosmos-container.

* Het delen van de ingerichte door Voer van een data base in een set met containers is handig wanneer u een NoSQL-data base, zoals MongoDB of Cassandra, migreert die wordt gehost op een cluster van virtuele machines of van on-premises fysieke servers naar Azure Cosmos DB. Denk na over de ingerichte door Voer die op uw Azure Cosmos-data base is geconfigureerd als een logische equivalent, maar rendabeler en elastisch, tot die van de reken capaciteit van uw MongoDB-of Cassandra-cluster.  

Alle containers die in een Data Base zijn gemaakt met een ingerichte door Voer, moeten worden gemaakt met een [partitie sleutel](partition-data.md). Op een bepaald moment wordt de door Voer toegewezen aan een container in een Data Base verdeeld over alle logische partities van die container. Wanneer u containers hebt waarin de ingerichte door Voer is geconfigureerd voor een Data Base, kunt u de door Voer niet selectief Toep assen op een specifieke container of een logische partitie. 

Als de werk belasting op een logische partitie meer gebruikt dan de door Voer die is toegewezen aan een specifieke logische partitie, zijn uw bewerkingen een beperkt aantal. Wanneer de snelheids beperking optreedt, kunt u de door Voer voor de gehele data base verg Roten of de bewerkingen opnieuw proberen. Zie [logische partities](partition-data.md)voor meer informatie over partitioneren.

Containers in een gedeelde doorvoerdatabase delen de doorvoer (RU/s) die zijn toegewezen aan deze database. U kunt maximaal vier containers hebben met minimaal 400 RU/s in de database. Bij standaard (hand matig) ingerichte door Voer is voor elke nieuwe container na de eerste vier een extra mini maal 100 RU/s vereist. Als u bijvoorbeeld een gedeelde doorvoerdatabase hebt met acht containers, is het minimale aantal RU/s voor de database 800 RU/s. Met de ingerichte door Voer voor automatisch schalen kunt u Maxi maal 25 containers in een Data Base hebben met automatisch schalen Max. 4000 RU/s (schalen tussen 400-4000 RU/s).

> [!NOTE]
> In februari 2020 hebben we een wijziging geïntroduceerd waarmee u Maxi maal 25 containers in een gedeelde doorvoer database kunt hebben, waardoor het delen van de door Voer via de containers beter wordt. Na de eerste 25 containers kunt u meer containers toevoegen aan de Data Base als ze zijn [ingericht met een specifieke door Voer](#set-throughput-on-a-database-and-a-container), die los is van de gedeelde door Voer van de data base.<br>
Als uw Azure Cosmos DB-account al een Data Base met gedeelde door Voer bevat met >= 25 containers, zijn het account en alle andere accounts in hetzelfde Azure-abonnement uitgesloten van deze wijziging. [Neem contact op met product ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u feedback of vragen hebt. 

Als uw workloads het verwijderen en opnieuw maken van alle verzamelingen in een Data Base vereisen, is het raadzaam om de lege data base weg te halen en opnieuw een nieuwe Data Base te maken voordat u de verzameling maakt. In de volgende afbeelding ziet u hoe een fysieke partitie een of meer logische partities kan hosten die deel uitmaken van verschillende containers in een Data Base:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Fysieke partitie" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Door Voer instellen voor een Data Base en een container

U kunt de twee modellen combi neren. De door Voer voor de data base en de container inrichten is toegestaan. In het volgende voor beeld ziet u hoe u standaard (hand matige) ingerichte door Voer kunt inrichten voor een Azure Cosmos-data base en een container:

* U kunt een Azure Cosmos-data base met de naam *Z* maken met de standaard (hand matig) ingerichte door Voer van *"K"* RUs. 
* Maak vervolgens vijf containers met de naam *A*, *B*, *C*, *D*en *E* in de-data base. Zorg ervoor dat u bij het maken van container B een **specifieke door Voer inrichten voor deze container** optie inschakelt en expliciet *' P '* RUs van ingerichte door Voer voor deze container configureert. Houd er rekening mee dat u een gedeelde en toegewezen door Voer alleen kunt configureren bij het maken van de data base en container. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="De door Voer op container niveau instellen":::

* De *"K"* RUs-door Voer wordt gedeeld in de vier containers *A*, *C*, *D*en *E*. De exacte hoeveelheid door Voer die beschikbaar is voor *A*, *C*, *D*of *E* , varieert. Er zijn geen service overeenkomsten voor de door Voer van elke afzonderlijke container.
* De container met de naam *B* is gegarandeerd dat de *"P"* RUs-door Voer altijd wordt opgehaald. Er wordt een back-up gemaakt van service overeenkomsten.

> [!NOTE]
> Een container met ingerichte door Voer kan niet worden geconverteerd naar een gedeelde database container. Een gedeelde database container kan niet worden geconverteerd naar een specifieke door voer.

## <a name="update-throughput-on-a-database-or-a-container"></a>De door Voer van een Data Base of container bijwerken

Nadat u een Azure Cosmos-container of een-Data Base hebt gemaakt, kunt u de ingerichte door Voer bijwerken. Er is geen limiet voor de Maxi maal ingerichte door Voer die u kunt configureren voor de data base of de container. 

Als u de [minimale ingerichte door Voer](concepts-limits.md#storage-and-throughput) van een Data Base of container wilt schatten, zoekt u het maximum van:

* 400 RU/s 
* Huidige opslag in GB * 10 RU/s
* De hoogste RU/s die zijn ingericht voor de data base of container/100
* Aantal containers * 100 RU/s (alleen gedeelde doorvoer database)

De werkelijke minimale RU/s kan variëren, afhankelijk van de configuratie van uw account. U kunt [Azure monitor metrische gegevens](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) gebruiken om de geschiedenis van ingerichte door Voer (ru/s) en opslag voor een bron weer te geven.

U kunt de minimale door Voer van een container of een Data Base via een programma ophalen met behulp van de Sdk's of de waarde in de Azure Portal weer geven. Wanneer u de .NET SDK gebruikt, kunt u met de methode [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) de ingerichte doorvoer waarde schalen. Wanneer u de Java-SDK gebruikt, kunt u met de methode [RequestOptions. setOfferThroughput](sql-api-java-sdk-samples.md) de ingerichte doorvoer waarde schalen. 

Wanneer u de .NET SDK gebruikt, kunt u met de methode [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) de minimale door Voer van een container of een Data Base ophalen. 

U kunt de ingerichte door Voer van een container of een Data Base op elk gewenst moment schalen. Wanneer er een schaal bewerking wordt uitgevoerd om de door voer te verg Roten, kan het langer duren vanwege de systeem taken om de vereiste resources in te richten. U kunt de status van de schaal bewerking controleren in Azure Portal of programmatisch met behulp van de Sdk's. Wanneer u de .NET SDK gebruikt, kunt u de status van de schaal bewerking ophalen met behulp van de- `DocumentClient.ReadOfferAsync` methode.

## <a name="comparison-of-models"></a>Vergelijking van modellen
In deze tabel ziet u een vergelijking tussen het inrichten van de standaard doorvoer (hand matig) voor een Data Base versus een container. 

|**Parameter**  |**Standaard doorvoer (hand matig) voor een Data Base**  |**Standaard (hand matig) door Voer voor een container**|**Door Voer automatisch schalen voor een Data Base** | **Door Voer automatisch schalen op een container**|
|---------|---------|---------|---------|---------|
|Ingangs punt (mini maal RU/s) |400 RU/s. Na de eerste vier containers vereist elke extra container mini maal 100 RU/s</li> |400| Automatisch schalen tussen 400-4000 RU/s. Kan Maxi maal 25 containers bevatten zonder het minimum aantal RU/s per container</li> | Automatisch schalen tussen 400-4000 RU/s.|
|Mini maal RU/s per container|100|400|--|Automatisch schalen tussen 400-4000 RU/s|
|Maximum RUs|Onbeperkt, op de data base.|Onbeperkt, op de container.|Onbeperkt, op de data base.|Onbeperkt, op de container.
|RUs toegewezen of beschikbaar voor een specifieke container|Geen garanties. RUs toegewezen aan een bepaalde container is afhankelijk van de eigenschappen. Eigenschappen kunnen de keuze zijn van partitie sleutels van containers die de door Voer, de distributie van de werk belasting en het aantal containers delen. |Alle RUs-instellingen die in de container zijn geconfigureerd, worden uitsluitend gereserveerd voor de container.|Geen garanties. RUs toegewezen aan een bepaalde container is afhankelijk van de eigenschappen. Eigenschappen kunnen de keuze zijn van partitie sleutels van containers die de door Voer, de distributie van de werk belasting en het aantal containers delen. |Alle RUs-instellingen die in de container zijn geconfigureerd, worden uitsluitend gereserveerd voor de container.|
|Maximale opslag voor een container|Limited.|Onbeperkt|Onbeperkt|Onbeperkt|
|Maximale door Voer per logische partitie van een container|10.000 RU/s|10.000 RU/s|10.000 RU/s|10.000 RU/s|
|Maximale opslag (data + index) per logische partitie van een container|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van Standard (hand matig) in een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van een standaard doorvoer (hand matig) in een Azure Cosmos-data base](how-to-provision-database-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor automatisch schalen in een Azure Cosmos-data base of-container](how-to-provision-autoscale-throughput.md).
