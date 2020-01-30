---
title: Ondersteuning voor Azure-tabelopslag in Azure Cosmos DB
description: Meer informatie over hoe Azure Cosmos DB Table-API en Azure Storage tabellen samen werken door hetzelfde tabel gegevens model te delen als een bewerking
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76770673"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Ontwikkelen met de tabel-API van Azure Cosmos DB en Azure-tabelopslag

De tabel-API van Azure Cosmos DB en Azure-tabelopslag hebben hetzelfde gegevensmodel voor tabellen en bieden via de bijbehorende SDK’s dezelfde bewerkingen voor maken, verwijderen, bijwerken en uitvoeren van query’s.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Ontwikkelen met de tabel-API van Azure Cosmos DB

Op dit moment beschikt de [tabel-API van Azure Cosmos DB](table-introduction.md) over vier SDK’s voor ontwikkelen: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Deze bibliotheek is gericht op .NET Standard en heeft dezelfde klassen en methodes als de openbare [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), maar biedt ook de mogelijkheid om verbinding te maken met Azure Cosmos DB-accounts met behulp van de Table-API. Gebruikers van .NET Framework bibliotheek [micro soft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) worden aanbevolen om een upgrade uit te zetten naar [micro soft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) omdat deze zich in de onderhouds modus bevindt en binnenkort wordt afgeschaft.

* [PYTHON SDK](table-sdk-python.md): de nieuwe Azure Cosmos DB python SDK is de enige SDK die Azure Table-opslag in python ondersteunt. Deze SDK is verbonden met zowel Azure-tabelopslag als met de tabel-API van Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): deze Azure Storage SDK biedt de mogelijkheid om verbinding te maken met Azure Cosmos DB accounts met behulp van de Table-API.

* [Node. js SDK](table-sdk-nodejs.md): deze Azure Storage SDK biedt de mogelijkheid om verbinding te maken met Azure Cosmos DB accounts met behulp van de Table-API.


Aanvullende informatie over het werken met de tabel-API is beschikbaar in het artikel [Veelgestelde vragen: Ontwikkelen met de tabel-API](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Ontwikkelen met Azure-tabelopslag

Azure-tabelopslag beschikt over deze SDK’s voor ontwikkelen:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Met deze bibliotheek kunt u werken met de opslagtabelservice.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). De Azure Cosmos DB Table SDK voor python ondersteunt de Table Storage-service (omdat de Table-API van Azure Table Storage en Cosmos DB dezelfde functies en functionaliteit delen, en in een poging om onze ontwikkel inspanningen voor de SDK te factorize, wordt aangeraden deze SDK te gebruiken).
- [Azure Storage SDK voor Java](https://github.com/azure/azure-storage-java). Deze Azure Storage-SDK biedt een clientbibliotheek in Java voor het gebruik van Azure-tabelopslag.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Deze SDK biedt een Node.js-pakket en een JavaScript-clientbibliotheek die compatibel is met de browser, voor het gebruik van de opslagtabelservice.
- [AzureRmStorageTable PowerShell-module](https://www.powershellgallery.com/packages/AzureRmStorageTable). Deze PowerShell-module bevat cmdlets voor het werken met opslagtabellen.
- [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/). Met deze bibliotheek kunt u toepassingen bouwen in Azure Storage.
- [Clientbibliotheek voor Azure-opslagtabellen voor Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Dit project biedt een Ruby-pakket waarmee u eenvoudig toegang kunt krijgen tot Azure-opslagtabelservices.
- [PHP-clientbibliotheek voor Azure-opslagtabellen](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Dit project biedt een PHP-clientbibliotheek waarmee u eenvoudig toegang kunt krijgen tot Azure-opslagtabelservices.


   





