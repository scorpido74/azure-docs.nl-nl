---
title: 'Quickstart: Table-API met Python - Azure Cosmos DB'
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en Python
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 9de9739efce13fc96bf550759eb0ef68d732af1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77212709"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Quickstart: Een Table-API compileren met Python en Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

In deze quickstart maakt en beheert u een Azure Cosmos DB Table API-account vanuit de Azure-portal en vanuit Visual Studio met een Python-app gekloond vanuit GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Visual Studio 2019](https://www.visualstudio.com/downloads/), met de **Azure-ontwikkel-** en **Python-ontwikkelingsworkloads** geselecteerd tijdens de installatie. 
- [Git.](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>Een databaseaccount maken

> [!IMPORTANT] 
> U moet een nieuw Table-API-account maken om te kunnen werken met de algemeen beschikbare SDK's voor de Table-API. Table-API-accounts die zijn gemaakt tijdens de preview worden niet ondersteund door de algemeen beschikbare SDK's.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Een tabel toevoegen

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Table-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Open vervolgens het oplossingenbestand in Visual Studio. 

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Selecteer **Verbindingstekenreeks**in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/). 

    ![De verbindingstekenreeks weergeven en kopiëren in de instellingen voor verbindingstekenreeks](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Kopieer de ACCOUNT NAME met behulp van de knop aan de rechterkant.

3. Open het *config.py-bestand* en plak de accountnaam van de portal in de STORAGE_ACCOUNT_NAME waarde op regel 19.

4. Ga terug naar de portal en kopieer de PRIMARY KEY.

5. Plak de PRIMARY KEY vanuit de portal in de waarde STORAGE_ACCOUNT_KEY op regel 20.

6. Sla het *config.py-bestand* op.

## <a name="run-the-app"></a>De app uitvoeren

1. Klik in Visual Studio met de rechtermuisknop op het project in **Solution Explorer.**

2. Selecteer de huidige Python-omgeving en klik met de rechtermuisknop.

2. Selecteer **Python-pakket installeren**en voer vervolgens *de Azure-storage-tabel*in .

3. Druk op F5 om de toepassing uit te voeren. Uw app wordt in uw browser weergegeven. 

U kunt nu teruggaan naar Data Explorer en deze nieuwe gegevens bekijken, wijzigen, opvragen en ermee werken. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een tabel maakt met de Data Explorer en een Python-app in Visual Studio uitvoert om tabelgegevens toe te voegen.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)
