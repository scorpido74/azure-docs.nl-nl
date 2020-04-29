---
title: Verbinding maken met Azure Cosmos DB met behulp van kompas
description: Leer hoe u MongoDB-kompas kunt gebruiken om gegevens op te slaan en te beheren in Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: c683ec0c4b3a536b0627a7c1c8abf28ee4f83663
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80757027"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-kompas gebruiken om verbinding te maken met de API van Azure Cosmos DB voor MongoDB

In deze zelf studie wordt gedemonstreerd hoe u [MongoDb kompas](https://www.mongodb.com/products/compass) kunt gebruiken bij het opslaan en/of beheren van gegevens in Cosmos db. We gebruiken de Azure Cosmos DB-API voor MongoDB voor deze procedure. Voor degenen die niet bekend zijn, is kompas een GUI voor MongoDB. Het wordt meestal gebruikt voor het visualiseren van uw gegevens, het uitvoeren van ad-hoc query's en het beheren van uw gegevens.

Cosmos DB is de wereld wijd gedistribueerde multi-model database service van micro soft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Cosmos DB.

## <a name="pre-requisites"></a>Vereisten

Als u verbinding wilt maken met uw Cosmos DB-account met behulp van MongoDB-kompas, moet u het volgende doen:

* Een [kompas](https://www.mongodb.com/download-center/compass?jmp=hero) downloaden en installeren
* Informatie over uw Cosmos DB [Connection String](connect-mongodb-account.md)

> [!NOTE]
> Momenteel wordt de API van Azure Cosmos DB voor MongoDB-Server versie 3,2 ondersteund met MongoDB-kompas.

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Verbinding maken met de API van Cosmos DB voor MongoDB

Als u uw Cosmos DB-account wilt verbinden met een kompas, kunt u de onderstaande stappen volgen:

1. Haal de verbindings gegevens op voor uw Cosmos-account dat is geconfigureerd met de API-MongoDB van Azure Cosmos DB met behulp van de instructies [hier](connect-mongodb-account.md).

    ![Scherm afbeelding van de Blade connection string](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klik op de knop met de tekst **kopiëren naar het klem bord** naast uw **primaire/secundaire Connection String** in Cosmos db. Als u op deze knop klikt, wordt uw hele connection string naar het klem bord gekopieerd.

    ![Scherm afbeelding van de knop kopiëren naar klem bord](./media/mongodb-compass/mongodb-connection-copy.png)

3. Open kompas op het bureau blad of de computer en klik op **verbinding maken** en vervolgens **verbinding maken met...**.

4. Met kompas wordt automatisch een connection string in het klem bord gedetecteerd en wordt u gevraagd of u wilt gebruiken om verbinding te maken. Klik op **Ja** , zoals wordt weer gegeven in de onderstaande scherm afbeelding.

    ![Scherm afbeelding van de kompas prompt om verbinding te maken](./media/mongodb-compass/mongodb-compass-detect.png)

5. Wanneer u op **Ja** klikt in de bovenstaande stap, worden uw details van de Connection String automatisch ingevuld. Verwijder de waarde die automatisch is ingevuld in het veld **naam van replicaset** om ervoor te zorgen dat deze leeg blijft.

    ![Scherm afbeelding van de kompas prompt om verbinding te maken](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klik onder aan de pagina op **verbinding maken** . Uw Cosmos DB-account en data bases moeten nu worden weer gegeven in MongoDB-kompas.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
