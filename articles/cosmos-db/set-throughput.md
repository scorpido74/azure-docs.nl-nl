---
title: Doorvoer inrichten op Azure Cosmos-containers en databases
description: Meer informatie over het instellen van ingerichte door Voer voor uw Azure Cosmos-containers en-data bases.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 31ad7a9d1108adc9071812454419252a813cb93e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364455"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Doorvoer voor containers en databases inrichten

Een Azure Cosmos-database is een eenheid van het beheer voor een set met containers. Een database bestaat uit een verzameling van schema-agnostische containers. Een Azure Cosmos-container is de schaalbaarheid voor doorvoer en opslag. Een container is horizontaal gepartitioneerd in een set van machines binnen een Azure-regio en is verdeeld over alle Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-account.

Met Azure Cosmos DB kunt u de door Voer op twee nauw keurigheid inrichten:
 
- Azure Cosmos-containers
- Azure Cosmos-databases

## <a name="set-throughput-on-a-container"></a>Door Voer voor een container instellen  

De door Voer die is ingericht voor een Azure Cosmos-container is exclusief gereserveerd voor die container. De container, ontvangt de ingerichte doorvoer voortdurend. De ingerichte doorvoer voor een container wordt met financiële ondersteund door Sla's. Zie de [door Voer inrichten op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie over het configureren van door Voer voor een container.

Het instellen van een ingerichte door Voer voor een container is de meest gebruikte optie. U kunt de door Voer voor een container elastisch schalen door een wille keurige hoeveelheid door Voer in te stellen met behulp van [aanvraag eenheden (RUs)](request-units.md). 

De door Voer die is ingericht voor een container wordt gelijkmatig verdeeld over de fysieke partities en er wordt ervan uitgegaan dat er een goede partitie sleutel is waarmee de logische partities gelijkmatig over de fysieke partities worden gedistribueerd. de door Voer wordt ook gelijkmatig verdeeld over alle de logische partities van de container. U kunt de door Voer voor logische partities niet selectief opgeven. Omdat een of meer logische partities van een container worden gehost door een fysieke partitie, behoren de fysieke partities uitsluitend tot de container en ondersteunen ze de door Voer ingericht op de container. 

Als de werk belasting die wordt uitgevoerd op een logische partitie, groter is dan de door Voer die is toegewezen aan de logische partitie, worden de verwerkings frequenties beperkt. Wanneer de snelheids beperking optreedt, kunt u de ingerichte door Voer voor de gehele container verhogen of de bewerking opnieuw uitvoeren. Zie [logische partities](partition-data.md)voor meer informatie over partitioneren.

We raden u aan om door voer te configureren bij de container granulatie wanneer u gegarandeerde prestaties voor de container wilt.

In de volgende afbeelding ziet u hoe een fysieke partitie als host fungeert voor een of meer logische partities van een container:

![Fysieke partitie](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Door Voer instellen voor een Data Base

Wanneer u de door Voer inricht in een Azure Cosmos-data base, wordt de door Voer gedeeld via alle containers (shared data base-containers genoemd) in de-data base. Een uitzonde ring hierop is als u een ingerichte door Voer hebt opgegeven voor specifieke containers in de data base. Het delen van de ingerichte door Voer op database niveau tussen de containers is vergelijkbaar met het hosten van een Data Base op een cluster machines. Omdat alle containers in een Data Base de resources delen die beschikbaar zijn op een computer, kunt u natuurlijk geen voorspel bare prestaties op een specifieke container krijgen. Zie [ingerichte door Voer configureren voor een Azure Cosmos-data base](how-to-provision-database-throughput.md)voor meer informatie over het configureren van een ingerichte door Voer voor een Data Base.

Door de door Voer voor een Azure Cosmos-data base in te stellen, zorgt u ervoor dat u de ingerichte door Voer voor de Data Base op elk moment ontvangt. Omdat alle containers in de data base de ingerichte door voer hebben gedeeld, biedt Azure Cosmos DB geen voorspel bare doorvoer garanties voor een bepaalde container in die data base. Het gedeelte van de doorvoer die een specifieke container kan ontvangen, is afhankelijk van:

* Het aantal containers.
* De keuze van partitie sleutels voor verschillende containers.
* De distributie van de werkbelasting over verschillende logische partities van de containers. 

We raden u aan de door Voer voor een Data Base te configureren als u de door voer wilt delen in meerdere containers, maar niet de door voer wilt toewijzen aan een bepaalde container. 

De volgende voor beelden laten zien waar het voor keur is om de door Voer in te richten op het niveau van de Data Base:

* Het delen van de ingerichte door Voer van een data base in een set containers is handig voor een multi tenant-toepassing. Elke gebruiker kan worden vertegenwoordigd door een afzonderlijke Azure-Cosmos-container.

* Het delen van de ingerichte door Voer van een data base in een set met containers is handig wanneer u een NoSQL-data base, zoals MongoDB of Cassandra, migreert die wordt gehost op een cluster van virtuele machines of van on-premises fysieke servers naar Azure Cosmos DB. Denk na over de ingerichte door Voer die op uw Azure Cosmos-data base is geconfigureerd als een logische equivalent, maar rendabeler en elastisch, tot die van de reken capaciteit van uw MongoDB-of Cassandra-cluster.  

Alle containers die in een Data Base zijn gemaakt met een ingerichte door Voer, moeten worden gemaakt met een [partitie sleutel](partition-data.md). Op een bepaald moment wordt de door Voer toegewezen aan een container in een Data Base verdeeld over alle logische partities van die container. Wanneer u containers hebt waarin de ingerichte door Voer is geconfigureerd voor een Data Base, kunt u de door Voer niet selectief Toep assen op een specifieke container of een logische partitie. 

Als de werk belasting op een logische partitie meer gebruikt dan de door Voer die is toegewezen aan een specifieke logische partitie, zijn uw bewerkingen een beperkt aantal. Wanneer de snelheids beperking optreedt, kunt u de door Voer voor de gehele data base verg Roten of de bewerkingen opnieuw proberen. Zie [logische partities](partition-data.md)voor meer informatie over partitioneren.

Containers in een gedeelde doorvoer database delen de door Voer (RU/s) die zijn toegewezen aan die data base. U kunt Maxi maal vier containers met mini maal 400 RU/s op de data base hebben. Voor elke nieuwe container na de eerste vier is een extra mini maal 100 RU/s vereist. Als u bijvoorbeeld een Data Base met gedeelde door Voer met acht containers hebt, is de mini maal RU/s op de data base 800 RU/s.

> [!NOTE]
> In februari 2020 hebben we een wijziging geïntroduceerd waarmee u Maxi maal 25 containers in een gedeelde doorvoer database kunt hebben, waardoor het delen van de door Voer via de containers beter wordt. Na de eerste 25 containers kunt u meer containers toevoegen aan de Data Base als ze zijn [ingericht met een specifieke door Voer](#set-throughput-on-a-database-and-a-container), die los is van de gedeelde door Voer van de data base.<br>
Als uw Azure Cosmos DB-account al een Data Base met gedeelde door Voer bevat met > = 25 containers, zijn het account en alle andere accounts in hetzelfde Azure-abonnement uitgesloten van deze wijziging. [Neem contact op met product ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u feedback of vragen hebt. 

Als uw workloads het verwijderen en opnieuw maken van alle verzamelingen in een Data Base vereisen, is het raadzaam om de lege data base weg te halen en opnieuw een nieuwe Data Base te maken voordat u de verzameling maakt. In de volgende afbeelding ziet u hoe een fysieke partitie een of meer logische partities kan hosten die deel uitmaken van verschillende containers in een Data Base:

![Fysieke partitie](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Door Voer instellen voor een Data Base en een container

U kunt de twee modellen combi neren. De door Voer voor de data base en de container inrichten is toegestaan. Het volgende voorbeeld laat zien hoe om te worden ingericht met doorvoer op een Azure Cosmos-database en een container:

* U kunt een Azure Cosmos-data base met de naam *Z* maken met de ingerichte door Voer van *"K"* RUs. 
* Maak vervolgens vijf containers met de naam *A*, *B*, *C*, *D*en *E* in de-data base. Zorg ervoor dat u bij het maken van container B een **specifieke door Voer inrichten voor deze container** optie inschakelt en expliciet *' P '* RUs van ingerichte door Voer voor deze container configureert. Houd er rekening mee dat u een gedeelde en toegewezen door Voer alleen kunt configureren bij het maken van de data base en container. 

   ![De door Voer op container niveau instellen](./media/set-throughput/coll-level-throughput.png)

* De *"K"* RUs-door Voer wordt gedeeld in de vier containers *A*, *C*, *D*en *E*. De exacte hoeveelheid door Voer die beschikbaar is voor *A*, *C*, *D*of *E* , varieert. Er zijn geen service overeenkomsten voor de door Voer van elke afzonderlijke container.
* De container met de naam *B* is gegarandeerd dat de *"P"* RUs-door Voer altijd wordt opgehaald. Er wordt een back-up gemaakt van service overeenkomsten.

> [!NOTE]
> Een container met ingerichte door Voer kan niet worden geconverteerd naar een gedeelde database container. Een gedeelde database container kan niet worden geconverteerd naar een specifieke door voer.

## <a name="update-throughput-on-a-database-or-a-container"></a>De door Voer van een Data Base of container bijwerken

Nadat u een Azure Cosmos-container of een-Data Base hebt gemaakt, kunt u de ingerichte door Voer bijwerken. Er is geen limiet voor de Maxi maal ingerichte door Voer die u kunt configureren voor de data base of de container. De minimale ingerichte door Voer is afhankelijk van de volgende factoren: 

* De maximale gegevens grootte die u ooit opslaat in de container
* De maximale door Voer die u ooit op de container hebt ingericht
* Het maximum aantal Azure Cosmos-containers dat u ooit in een Data Base hebt gemaakt met een gedeelde door voer. 

U kunt de minimale door Voer van een container of een Data Base via een programma ophalen met behulp van de Sdk's of de waarde in de Azure Portal weer geven. Wanneer u de .NET SDK gebruikt, kunt u met de methode [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) de ingerichte doorvoer waarde schalen. Wanneer u de Java-SDK gebruikt, kunt u met de methode [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) de ingerichte doorvoer waarde schalen. 

Wanneer u de .NET SDK gebruikt, kunt u met de methode [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) de minimale door Voer van een container of een Data Base ophalen. 

U kunt de ingerichte door Voer van een container of een Data Base op elk gewenst moment schalen. Wanneer er een schaal bewerking wordt uitgevoerd om de door voer te verg Roten, kan het langer duren vanwege de systeem taken om de vereiste resources in te richten. U kunt de status van de schaal bewerking controleren in Azure Portal of programmatisch met behulp van de Sdk's. Wanneer u de .NET SDK gebruikt, kunt u de status van de schaal bewerking ophalen met behulp van de `DocumentClient.ReadOfferAsync` methode.

## <a name="comparison-of-models"></a>Vergelijking van modellen

|**Bepaalde**  |**Door Voer ingericht voor een Data Base**  |**Door Voer ingericht voor een container**|
|---------|---------|---------|
|Minimale ru 's |400 (na de eerste vier containers is voor elke extra container mini maal 100 RUs per seconde vereist.) |400|
|Minimale ru's per container|100|400|
|Maximum aantal ru 's|Onbeperkt, op de data base.|Onbeperkt, op de container.|
|RUs toegewezen of beschikbaar voor een specifieke container|Er zijn geen garanties. RUs toegewezen aan een bepaalde container is afhankelijk van de eigenschappen. Eigenschappen kunnen de keuze zijn van partitie sleutels van containers die de door Voer, de distributie van de werk belasting en het aantal containers delen. |Alle ru's die zijn geconfigureerd op de container zijn exclusief zijn gereserveerd voor de container.|
|Maximale opslag voor een container|Limited.|Limited.|
|Maximale doorvoer per logische partitie van een container|10K ru 's|10K ru 's|
|Maximale opslag (gegevens en index) per logische partitie van een container|20 GB|20 GB|

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).

