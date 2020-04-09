---
title: Azure Cosmos DB Table API .NET SDK & Resources
description: Lees alles over de Azure Cosmos DB Table API, inclusief releasedatums, pensioendatums en wijzigingen die tussen elke versie zijn aangebracht.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 9356ab0aec375a046a8f200838df296b2cf74bce
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984977"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Notities downloaden en vrijgeven

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK downloaden**|[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Snelstartgids**|[Azure Cosmos DB: een app bouwen met .NET en de tabel-API](create-table-dotnet.md)|
|**Zelfstudie**|[Azure Cosmos DB: ontwikkelen met de tabel-API in .NET](tutorial-develop-table-dotnet.md)|
|**Huidig ondersteund kader**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> De .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) bevindt zich in de onderhoudsmodus en wordt binnenkort afgeschaft. Upgrade naar de nieuwe .NET Standard-bibliotheek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) om de nieuwste functies te blijven ondersteunen door de Tabel-API.

> Als u tijdens de preview een tabel-API-account hebt gemaakt, moet u een [nieuw tabel-API-account](create-table-dotnet.md#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK’s voor tabel-API's.
>

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="212"></a><a name="2.1.2"/>2.1.2

* Opgeloste fouten

### <a name="210"></a><a name="2.1.0"/>2.1.0

* Opgeloste fouten

### <a name="200"></a><a name="2.0.0"/>2.0.0

* Ondersteuning voor schrijven in meerdere regio's toegevoegd
* Vaste NuGet-pakketafhankelijkheden van Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="113"></a><a name="1.1.3"/>1.1.3

* Vaste nuget-pakketafhankelijkheden van Microsoft.Azure.Storage.Common en Microsoft.Azure.DocumentDB.
* Bugfixes op tabelserialisatie wanneer JsonConvert.DefaultSettings zijn geconfigureerd.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Validatie toegevoegd voor verkeerd gevormde ETAGs in direct modus.
* Fixed LINQ query bug in Gateway Mode.
* Synchrone API's worden nu uitgevoerd op de threadpool met SynchronisatieContext.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* TabelQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelisme en TableQueryContinuationTokenLimitInKb toevoegen aan TabelRequestOptions
* Opgeloste fouten

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Algemene beschikbaarheidsrelease

### <a name="090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* Eerste preview-release

## <a name="release-and-retirement-dates"></a>Release- en pensioendata

Microsoft biedt ten minste **12 maanden** van tevoren een melding om een SDK met pensioen te laten gaan om de overgang naar een nieuwere/ondersteunde versie soepel te laten verlopen.

De `Microsoft.Azure.CosmosDB.Table` bibliotheek is momenteel alleen beschikbaar voor .NET Framework en is in onderhoudsmodus en zal binnenkort worden afgeschaft. Nieuwe functies en functionaliteiten en optimalisaties worden alleen toegevoegd aan de .NET Standard-bibliotheek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), als zodanig wordt aanbevolen om te upgraden naar [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Het [previewpakket WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) is afgeschaft. De WindowsAzure.Storage-PremiumTable SDK wordt op 15 november 2018 buiten gebruik gesteld, waarna aanvragen bij de gepensioneerde SDK niet zijn toegestaan. 

Alle aanvragen voor Azure Cosmos DB met behulp van een gepensioneerde SDK worden door de service afgewezen.
<br/>

| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 september 2019| |
| [2.1.0](#2.1.0) |22 januari 2019|01 april 2020 |
| [2.0.0](#2.0.0) |26 september 2018|01 maart 2020 |
| [1.1.3](#1.1.3) |17 juli 2018|01 december 2019 |
| [1.1.1](#1.1.1) |26 maart 2018|01 december 2019 |
| [1.1.0](#1.1.0) |21 februari 2018|01 december 2019 |
| [1.0.0](#1.0.0) |15 november 2017|15 november 2019 |
| 0.9.0-preview |11 november 2017 |11 november 2019 |

## <a name="troubleshooting"></a>Problemen oplossen

Als u de fout krijgt 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

wanneer u het NuGet-pakket Microsoft.Azure.CosmosDB.Table NuGet probeert te gebruiken, hebt u twee opties om het probleem op te lossen:

* Gebruik Package Manage Console om het Microsoft.Azure.CosmosDB.Table-pakket en de afhankelijkheden ervan te installeren. Typ hiervoor het volgende in de Package Manager Console voor uw oplossing. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Installeer het Microsoft.Azure.Storage.Common NuGet-pakket met behulp van uw favoriete NuGet-pakketbeheertool voordat u Microsoft.Azure.CosmosDB.Table installeert.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook

Zie Inleiding tot Azure Cosmos DB [Table API](table-introduction.md)voor meer informatie over de Azure Cosmos DB Table API. 
