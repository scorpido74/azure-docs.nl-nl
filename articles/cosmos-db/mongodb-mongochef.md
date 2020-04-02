---
title: Studio 3T gebruiken om verbinding te maken met de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over hoe u verbinding maken met de API van Azure Cosmos DB voor MongoDB met Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548827"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Verbinding maken met een Azure Cosmos-account met Studio 3T

Als u verbinding wilt maken met de API van Azure Cosmos DB voor MongoDB met Studio 3T, moet u het:

* Studio [3T](https://studio3t.com/)downloaden en installeren.
* Heb de [verbindingstekenreeksgegevens](connect-mongodb-account.md) van uw Azure Cosmos-account.

## <a name="create-the-connection-in-studio-3t"></a>De verbinding maken in Studio 3T

Voer de volgende stappen uit om uw Azure Cosmos-account toe te voegen aan de Studio 3T-verbindingsmanager:

1. Haal de verbindingsgegevens voor de API van uw Azure Cosmos DB voor MongoDB-account op met behulp van de instructies in de [Toepassing Connect a MongoDB met Azure Cosmos DB.](connect-mongodb-account.md)

    ![Schermafbeelding van de pagina verbindingstekenreeks](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Klik **op Verbinding maken** om verbindingsbeheer te openen en klik vervolgens op Nieuwe **verbinding**

    ![Schermafbeelding van de Studio 3T-verbindingsmanager](./media/mongodb-mongochef/ConnectionManager.png)
3. Voer in het venster **Nieuwe verbinding** op het tabblad **Server** de HOST (FQDN) van het Azure Cosmos-account en de POORT in.

    ![Schermafbeelding van het tabblad Studio 3T-verbindingsbeheerserver](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Kies in het venster **Nieuwe verbinding** op het tabblad **Verificatiebasis** **(MONGODB-CR of SCARM-SHA-1)** en voer de gebruikersnaam en het wachtwoord in.  Accepteer de standaardverificatie db (admin) of geef uw eigen waarde op.

    ![Schermafbeelding van het tabblad verificatie van de studio 3T-verbindingsbeheer](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Schakel in het venster **Nieuwe verbinding** op het **tabblad SSL** het selectievakje **SSL-protocol gebruiken om verbinding te maken** en de knop **Serverzelfondertekende SSL-certificaten-radio accepteren** in.

    ![Schermafbeelding van het SSL-tabblad Studio 3T-verbindingsbeheer](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klik op de knop **Verbindingstesten** om de verbindingsgegevens te valideren, klik op **OK** om terug te keren naar het venster Nieuwe verbinding en klik vervolgens op **Opslaan**.

    ![Schermafbeelding van het studio 3T-testverbindingsvenster](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T gebruiken om een database, verzameling en documenten te maken
Voer de volgende stappen uit om een database, verzameling en documenten te maken met Studio 3T:

1. Markeer in **Verbindingsbeheer**de verbinding en klik op **Verbinden**.

    ![Schermafbeelding van de Studio 3T-verbindingsmanager](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klik met de rechtermuisknop op de host en kies **Database toevoegen**.  Geef een databasenaam op en klik op **OK**.

    ![Schermafbeelding van de optie Database toevoegen van Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Klik met de rechtermuisknop op de database en kies **Verzameling toevoegen**.  Geef een verzamelingsnaam op en klik op **Maken**.

    ![Schermafbeelding van de optie Verzameling toevoegen van Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Klik op het **menu-item Verzameling** en klik vervolgens op **Document toevoegen**.

    ![Schermafbeelding van het menu-item Document toevoegen van Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. Plak in het dialoogvenster Document toevoegen het volgende en klik vervolgens op **Document toevoegen**.

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
6. Voeg een ander document toe, dit keer met de volgende inhoud:

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
7. Een voorbeeldquery uitvoeren. Zoek bijvoorbeeld naar gezinnen met de achternaam 'Andersen' en geef de ouders en staatsvelden terug.

    ![Schermafbeelding van de queryresultaten van Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
