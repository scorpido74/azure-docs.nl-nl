---
title: Azure Cosmos DB Table API .NET Standard SDK & Resources
description: Lees alles over de Azure Cosmos DB Table API en de .NET Standard SDK, inclusief releasedatums, pensioendatums en wijzigingen die tussen elke versie zijn aangebracht.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: f7c6829d858e36b55f079f8ee03e431f67995e66
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272573"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: Notities downloaden en vrijgeven
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK downloaden**|[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Monster**|[Voorbeeld van Cosmos DB Table API .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Snelstartgids**|[Snelstartgids](create-table-dotnet.md)|
|**Zelfstudie**|[Zelfstudie](tutorial-develop-table-dotnet.md)|
|**Huidig ondersteund kader**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Rapportprobleem**|[Rapportprobleem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Release notes voor 2.0.0-serie
2.0.0-serie neemt de afhankelijkheid van [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), met prestatieverbeteringen en naamruimteconsolidatie naar Cosmos DB-eindpunt.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* eerste preview van 2.0.0 Table SDK die de afhankelijkheid van [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)neemt, met prestatieverbeteringen en naamruimteconsolidatie naar Cosmos DB-eindpunt. De openbare API blijft hetzelfde.

## <a name="release-notes-for-100-series"></a>Release notes voor 1.0.0-serie
1.0.0-serie is afhankelijk van [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="107"></a><a name="1.0.7"/>1.0.7
* Prestatieverbetering door het standaardtraceringsniveau van Tabel SDK in te stellen op SourceLevels.Off, waarvoor u zich via app.config aanmelden

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Nieuwe config introduceren onder TableClientConfiguration om Rest Executor te gebruiken om te communiceren met Cosmos DB Table API

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-preview
* Opgeloste fouten

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Opgeloste fouten
* Geef de optie HttpClientTimeout op voor RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-preview
* Opgeloste fouten
* Geef de optie HttpClientTimeout op voor RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Opgeloste fouten

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Algemene beschikbaarheidsrelease

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* Er zijn wijzigingen aangebracht in de manier waarop CloudTableClient kan worden geconfigureerd. Het duurt nu een tableClientConfiguration object tijdens de bouw. TableClientConfiguration biedt verschillende eigenschappen om het clientgedrag te configureren, afhankelijk van of het doeleindpunt Cosmos DB Table API of Azure Storage Table API is.
* Ondersteuning toegevoegd aan TableQuery om resultaten in gesorteerde volgorde op een aangepaste kolom terug te sturen. Deze functie wordt alleen ondersteund op Cosmos DB Table-eindpunten.
* Ondersteuning toegevoegd om RequestCharges bloot te leggen voor verschillende resultaattypen. Deze functie wordt alleen ondersteund op Cosmos DB Table-eindpunten.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Voeg ondersteuning voor SAS-token, bewerkingen van TablePermissions, ServiceProperties en ServiceStats toe op azure storage table-eindpunten. 
   > [!NOTE]
   > Sommige functionaliteiten in eerdere Azure Storage Table SDKs worden nog niet ondersteund, zoals versleuteling aan de clientzijde.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Voeg ondersteuning toe voor kern-CRUD-, batch- en querybewerkingen op azure storagetable-eindpunten. 
   > [!NOTE]
   > Sommige functionaliteiten in eerdere Azure Storage Table SDKs worden nog niet ondersteund, zoals versleuteling aan de clientzijde.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Azure Cosmos DB Table .NET Standard SDK is een cross-platform .NET-bibliotheek die efficiënte toegang biedt tot het tabelgegevensmodel op Cosmos DB. Deze eerste release ondersteunt de volledige set crud + queryfunctionaliteiten van tabel en entiteit met vergelijkbare API's als de [Cosmos DB Table SDK Voor .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure Storage Table-eindpunten worden nog niet ondersteund in de versie met 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Release- en pensioendata
Microsoft biedt ten minste **12 maanden** van tevoren een melding om een SDK met pensioen te laten gaan om de overgang naar een nieuwere/ondersteunde versie soepel te laten verlopen.

Deze cross-platform .NET Standard library [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) vervangt de .NET Framework library [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>2.0.0-serie
| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0-serie
| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 september 2019 |--- |
| [1.0.5-preview](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-preview](#1.0.4-preview) |26 juli 2019 |--- |
| 1.0.2-preview |2 mei 2019 |--- |
| [1.0.1](#1.0.1) |19 april 2019 |--- |
| [1.0.0](#1.0.0) |13 maart 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5 maart 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 januari 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 december 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18 oktober 2018 |--- |


## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie Inleiding tot Azure Cosmos DB [Table API](table-introduction.md)voor meer informatie over de Azure Cosmos DB Table API.
