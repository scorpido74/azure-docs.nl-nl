---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038167"
---
De [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) biedt een klasse voor het parseren van een verbindingsreeks uit een configuratiebestand. De klasse [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) parseert configuratie-instellingen. Het parseert instellingen voor client toepassingen die worden uitgevoerd op het bureau blad, op een mobiel apparaat, op een virtuele machine van Azure of in een Azure-Cloud service.

Als u wilt verwijzen naar het `CloudConfigurationManager`-pakket, voegt u de volgende `using`-instructies toe:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

In het volgende voorbeeld ziet u hoe u een verbindingsreeks ophaalt uit een configuratiebestand:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Het gebruik van Azure Configuration Manager is optioneel. U kunt ook een API gebruiken, zoals de ConfigurationManager- [klasse](/dotnet/api/system.configuration.configurationmanager)van de .NET Framework.
