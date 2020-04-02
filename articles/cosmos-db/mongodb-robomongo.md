---
title: Robo 3T gebruiken om verbinding te maken met Azure Cosmos DB
description: Meer informatie over hoe u verbinding maken met Azure Cosmos DB met de API van Robo 3T en Azure Cosmos DB voor MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 68b880957754439b3b88a0cccff2218a5942f967
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548819"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Robo 3T gebruiken met de Azure Cosmos DB-API voor MongoDB

Als u verbinding wilt maken met cosmos-account met Robo 3T, moet u het:

* Robo [3T](https://robomongo.org/) downloaden en installeren
* Gegevens over de Cosmos [DB-verbindingstekenreeks](connect-mongodb-account.md)

> [!NOTE]
> Momenteel worden Robo 3T v1.2 en lagere versies ondersteund met Cosmos DB's API voor MongoDB.

## <a name="connect-using-robo-3t"></a>Verbinding maken via Robo 3T

Voer de volgende stappen uit om uw Cosmos-account toe te voegen aan de Robo 3T-verbindingsmanager:

1. Haal de verbindingsgegevens op voor uw Cosmos-account die is geconfigureerd met de API MongoDB van Azure Cosmos DB met behulp van de [instructies hier](connect-mongodb-account.md).

    ![Schermafbeelding van het verbindingstekenreeksblad](./media/mongodb-robomongo/connectionstringblade.png)
2. Run *Robomongo.exe*

3. Klik op de verbindingsknop onder **Bestand** om uw verbindingen te beheren. Klik vervolgens op **Maken** in het venster **MongoDB-verbindingen,** waarmee het venster **Verbindingsinstellingen** wordt geopend.

4. Kies in het venster **Verbindingsinstellingen** een naam. Zoek vervolgens de **host** en **poort** van uw verbindingsgegevens in stap 1 en voer deze in respectievelijk **adres** en **poort**in.

    ![Schermafbeelding van de verbindingen voor robomongo beheren](./media/mongodb-robomongo/manageconnections.png)
5. Klik **op** het tabblad Verificatie op **Verificatie uitvoeren**. Voer vervolgens uw database in (standaard is *Beheerder),* **Gebruikersnaam** en **Wachtwoord**.
Zowel **gebruikersnaam** als **wachtwoord** zijn te vinden in uw verbindingsgegevens in stap 1.

    ![Schermafbeelding van het tabblad Robomongo-verificatie](./media/mongodb-robomongo/authentication.png)
6. Controleer op het **tabblad SSL** het **SSL-protocol gebruiken**en wijzig vervolgens de **verificatiemethode** in **zelfondertekend certificaat**.

    ![Schermafbeelding van het tabblad Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Klik tot slot op **Testen** om te controleren of u verbinding maken en sla vervolgens **op**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
