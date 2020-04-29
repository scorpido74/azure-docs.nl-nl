---
title: Studio 3T gebruiken gebruiken om verbinding te maken met de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over hoe u verbinding kunt maken met de API van een Azure Cosmos DB voor MongoDB met Studio 3T gebruiken.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548827"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Verbinding maken met een Azure Cosmos-account met Studio 3T gebruiken

Als u verbinding wilt maken met de API van een Azure Cosmos DB voor MongoDB met Studio 3T gebruiken, moet u het volgende doen:

* Down load en Installeer [Studio 3T gebruiken](https://studio3t.com/).
* Laat de [Connection String](connect-mongodb-account.md) informatie over uw Azure Cosmos-account.

## <a name="create-the-connection-in-studio-3t"></a>Maak de verbinding in Studio 3T gebruiken

Gebruik de volgende stappen om uw Azure Cosmos-account toe te voegen aan de Studio 3T gebruiken-verbindings beheer:

1. Haal de verbindings gegevens voor de API van uw Azure Cosmos DB voor het MongoDB-account op met behulp van de instructies in het artikel [een MongoDb-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) .

    ![Scherm afbeelding van de connection string pagina](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Klik op **verbinding maken** om verbindings beheer te openen en klik vervolgens op **nieuwe verbinding**

    ![Scherm afbeelding van de Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManager.png)
3. Voer in het venster **nieuwe verbinding** , op het tabblad **Server** , de host (FQDN) van het Azure Cosmos-account en de poort in.

    ![Scherm afbeelding van het tabblad Server van Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Kies in het venster **nieuwe verbinding** op het tabblad **verificatie** de optie verificatie modus **Basic (MONGODB-CR of SCARM-SHA-1)** en voer de gebruikers naam en het wacht woord in.  Accepteer de standaard verificatie database (beheerder) of geef uw eigen waarde op.

    ![Scherm afbeelding van het tabblad Verificatie van Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Schakel in het venster **nieuwe verbinding** , op het tabblad **SSL** , het selectie vakje **SSL-protocol gebruiken om verbinding te maken** in en het keuze rondje **Server zelfondertekende SSL-certificaten accepteren** .

    ![Scherm afbeelding van het tabblad SSL 3T gebruiken-verbindings beheer van Studio](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klik op de knop **verbinding testen** om de verbindings gegevens te valideren, klik op **OK** om terug te keren naar het venster nieuwe verbinding en klik vervolgens op **Opslaan**.

    ![Scherm afbeelding van het venster Studio 3T gebruiken test-verbinding](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T gebruiken gebruiken om een Data Base, verzameling en documenten te maken
Voer de volgende stappen uit om een Data Base, verzameling en documenten te maken met behulp van Studio 3T gebruiken:

1. Markeer in **verbindings beheer**de verbinding en klik op **verbinding maken**.

    ![Scherm afbeelding van de Studio 3T gebruiken-verbindings beheer](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klik met de rechter muisknop op de host en kies **Data Base toevoegen**.  Geef een database naam op en klik op **OK**.

    ![Scherm afbeelding van de optie Studio 3T gebruiken add data base](./media/mongodb-mongochef/AddDatabase1.png)
3. Klik met de rechter muisknop op de data base en kies **verzameling toevoegen**.  Geef een naam voor de verzameling op en klik op **maken**.

    ![Scherm afbeelding van de optie voor het toevoegen van de Studio 3T gebruiken-verzameling](./media/mongodb-mongochef/AddCollection.png)
4. Klik op het menu-item **verzameling** en klik vervolgens op **document toevoegen**.

    ![Scherm afbeelding van het menu-item Studio 3T gebruiken add document](./media/mongodb-mongochef/AddDocument1.png)
5. Plak het volgende in het dialoog venster document toevoegen en klik vervolgens op **document toevoegen**.

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
6. Voeg nog een document toe met de volgende inhoud:

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
7. Een voorbeeld query uitvoeren. Zoek bijvoorbeeld naar families met de achternaam ' Splinter ' en retour neer de velden ouders en provincie.

    ![Scherm opname van Mongo chef-query resultaten](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
