---
title: Verbinding maken met Azure Cosmos DB met Compass
description: Meer informatie over het gebruik van MongoDB Compass om gegevens op te slaan en te beheren in Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: c683ec0c4b3a536b0627a7c1c8abf28ee4f83663
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757027"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB Compass gebruiken om verbinding te maken met de API van Azure Cosmos DB voor MongoDB

Deze zelfstudie laat zien hoe [u MongoDB Compass kunt](https://www.mongodb.com/products/compass) gebruiken bij het opslaan en/of beheren van gegevens in Cosmos DB. We gebruiken de API van Azure Cosmos DB voor MongoDB voor deze doorloop. Voor degenen onder u onbekend, Compass is een GUI voor MongoDB. Het wordt vaak gebruikt om uw gegevens te visualiseren, ad-hocquery's uit te voeren, samen met het beheren van uw gegevens.

Cosmos DB is microsoft's wereldwijd gedistribueerde multi-model database service. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Cosmos DB.

## <a name="pre-requisites"></a>Vereisten

Als u verbinding wilt maken met uw Cosmos DB-account met MongoDB Compass, moet u het:

* [Compass](https://www.mongodb.com/download-center/compass?jmp=hero) downloaden en installeren
* Gegevens over de Cosmos [DB-verbindingstekenreeks](connect-mongodb-account.md)

> [!NOTE]
> Momenteel wordt de API van Azure Cosmos DB voor MongoDB Server versie 3.2 ondersteund met MongoDB Compass.

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Verbinding maken met de API van Cosmos DB voor MongoDB

Als u uw Cosmos DB-account wilt koppelen aan Compass, u de onderstaande stappen volgen:

1. Haal de verbindingsgegevens op voor uw Cosmos-account die is geconfigureerd met de API MongoDB van Azure Cosmos DB met behulp van de [instructies hier](connect-mongodb-account.md).

    ![Schermafbeelding van het verbindingstekenreeksblad](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klik op de knop met de tekst **Kopiëren naar klembord** naast de **tekenreeks Primaire/Secundaire verbinding** in Cosmos DB. Als u op deze knop klikt, wordt uw volledige verbindingstekenreeks naar uw klembord gekopieerd.

    ![Schermafbeelding van de knop kopiëren naar klembord](./media/mongodb-compass/mongodb-connection-copy.png)

3. Open Compass op uw bureaublad/machine en klik op **Verbinding maken** en maak **vervolgens verbinding met...**.

4. Compass detecteert automatisch een verbindingstekenreeks in het klembord en vraagt u om te vragen of u dat wilt gebruiken om verbinding te maken. Klik op **Ja** zoals getoond in de screenshot hieronder.

    ![Schermafbeelding van de prompt Kompas om verbinding te maken](./media/mongodb-compass/mongodb-compass-detect.png)

5. Wanneer u in de bovenstaande stap op **Ja** klikt, worden uw gegevens uit de verbindingstekenreeks automatisch ingevuld. Verwijder de waarde die automatisch wordt ingevuld in het veld Naam van de **replicaset** om ervoor te zorgen dat deze leeg blijft.

    ![Schermafbeelding van de prompt Kompas om verbinding te maken](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klik onderaan de pagina op **Verbinding maken.** Uw Cosmos DB-account en databases moeten nu zichtbaar zijn binnen MongoDB Compass.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
