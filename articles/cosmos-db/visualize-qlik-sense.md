---
title: Verbind Qlik Sense met Azure Cosmos DB en visualiseer uw gegevens
description: In dit artikel worden de stappen beschreven die nodig zijn om Azure Cosmos DB met Qlik Sense te verbinden en uw gegevens te visualiseren.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67985552"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Verbind Qlik Sense met Azure Cosmos DB en visualiseer uw gegevens

Qlik Sense is een tool voor gegevensvisualisatie die gegevens uit verschillende bronnen combineert in één weergave. Qlik Sense indexeert elke mogelijke relatie in uw gegevens, zodat u direct inzicht krijgt in de gegevens. U Azure Cosmos DB-gegevens visualiseren met Qlik Sense. In dit artikel worden de stappen beschreven die nodig zijn om Azure Cosmos DB met Qlik Sense te verbinden en uw gegevens te visualiseren. 

> [!NOTE]
> Het aansluiten van Qlik Sense met Azure Cosmos DB wordt momenteel alleen ondersteund voor SQL API en Azure Cosmos DB's API voor MongoDB-accounts.

U Qlik Sense verbinden met Azure Cosmos DB met:

* Cosmos DB SQL API met behulp van de ODBC-connector.

* Azure Cosmos DB's API voor MongoDB met behulp van de Qlik Sense MongoDB-connector (momenteel in preview).

* De API van Azure Cosmos DB voor MongoDB en SQL API met restapi-connector in Qlik Sense.

* Cosmos DB Mongo DB API met de gRPC-connector voor Qlik Core.
In dit artikel worden de details beschreven van het verbinden met de Cosmos DB SQL API met behulp van de ODBC-connector.

In dit artikel worden de details beschreven van het verbinden met de Cosmos DB SQL API met behulp van de ODBC-connector.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u de instructies in dit artikel volgt, dat u de volgende bronnen gereed hebt:

* Download de [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) of stel Qlik Sense in Azure in door [het Qlik Sense marketplace-item te installeren.](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)

* Download de [video game gegevens,](https://www.kaggle.com/gregorut/videogamesales)deze voorbeeldgegevens zijn in CSV-formaat. Je slaat deze gegevens op in een Cosmos DB-account en visualiseert deze in Qlik Sense.

* Maak een Azure Cosmos DB SQL API-account met behulp van de stappen die zijn beschreven in [het maken van een accountsectie](create-sql-api-dotnet.md#create-account) van het quickstart-artikel.

* [Een database en een verzameling maken](create-sql-api-java.md#add-a-container) : u de doorvoerwaarde van de verzameling instellen op 1000 RU/s. 

* Laad de verkoopgegevens van de voorbeeldvideogame naar uw Cosmos DB-account. U de gegevens importeren met behulp van Azure Cosmos DB-gegevensmigratietool, u een [sequentiële](import-data.md#SQLSeqTarget) of een [bulkimport](import-data.md#SQLBulkTarget) van gegevens doen. Het duurt ongeveer 3-5 minuten voor de gegevens te importeren naar de Cosmos DB account.

* Download, installeer en configureer het ODBC-stuurprogramma met behulp van de stappen in de [verbinding met Cosmos DB met ODBC-stuurprogrammaartikel.](odbc-driver.md) De video game gegevens is een eenvoudige dataset en je hoeft niet om het schema te bewerken, gewoon gebruik maken van de standaard collectie-mapping schema.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Verbind Qlik Sense met Cosmos DB

1. Open Qlik Sense en selecteer **Nieuwe app maken**. Geef een naam op voor uw app en selecteer **Maken**.

   ![Een nieuwe Qlik Sense-app maken](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Nadat de nieuwe app is gemaakt, selecteert u **App openen** en kiest u Gegevens toevoegen uit bestanden en **andere bronnen**. 

3. Selecteer **ODBC in** de gegevensbronnen om het nieuwe venster voor het instellen van verbindingen te openen. 

4. Schakel over naar **Gebruiker DSN** en kies de ODBC-verbinding die u eerder hebt gemaakt. Geef een naam op voor de verbinding en selecteer **Maken**. 

   ![Een nieuwe verbinding maken](./media/visualize-qlik-sense/create-new-connection.png)

5. Nadat u de verbinding hebt gemaakt, u de database kiezen, verzamelen waar de videogamegegevens zich bevinden en deze vervolgens bekijken.

   ![De database en verzameling kiezen](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Selecteer vervolgens **Gegevens toevoegen** om de gegevens naar Qlik Sense te laden. Nadat u gegevens hebt geladen naar Qlik Sense, u inzichten genereren en analyses uitvoeren op de gegevens. Je de inzichten gebruiken of je eigen app bouwen om de verkoop van videogames te verkennen. De volgende afbeelding wordt weergegeven 

   ![Gegevens visualiseren](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Beperkingen bij het verbinden met ODBC 

Cosmos DB is een schemaloze gedistribueerde database met stuurprogramma's gemodelleerd naar ontwikkelaarsbehoeften. Het ODBC-stuurprogramma vereist een database met schema om kolommen, hun gegevenstypen en andere eigenschappen af te leiden. De reguliere SQL-query of de DML-syntaxis met relationele mogelijkheden is niet van toepassing op Cosmos DB SQL API omdat SQL API geen ANSI SQL is. Om deze reden worden de SQL-instructies die via het ODBC-stuurprogramma worden uitgegeven, vertaald naar Cosmos DB-specifieke SQL-syntaxis die geen equivalenten heeft voor alle constructies. Als u deze vertaalproblemen wilt voorkomen, moet u een schema toepassen bij het instellen van de ODBC-verbinding. Het [artikel verbinding met ODBC-stuurprogramma's](odbc-driver.md) geeft u suggesties en methoden om u te helpen het schema te configureren. Zorg ervoor dat u deze toewijzing maakt voor elke database/verzameling binnen het Cosmos DB-account.

## <a name="next-steps"></a>Volgende stappen

Als u een ander visualisatiegereedschap gebruikt, zoals Power BI, u er verbinding mee maken met de instructies in het volgende document:

* [Cosmos DB-gegevens visualiseren met de Power BI-connector](powerbi-visualize.md)
