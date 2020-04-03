---
title: Een MongoDB-toepassing verbinden met Azure Cosmos DB
description: Meer informatie over het verbinden van een MongoDB-app met Azure Cosmos DB door de verbindingstekenreeks van Azure-portal te halen
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: cba16d79b6506f9809a76f0128938a68afd15c92
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617041"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Een MongoDB-toepassing verbinden met Azure Cosmos DB

Ontdek hoe u uw MongoDB-app met een Azure Cosmos DB kunt verbinden met behulp van een MongoDB-verbindingsreeks. U kunt vervolgens een Azure Cosmos-database gebruiken als het gegevensarchief voor uw MongoDB-app.

In deze zelfstudie kunt u op twee manieren gegevens van verbindingsreeksen ophalen:

- [De quickstart-methode](#get-the-mongodb-connection-string-by-using-the-quick-start), voor gebruik met .NET, Node.js, MongoDB Shell, Java en Python drivers
- [De aangepaste verbindingstekenreeksmethode](#get-the-mongodb-connection-string-to-customize)voor gebruik met andere stuurprogramma's

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen Azure-account hebt, maakt u nu een [gratis Azure-account.](https://azure.microsoft.com/free/)
- Een Cosmos-account. Zie Een [web-app bouwen met de API van Azure Cosmos DB voor MongoDB en .NET SDK voor](create-mongodb-dotnet.md)instructies.

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Download de MongoDB-verbindingstekenreeks met de snelle start

1. Meld u in een internetbrowser aan bij de [Azure-portal.](https://portal.azure.com)
2. Selecteer de API in het **Azure Cosmos DB-blad.**
3. Klik in het linkerdeelvenster van het accountblad op **Snel starten**.
4. Kies uw platform (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Als u uw stuurprogramma of hulpprogramma niet in de lijst ziet staan, hoeft u zich geen zorgen te maken: we documenteren continu meer verbindingscodefragmenten. Reageer hieronder op wat je wilt zien. Lees De [verbindingstekenreeksgegevens van het account voor](#get-the-mongodb-connection-string-to-customize)meer informatie over het maken van uw eigen verbinding.
5. Kopieer en plak het codefragment in uw MongoDB-app.

    ![Snel startblad](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a>De MongoDB-verbindingstekenreeks aanpassen

1. Meld u in een internetbrowser aan bij de [Azure-portal.](https://portal.azure.com)
2. Selecteer de API in het **Azure Cosmos DB-blad.**
3. Klik in het linkerdeelvenster van het accountblad op **Verbindingstekenreeks**.
4. Het **verbindingsstouwsblad** wordt geopend. Het heeft alle informatie die nodig is om verbinding te maken met de rekening met behulp van een driver voor MongoDB, inclusief een vooraf gebouwde verbindingstekenreeks.

   [![Verbindingsstouwsblad](./media/connect-mongodb-account/ConnectionStringBlade.png)](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Vereisten voor verbindingstekenreeks

> [!Important]
> Voor Azure Cosmos DB gelden strenge beveiligingsvereisten en -normen. Azure Cosmos DB-accounts vereisen verificatie en beveiligde communicatie via *TLS.* 
>
>

Azure Cosmos DB ondersteunt de standaard Uri-verbindingstekenreeks URI-indeling voor MongoDB-verbindingen, met een paar specifieke vereisten: Azure Cosmos DB-accounts vereisen verificatie en veilige communicatie via TLS. De verbindingstekenreeksindeling is dus:

    mongodb://username:password@host:port/[database]?ssl=true

De waarden van deze tekenreeks zijn beschikbaar in het eerder weergegeven **verbindingstekenreeksblad:**

* Gebruikersnaam (vereist): Cosmos-accountnaam.
* Wachtwoord (vereist): Wachtwoord van cosmos-account.
* Host (vereist): FQDN van het Cosmos-account.
* Poort (verplicht): 10255.
* Database (optioneel): de database die de verbinding gebruikt. Als er geen database wordt verstrekt, is de standaarddatabase 'test'.
* ssl=true (vereist)

Denk bijvoorbeeld aan het account dat wordt weergegeven in het **verbindingstekenreeksblad.** Een geldige verbindingstekenreeks is:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
