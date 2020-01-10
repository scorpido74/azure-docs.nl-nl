---
title: Veelvoorkomende fouten in de API van Azure Cosmos DB voor Mongo DB oplossen
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die zijn opgetreden in de API van Azure Cosmos DB voor MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 41d667f4e90114052a17c5707989634bbb5fc421
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719831"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen

Azure Cosmos DB implementeert de Wire-protocollen van algemene NoSQL-data bases, waaronder MongoDB. Als gevolg van de implementatie van het wire-protocol, kunt u op transparante wijze communiceren met Azure Cosmos DB door gebruik te maken van de bestaande client-Sdk's, stuur Programma's en hulpprogram ma's die werken met NoSQL-data bases. Azure Cosmos DB maakt geen gebruik van een bron code van de data bases voor het bieden van Wire-compatibele Api's voor een van de NoSQL-data bases. Elk MongoDB-client stuur programma dat inzicht heeft in de wire-protocol versies kan verbinding maken met Azure Cosmos DB.

Hoewel de API van Azure Cosmos DB voor MongoDB compatibel is met 3,2-versie van het wire-protocol van MongoDB (de query operators en functies die zijn toegevoegd in versie 3,4 zijn momenteel beschikbaar als preview), zijn er enkele aangepaste fout codes die overeenkomen met Azure Cosmos DB specifieke fouten. In dit artikel worden verschillende fouten, fout codes en de stappen beschreven om deze fouten op te lossen.

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Fout               | Coderen  | Beschrijving  | Oplossing  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Het totale aantal aanvraageenheden verbruikt meer is dan de frequentie van de ingerichte aanvraageenheid voor de verzameling en is beperkt. | Overweeg de door Voer die is toegewezen aan een container of een set containers te schalen vanuit de Azure Portal of probeer de bewerking opnieuw uit te voeren. |
| ExceededMemoryLimit | 16501 | Als een service met meerdere tenants, heeft de bewerking van de client geheugen toegewezen overschreden. | Verklein het bereik van de bewerking door meer beperkende querycriteria of neem contact op met ondersteuning van de [Azure-portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Het pad naar de index dat overeenkomt met het opgegeven order-by-item is uitgesloten/de order by-query heeft geen overeenkomende samengestelde index waaruit het kan worden geleverd. | 2 | De query vraagt een sortering op een veld dat niet is geïndexeerd. | Maak een overeenkomende index (of samengestelde index) voor de sorteer query die wordt geprobeerd. |
| Problemen met MongoDB-bedradings versies | - | De oudere versies van MongoDB-Stuur Programma's kunnen de naam van het Azure Cosmos-account in de verbindings reeksen niet detecteren. | Append *AppName = @**AccountName**@* aan het einde van de API van uw Cosmos DB voor MongoDb Connection String, waarbij ***AccountName*** uw Cosmos DB account naam is. |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.

