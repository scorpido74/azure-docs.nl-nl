---
title: Verbinding maken met de API van Azure Cosmos DB voor MongoDB met Studio 3T gebruiken
titleSuffix: Azure Cosmos DB
description: Lees hoe u verbinding maakt met Cosmos DB met behulp van Studio 3T gebruiken en de API van Azure Cosmos DB voor MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 46cac6720bbef133d6b3f7a269cf97a81cddd0c5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062118"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Verbinding maken met een Cosmos-account met Studio 3T gebruiken

Als u verbinding wilt maken met een Cosmos-account met behulp van de API van Azure Cosmos DB voor MongoDB, moet u het volgende doen:

* [Studio 3T gebruiken](https://studio3t.com/) downloaden en installeren
* Informatie over uw Cosmos DB [Connection String](connect-mongodb-account.md)

> [!NOTE]
> Momenteel worden Robo 3T gebruiken v 1.2 en lager ondersteund met de API van Cosmos DB voor MongoDB. 

## <a name="create-the-connection-in-studio-3t"></a>De verbinding in Studio 3T maken
Voer de volgende stappen uit om uw Cosmos-account toe te voegen aan de Studio 3T gebruiken-verbindings beheer:

1. Haal de verbindings gegevens op voor uw Cosmos-account dat is geconfigureerd met de API van Azure Cosmos DB voor MongoDB met behulp van de instructies in het artikel [een MongoDb-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) .

    ![Scherm afbeelding van de connection string pagina](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klik op **Connect** om te openen in de Connection Manager, klikt u vervolgens op **nieuwe verbinding**

    ![Scherm afbeelding van de Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManager.png)
3. In de **nieuwe verbinding** venster op de **Server** tabblad, voert u de HOST (FQDN) van het Azure Cosmos DB-account en de poort.

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
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API voor MongoDB van Azure Cosmos DB.
