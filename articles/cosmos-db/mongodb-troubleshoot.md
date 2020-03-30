---
title: Veelvoorkomende fouten in de API van Azure Cosmos DB voor Mongo DB oplossen
description: In dit document worden de manieren besproken om veelvoorkomende problemen op te lossen die zich voordoen in de API van Azure Cosmos DB voor MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941846"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Veelvoorkomende problemen in de API van Azure Cosmos DB voor MongoDB oplossen

Azure Cosmos DB implementeert de draadprotocollen van veelvoorkomende NoSQL-databases, waaronder MongoDB. Dankzij de implementatie van het draadprotocol u transparant communiceren met Azure Cosmos DB met behulp van de bestaande client-SDK's, stuurprogramma's en hulpprogramma's die werken met NoSQL-databases. Azure Cosmos DB gebruikt geen broncode van de databases voor het verstrekken van wire-compatibele API's voor een van de NoSQL-databases. Elk MongoDB-clientstuurprogramma dat de versies van het draadprotocol begrijpt, kan verbinding maken met Azure Cosmos DB.

Hoewel de API van Azure Cosmos DB voor MongoDB compatibel is met de 3.2-versie van het draadprotocol van de MongoDB (de queryoperators en -functies die in versie 3.4 zijn toegevoegd, zijn momenteel beschikbaar als voorbeeld), zijn er enkele aangepaste foutcodes die overeenkomen met Azure Cosmos DB specifieke fouten. In dit artikel worden verschillende fouten, foutcodes en de stappen uitgelegd om deze fouten op te lossen.

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Fout               | Code  | Beschrijving  | Oplossing  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Het totale aantal verbruikte aanvraageenheden is meer dan het ingerichte aanvraag-eenheidstarief voor de verzameling en is beperkt. | Overweeg de doorvoer die is toegewezen aan een container of een set containers te schalen vanuit de Azure-portal of u de bewerking opnieuw proberen. |
| ExceededMemoryLimit | 16501 | Als een multi-tenant service, de operatie is gegaan over het geheugen van de client toewijzing. | Verminder het bereik van de bewerking door meer beperkende querycriteria of neem contact op met ondersteuning vanuit de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Het indexpad dat overeenkomt met het opgegeven order-by-item is uitgesloten / De volgorde per query heeft geen overeenkomstige samengestelde index waarvan het kan worden weergegeven. | 2 | De query vraagt een sortering aan op een veld dat niet is geïndexeerd. | Maak een overeenkomende index (of samengestelde index) voor de sorteerquery die wordt geprobeerd. |
| Problemen met wire-versies van MongoDB | - | De oudere versies van MongoDB-stuurprogramma's kunnen de naam van het Azure Cosmos-account niet detecteren in de verbindingstekenreeksen. | *AppName=@-accounttoevoegenNaam**accountName** @ * aan het einde van de API voor MongoDB-verbinding van uw Cosmos DB, waarbij ***accountName*** uw Cosmos DB-accountnaam is. |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.

