---
title: Studio 3T gebruiken gebruiken om verbinding te maken met de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over hoe u verbinding kunt maken met de API van een Azure Cosmos DB voor MongoDB met Studio 3T gebruiken.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/01/2019
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 533917e4cc39a1f4885a1604c11480fe09870c61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441616"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Verbinding maken met een Azure Cosmos-account met Studio 3T gebruiken

Als u verbinding wilt maken met de API van een Azure Cosmos DB voor MongoDB met Studio 3T gebruiken, moet u het volgende doen:

* Down load en Installeer [Studio 3T gebruiken](https://studio3t.com/).
* Laat de [Connection String](connect-mongodb-account.md) informatie over uw Azure Cosmos-account.

> [!NOTE]
> Momenteel worden Robo 3T gebruiken v 1.2 en lagere versies ondersteund met de API van Cosmos DB voor MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>De verbinding in Studio 3T maken

Gebruik de volgende stappen om uw Azure Cosmos-account toe te voegen aan de Studio 3T gebruiken-verbindings beheer:

1. Haal de verbindings gegevens voor de API van uw Azure Cosmos DB voor het MongoDB-account op met behulp van de instructies in het artikel [een MongoDb-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) .

    ![Scherm afbeelding van de connection string pagina](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Klik op **Connect** om te openen in de Connection Manager, klikt u vervolgens op **nieuwe verbinding**

    ![Scherm afbeelding van de Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManager.png)
3. Voer in het venster **nieuwe verbinding** , op het tabblad **Server** , de host (FQDN) van het Azure Cosmos-account en de poort in.

    ![Scherm afbeelding van het tabblad Server van Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. In de **nieuwe verbinding** venster op de **verificatie** Kies verificatiemodus **Basic (MONGODB-CR of SCARM-SHA-1)** en voer de gebruikersnaam en wachtwoord.  Accepteer de standaard verificatie db (admin) of geef uw eigen waarde.

    ![Scherm afbeelding van het tabblad Verificatie van Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. In de **nieuwe verbinding** venster op de **SSL** tabblad en controleer de **Gebruik SSL-protocol om verbinding te** selectievakje en de **server zelf-ondertekend SSL-certificaten accepteren**  keuzerondje.

    ![Scherm afbeelding van het tabblad SSL 3T gebruiken-verbindings beheer van Studio](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klik op de **testverbinding** knop valideren van de verbindingsgegevens, klikt u op **OK** terug te keren naar de nieuwe verbinding en klik vervolgens op **opslaan**.

    ![Scherm afbeelding van het venster Studio 3T gebruiken test-verbinding](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T gebruiken voor het maken van een database, verzameling en documenten
Voor het maken van een database, verzameling en documenten Studio 3T gebruiken, moet u de volgende stappen uitvoeren:

1. In **Connection Manager**, markeert u de verbinding en klikt u op **Connect**.

    ![Scherm afbeelding van de Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectToAccount.png)
2. Met de rechtermuisknop op de host en kies **Database toevoegen**.  Geef een databasenaam op en klik op **OK**.

    ![Scherm afbeelding van de optie Studio 3T gebruiken add data base](./media/mongodb-mongochef/AddDatabase1.png)
3. Met de rechtermuisknop op de database en kies **verzameling toevoegen**.  Geef de naam van een verzameling en klik op **maken**.

    ![Scherm afbeelding van de optie voor het toevoegen van de Studio 3T gebruiken-verzameling](./media/mongodb-mongochef/AddCollection.png)
4. Klik op de **verzameling** menu, klikt u vervolgens op **Document toevoegen**.

    ![Scherm afbeelding van het menu-item Studio 3T gebruiken add document](./media/mongodb-mongochef/AddDocument1.png)
5. Plak het volgende in het dialoogvenster Document toevoegen en klik vervolgens op **Document toevoegen**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Voeg een ander document, ditmaal met de volgende inhoud:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Een voorbeeld-query uitvoeren. Bijvoorbeeld zoeken naar gezinnen zijn met de achternaam 'Andersen' en de ouders en statusvelden retourneren.

    ![Scherm opname van Mongo chef-query resultaten](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
