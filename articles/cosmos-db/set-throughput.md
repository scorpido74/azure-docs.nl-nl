---
title: Doorvoer voorziening op Azure Cosmos-containers en -databases
description: Meer informatie over het instellen van ingerichte doorvoer voor uw Azure Cosmos-containers en -databases.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251971"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Doorvoer voor containers en databases inrichten

Een Azure Cosmos-database is een beheereenheid voor een set containers. Een database bestaat uit een set schema-agnostische containers. Een Azure Cosmos-container is de eenheid van schaalbaarheid voor zowel doorvoer als opslag. Een container is horizontaal verdeeld over een set machines binnen een Azure-gebied en wordt verdeeld over alle Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-account.

Met Azure Cosmos DB u doorvoer inrichten op twee granulariteiten:
 
- Azure Cosmos-containers
- Azure Cosmos-databases

## <a name="set-throughput-on-a-container"></a>Doorvoer instellen op een container  

De doorvoer die is ingericht op een Azure Cosmos-container is uitsluitend gereserveerd voor die container. De container ontvangt de ingerichte doorvoer de hele tijd. De ingerichte doorvoer op een container wordt financieel ondersteund door SLA's. Zie [Doorvoer voorziening op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie over het configureren van doorvoer op een container.

Het instellen van ingerichte doorvoer op een container is de meest gebruikte optie. U de doorvoer voor een container elastisch schalen door elke hoeveelheid doorvoer in te richten met behulp van [Aanvraageenheden (RU's).](request-units.md) 

De doorvoer die voor een container is ingericht, wordt gelijkmatig verdeeld over de fysieke partities en uitgaande van een goede partitiesleutel die de logische partities gelijkmatig over de fysieke partities verdeelt, wordt de doorvoer ook gelijkmatig verdeeld over alle de logische partities van de container. U de doorvoer voor logische partities niet selectief opgeven. Omdat een of meer logische partities van een container worden gehost door een fysieke partitie, behoren de fysieke partities uitsluitend tot de container en ondersteunen ze de overslag die op de container is ingericht. 

Als de werkbelasting die op een logische partitie wordt uitgevoerd, meer verbruikt dan de doorvoer die aan die logische partitie is toegewezen, worden uw bewerkingen beperkt. Wanneer de snelheid beperkt optreedt, u de ingerichte doorvoer voor de hele container verhogen of de bewerkingen opnieuw proberen. Zie [Logische partities voor](partition-data.md)meer informatie over partitionering .

We raden u aan de doorvoer bij de granulariteit van de container te configureren wanneer u gegarandeerde prestaties voor de container wilt.

In de volgende afbeelding ziet u hoe een fysieke partitie een of meer logische partities van een container host:

![Fysieke partitie](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Doorvoer instellen in een database

Wanneer u doorvoer indient op een Azure Cosmos-database, wordt de doorvoer gedeeld over alle containers (gedeelde databasecontainers genoemd) in de database. Een uitzondering is als u een ingerichte doorvoer hebt opgegeven voor specifieke containers in de database. Het delen van de database-niveau ingerichte doorvoer tussen de containers is analoog aan het hosten van een database op een cluster van machines. Omdat alle containers in een database de beschikbare resources op een machine delen, krijgt u natuurlijk geen voorspelbare prestaties op een specifieke container. Zie Ingerichte doorvoer configureren voor een [Azure Cosmos-database](how-to-provision-database-throughput.md)voor meer informatie over het configureren van de ingerichte doorvoer in een database.

Doorvoer instellen op een Azure Cosmos-database garandeert dat u de ingerichte doorvoer voor die database altijd ontvangt. Omdat alle containers in de database de ingerichte doorvoer delen, biedt Azure Cosmos DB geen voorspelbare doorvoergaranties voor een bepaalde container in die database. Het gedeelte van de doorvoer dat een specifieke container kan ontvangen, is afhankelijk van:

* Het aantal containers.
* De keuze van partitiesleutels voor verschillende containers.
* De verdeling van de werklast over verschillende logische partities van de containers. 

We raden u aan de doorvoer in een database te configureren wanneer u de doorvoer over meerdere containers wilt delen, maar de doorvoer niet wilt wijden aan een bepaalde container. 

De volgende voorbeelden laten zien waar het de voorkeur heeft aan het inrichten van doorvoer op databaseniveau:

* Het delen van de ingerichte doorvoer van een database over een set containers is handig voor een multitenant-toepassing. Elke gebruiker kan worden vertegenwoordigd door een afzonderlijke Azure Cosmos-container.

* Het delen van de ingerichte doorvoer van een database over een set containers is handig wanneer u een NoSQL-database migreert, zoals MongoDB of Cassandra, gehost op een cluster van VM's of van on-premises fysieke servers naar Azure Cosmos DB. Denk aan de ingerichte doorvoer die is geconfigureerd op uw Azure Cosmos-database als een logisch equivalent, maar meer kosteneffectief en elastisch, met die van de rekencapaciteit van uw MongoDB- of Cassandra-cluster.  

Alle containers die zijn gemaakt in een database met ingerichte doorvoer, moeten worden gemaakt met een [partitiesleutel.](partition-data.md) Op een bepaald moment wordt de doorvoer toegewezen aan een container in een database verdeeld over alle logische partities van die container. Wanneer u containers hebt die de ingerichte doorvoer in een database delen, u de doorvoer niet selectief toepassen op een specifieke container of een logische partitie. 

Als de werkbelasting op een logische partitie meer verbruikt dan de doorvoer die is toegewezen aan een specifieke logische partitie, zijn uw bewerkingen beperkt. Wanneer de snelheid beperkt optreedt, u de doorvoer voor de hele database verhogen of de bewerkingen opnieuw proberen. Zie [Logische partities voor](partition-data.md)meer informatie over partitionering .

Containers in een gedeelde doorvoerdatabase delen de doorvoer (RU/s) die aan die database is toegewezen. U maximaal vier containers met een minimum van 400 RU/s in de database hebben. Elke nieuwe container na de eerste vier vereist een extra 100 RU /s minimum. Als u bijvoorbeeld een gedeelde doorvoerdatabase met acht containers hebt, zijn de minimale RU/s in de database 800 RU/s.

> [!NOTE]
> In februari 2020 hebben we een wijziging doorgevoerd waarmee u maximaal 25 containers in een gedeelde doorvoerdatabase hebben, waardoor doorvoerdeling over de containers beter mogelijk is. Na de eerste 25 containers u alleen meer containers aan de database toevoegen als ze zijn [ingericht met speciale doorvoer](#set-throughput-on-a-database-and-a-container), die los staat van de gedeelde doorvoer van de database.<br>
Als uw Azure Cosmos DB-account al een gedeelde doorvoerdatabase bevat met >=25-containers, zijn het account en alle andere accounts in hetzelfde Azure-abonnement vrijgesteld van deze wijziging. Neem [contact op met de productondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u feedback of vragen hebt. 

Als uw workloads betrekking hebben op het verwijderen en opnieuw maken van alle verzamelingen in een database, wordt aanbevolen de lege database te laten vallen en een nieuwe database opnieuw te maken voordat u de verzameling maakt. In de volgende afbeelding ziet u hoe een fysieke partitie een of meer logische partities kan hosten die tot verschillende containers in een database behoren:

![Fysieke partitie](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Doorvoer instellen op een database en een container

U de twee modellen combineren. Het inrichten van doorvoer op zowel de database als de container is toegestaan. In het volgende voorbeeld ziet u hoe u doorvoer indient op een Azure Cosmos-database en een container:

* U een Azure Cosmos-database met de naam *Z* maken met een ingerichte doorvoer van *'K'-RU's.* 
* Maak vervolgens vijf containers met de naam *A,* *B,* *C,* *D*en *E* in de database. Zorg er bij het maken van container B voor dat u **de specifieke doorvoer voorziening voor deze containeroptie** inschakelt en configureert u *'P'-RU's* van de ingerichte doorvoer op deze container expliciet. Houd er rekening mee dat u gedeelde en toegewezen doorvoer alleen configureren bij het maken van de database en container. 

   ![De doorvoer op containerniveau instellen](./media/set-throughput/coll-level-throughput.png)

* De *"K"* RUs-doorvoer wordt gedeeld over de vier containers *A*, *C*, *D*en *E*. De exacte hoeveelheid doorvoer die beschikbaar is voor *A,* *C,* *D*of *E* varieert. Er zijn geen SLA's voor de doorvoer van elke afzonderlijke container.
* De container met de naam *B* krijgt gegarandeerd de *"P"* RUs doorvoer de hele tijd. Het wordt ondersteund door SLA's.

> [!NOTE]
> Een container met ingerichte doorvoer kan niet worden geconverteerd naar gedeelde databasecontainer. Omgekeerd kan een gedeelde databasecontainer niet worden geconverteerd naar een speciale doorvoer.

## <a name="update-throughput-on-a-database-or-a-container"></a>Doorvoer bijwerken in een database of container

Nadat u een Azure Cosmos-container of een database hebt gemaakt, u de ingerichte doorvoer bijwerken. Er is geen limiet aan de maximaal ingerichte doorvoer die u configureren in de database of de container. De [minimale ingerichte doorvoer](concepts-limits.md#storage-and-throughput) is afhankelijk van de volgende factoren: 

* De maximale gegevensgrootte die u ooit in de container opslaat
* De maximale doorvoer die u ooit op de container indient
* Het huidige aantal Azure Cosmos-containers dat u in een database met gedeelde doorvoer hebt. 

U de minimale doorvoer van een container of een database programmatisch ophalen met behulp van de SDK's of de waarde in de Azure-portal weergeven. Wanneer u de .NET SDK gebruikt, u met de methode [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) de ingerichte doorvoerwaarde schalen. Bij het gebruik van de Java SDK u met de methode [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) de ingerichte doorvoerwaarde schalen. 

Bij het gebruik van de .NET SDK u met de methode [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) de minimale doorvoer van een container of database ophalen. 

U de ingerichte doorvoer van een container of database op elk gewenst moment schalen. Wanneer een schaalbewerking wordt uitgevoerd om de doorvoer te verhogen, kan het langer duren voordat de systeemtaken de vereiste resources inrichten. U de status van de schaalbewerking in Azure-portal of programmatisch controleren met behulp van de SDK's. Wanneer u de .Net SDK gebruikt, u de `DocumentClient.ReadOfferAsync` status van de schaalbewerking krijgen met behulp van de methode.

## <a name="comparison-of-models"></a>Vergelijking van modellen

|**Parameter**  |**Doorvoer ingericht in een database**  |**Doorvoer op een container**|
|---------|---------|---------|
|Minimum rus's |400 (Na de eerste vier containers heeft elke extra container minimaal 100 RALLY's per seconde nodig.) |400|
|Minimum-R's per container|100|400|
|Maximale RUS|Onbeperkt, in de database.|Onbeperkt, op de container.|
|RU's toegewezen of beschikbaar voor een specifieke container|Geen garanties. DE's die aan een bepaalde container zijn toegewezen, zijn afhankelijk van de eigenschappen. Eigenschappen kunnen de keuze zijn van partitiesleutels van containers die de doorvoer, de verdeling van de werkbelasting en het aantal containers delen. |Alle OP DE's geconfigureerde R's zijn exclusief voor de container gereserveerd.|
|Maximale opslag voor een container|Onbeperkt.|Onbeperkt.|
|Maximale doorvoer per logische partitie van een container|10k na|10k na|
|Maximale opslag (gegevens + index) per logische partitie van een container|20 GB|20 GB|

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-database](how-to-provision-database-throughput.md).

