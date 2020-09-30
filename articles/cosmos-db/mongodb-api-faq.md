---
title: Veelgestelde vragen over de API van de Azure Cosmos DB voor MongoDB
description: Krijg antwoorden op veelgestelde vragen over de API van de Azure Cosmos DB voor MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 05b0ff7d4f56a61c5c91848044a30cb9bf1d0f46
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565306"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Veelgestelde vragen over de API van de Azure Cosmos DB voor MongoDB

De API van het Azure Cosmos DB voor MongoDB is een interprotocol Compatibility Layer waarmee toepassingen eenvoudig en transparant kunnen communiceren met de systeem eigen Azure Cosmos-data base-engine door gebruik te maken van bestaande Sdk's en stuur Programma's die door de community worden ondersteund voor MongoDB. Ontwikkel aars kunnen nu bestaande MongoDB-toolchains en-vaardig heden gebruiken om toepassingen te ontwikkelen die gebruikmaken van Azure Cosmos DB. Ontwikkel aars profiteren van de unieke mogelijkheden van Azure Cosmos DB, waaronder wereld wijde distributie met multi-regio write-replicatie, automatische indexering, onderhoud van back-ups, financieel ondersteunde service level agreements (Sla's), enzovoort.

## <a name="how-do-i-connect-to-my-database"></a>Hoe kan ik verbinding maken met mijn data base?

De snelste manier om verbinding te maken met een Cosmos-data base met de API van Azure Cosmos DB voor MongoDB, is naar de [Azure Portal](https://portal.azure.com). Ga naar uw account en klik vervolgens in het navigatie menu links op **Quick Start**. Quick start is de beste manier om code fragmenten op te halen om verbinding te maken met uw data base.

Azure Cosmos DB dwingt strikte beveiligings vereisten en-standaarden af. Voor Azure Cosmos DB accounts is verificatie en beveiligde communicatie via TLS vereist. Zorg er dus voor dat u TLSv 1.2 gebruikt.

Zie [verbinding maken met uw Cosmos-data base met de API van Azure Cosmos DB voor MongoDb](connect-mongodb-account.md)voor meer informatie.

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Fout codes tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB?

Naast de algemene MongoDB-fout codes heeft de API van de Azure Cosmos DB voor MongoDB een eigen specifieke fout code:

| Fout               | Code  | Beschrijving  | Oplossing  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Het totale aantal verbruikte aanvraag eenheden is hoger dan de ingerichte aanvraag-eenheids snelheid voor de container en is beperkt. | U kunt de door Voer die is toegewezen aan een container of een set containers van de Azure Portal schalen of het opnieuw proberen. |
| ExceededMemoryLimit | 16501 | Als multi tenant service heeft de bewerking de geheugen toewijzing van de client overschreden. | Verklein het bereik van de bewerking via meer beperkende query criteria of neem contact op met de ondersteuning van de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br> Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>Ondersteunde Stuur Programma's

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Is het Simba-stuur programma voor MongoDB ondersteund voor gebruik met de API van Azure Cosmos DB voor MongoDB?

Ja, u kunt het Simba Mongo ODBC-stuur programma gebruiken met de API van Azure Cosmos DB voor MongoDB

## <a name="next-steps"></a>Volgende stappen

* [Een .NET-Web-app maken met behulp van de API van Azure Cosmos DB voor MongoDB](create-mongodb-dotnet.md)
* [Een console-app maken met Java en de MongoDB-API in Azure Cosmos DB](create-mongodb-java.md)
