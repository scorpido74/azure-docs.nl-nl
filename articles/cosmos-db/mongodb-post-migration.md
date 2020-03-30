---
title: Optimalisatiestappen na migratie met Azure Cosmos DB's API voor MongoDB
description: Dit document biedt de optimalisatietechnieken na migratie van MongoDB tot Azure Cosmos DB's APi voor Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063621"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimalisatiestappen na migratie bij het gebruik van de API van Azure Cosmos DB voor MongoDB

Nadat u de gegevens die zijn opgeslagen in de MongoDB-database hebt gemigreerd naar de API van Azure Cosmos DB voor MongoDB, u verbinding maken met Azure Cosmos DB en de gegevens beheren. Deze handleiding bevat de stappen die u moet overwegen na de migratie. Zie de [API voor De API van Migreren naar Azure Cosmos DB voor MongoDB](../dms/tutorial-mongodb-cosmos-db.md) voor de migratiestappen.

In deze handleiding leert u het volgende:

- [Uw toepassing verbinden](#connect-your-application)
- [Het indexeringsbeleid optimaliseren](#optimize-the-indexing-policy)
- [Globale distributie configureren voor de API van Azure Cosmos DB voor MongoDB](#globally-distribute-your-data)
- [Consistentieniveau instellen](#set-consistency-level)

> [!NOTE]
> De enige verplichte stap na migratie op toepassingsniveau is het wijzigen van de verbindingstekenreeks in uw toepassing om naar uw nieuwe Azure Cosmos DB-account te wijzen. Alle andere migratiestappen zijn aanbevolen optimalisaties.
>

## <a name="connect-your-application"></a>Uw toepassing verbinden

1. In een nieuw venster aanmelden bij de [Azure-portal](https://www.portal.azure.com/)
2. Open in [het](https://www.portal.azure.com/)linkerdeelvenster in het linkerdeelvenster het menu Alle **bronnen** en zoek het Azure Cosmos DB-account waarnaar u uw gegevens hebt gemigreerd.
3. Open het **verbindingssnaarblad.** Het rechterdeelvenster bevat alle informatie die u nodig hebt om verbinding te maken met uw account.
4. Gebruik de verbindingsgegevens in de configuratie van uw toepassing (of andere relevante plaatsen) om de API voor MongoDB-verbinding van Azure Cosmos DB in uw app weer te geven.
![Verbindingstekenreeks](./media/mongodb-post-migration/connection-string.png)

Zie de [connect a MongoDB-toepassing op azure cosmos DB-pagina](connect-mongodb-account.md) voor meer informatie.

## <a name="optimize-the-indexing-policy"></a>Het indexeringsbeleid optimaliseren

Alle gegevensvelden worden standaard automatisch geïndexeerd tijdens de migratie van gegevens naar Azure Cosmos DB. In veel gevallen is dit standaardindexeringsbeleid aanvaardbaar. In het algemeen optimaliseert het verwijderen van indexen schrijfaanvragen en optimaliseert het standaardindexeringsbeleid (d.w.z. automatische indexering) leesaanvragen.

Zie [Gegevensindexering in de API van Azure Cosmos DB voor MongoDB](mongodb-indexing.md) en de [indexering in Azure Cosmos DB-artikelen](index-overview.md) voor meer informatie over indexering.

## <a name="globally-distribute-your-data"></a>Uw gegevens wereldwijd distribueren

Azure Cosmos DB is wereldwijd beschikbaar in alle [Azure-regio's](https://azure.microsoft.com/regions/#services). Nadat u het standaardconsistentieniveau voor uw Azure Cosmos DB-account hebt geselecteerd, u een of meer Azure-regio's koppelen (afhankelijk van uw globale distributiebehoeften). Voor hoge beschikbaarheid en bedrijfscontinuïteit raden we altijd aan om in ten minste 2 regio's te draaien. U de tips bekijken voor [het optimaliseren van de kosten van implementaties met meerdere regio's in Azure Cosmos DB.](optimize-cost-regions.md)

Zie Gegevens wereldwijd distribueren op de API van [Azure Cosmos DB voor MongoDB](tutorial-global-distribution-mongodb.md)om uw gegevens wereldwijd te distribueren.

## <a name="set-consistency-level"></a>Consistentieniveau instellen

Azure Cosmos DB biedt 5 goed gedefinieerde [consistentieniveaus.](consistency-levels.md) Lees [Consistentieniveaus en Azure Cosmos DB-API's](consistency-levels-across-apis.md)voor meer informatie over de toewijzing tussen MongoDB en Azure Cosmos DB- Het standaardconsistentieniveau is het consistentieniveau van de sessie. Het wijzigen van het consistentieniveau is optioneel en u het optimaliseren voor uw app. Ga als een wijziging smeerwerkop het niveau van de consistentie wijzigen met Azure-portal:

1. Ga naar het **standaardpagina van consistentie** onder Instellingen.
2. Selecteer uw [consistentieniveau](consistency-levels.md)

De meeste gebruikers laten hun consistentieniveau achter bij de standaardinstelling voor sessieconsistentie. Er zijn echter [beschikbaarheids- en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md).

## <a name="next-steps"></a>Volgende stappen

* [Een MongoDB-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md)
* [Verbinding maken met Azure Cosmos DB-account met Studio 3T](mongodb-mongochef.md)
* [Hoe leesbewerking wereldwijd te distribueren met behulp van Azure Cosmos DB's API voor MongoDB](mongodb-readpreference.md)
* [Gegevens automatisch laten verlopen met de Azure Cosmos DB-API voor MongoDB](mongodb-time-to-live.md)
* [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)
* [Indexering in Azure Cosmos DB](index-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)