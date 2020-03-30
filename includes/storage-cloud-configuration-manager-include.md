---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038167"
---
De [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) biedt een klasse voor het parseren van een verbindingsreeks uit een configuratiebestand. De klasse [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) onteert configuratie-instellingen. Het ontziet instellingen voor clienttoepassingen die worden uitgevoerd op het bureaublad, op een mobiel apparaat, in een virtuele Azure-machine of in een Azure-cloudservice.

Voeg als `CloudConfigurationManager` referentie naar `using` het pakket de volgende richtlijnen toe:

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

Het gebruik van Azure Configuration Manager is optioneel. U ook een API gebruiken, zoals de [klasse ConfigurationManager van](/dotnet/api/system.configuration.configurationmanager).NET Framework.
