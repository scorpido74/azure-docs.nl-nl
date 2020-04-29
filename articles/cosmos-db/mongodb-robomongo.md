---
title: Robo 3T gebruiken gebruiken om verbinding te maken met Azure Cosmos DB
description: Meer informatie over hoe u verbinding maakt met Azure Cosmos DB met behulp van Robo 3T gebruiken en de API van Azure Cosmos DB voor MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114194"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Robo 3T gebruiken met de Azure Cosmos DB-API voor MongoDB

Als u verbinding wilt maken met een Cosmos-account via Robo 3T gebruiken, moet u het volgende doen:

* Down load en Installeer [Robo 3T gebruiken](https://robomongo.org/)
* Informatie over uw Cosmos DB [Connection String](connect-mongodb-account.md)

> [!NOTE]
> Momenteel worden Robo 3T gebruiken v 1.2 en lagere versies ondersteund met de API van Cosmos DB voor MongoDB.

## <a name="connect-using-robo-3t"></a>Verbinding maken via Robo 3T

Voer de volgende stappen uit om uw Cosmos-account toe te voegen aan de Robo 3T gebruiken-verbindings beheer:

1. Haal de verbindings gegevens op voor uw Cosmos-account dat is geconfigureerd met de API-MongoDB van Azure Cosmos DB met behulp van de instructies [hier](connect-mongodb-account.md).

    ![Scherm afbeelding van de Blade connection string](./media/mongodb-robomongo/connectionstringblade.png)
2. Voer de toepassing *Robomongo* uit.

3. Klik op de knop verbinding onder **bestand** om uw verbindingen te beheren. Klik vervolgens op **maken** in het venster **MongoDb-verbindingen** , waarmee het venster **Verbindings instellingen** wordt geopend.

4. Kies in het venster **Verbindings instellingen** een naam. Zoek vervolgens de **host** en **poort** uit uw verbindings gegevens in stap 1 en geef deze op in respectievelijk **adres** en **poort**.

    ![Scherm afbeelding van de Robomongo-verbindingen beheren](./media/mongodb-robomongo/manageconnections.png)
5. Klik op het tabblad **verificatie** op **verificatie uitvoeren**. Voer vervolgens uw data base (standaard *beheerder*), **gebruikers naam** en **wacht woord**in.
De **gebruikers naam** en het **wacht woord** kunnen worden gevonden in de verbindings gegevens in stap 1.

    ![Scherm afbeelding van het tabblad Robomongo-verificatie](./media/mongodb-robomongo/authentication.png)
6. Schakel op het tabblad **SSL** het selectie vakje **SSL-protocol gebruiken**in en wijzig de **verificatie methode** in een **zelfondertekend certificaat**.

    ![Scherm afbeelding van het Robomongo SSL-tabblad](./media/mongodb-robomongo/SSL.png)
7. Klik ten slotte op **testen** om te controleren of u verbinding kunt maken en **Sla**het op.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
