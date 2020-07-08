---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205672"
---
## <a name="local-settings-file"></a>Lokale instellingsbestand

Met de local.settings.jsvoor de bestanden worden de app-instellingen, verbindings reeksen en instellingen die worden gebruikt door de lokale ontwikkelingsprogram ma's opgeslagen. Instellingen in het bestand local.settings.jsworden alleen gebruikt wanneer u projecten lokaal uitvoert. Het lokale instellingen bestand heeft deze structuur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Deze instellingen worden ondersteund wanneer u projecten lokaal uitvoert:

| Instelling      | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Als deze instelling is ingesteld op `true` , worden alle waarden versleuteld met een lokale computer sleutel. Wordt gebruikt met `func settings` opdrachten. De standaard waarde is `false` . |
| **`Values`** | Matrix van toepassings instellingen en verbindings reeksen die worden gebruikt wanneer een project lokaal wordt uitgevoerd. Deze combi natie van sleutel waarden (string-string) komt overeen met de toepassings instellingen in uw functie-app in azure, zoals [`AzureWebJobsStorage`] . Veel triggers en bindingen hebben een eigenschap die verwijst naar een connection string app-instelling, zoals `Connection` voor de [Blob Storage-trigger](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Voor deze eigenschappen hebt u een toepassings instelling nodig die in de `Values` matrix is gedefinieerd. <br/>[`AzureWebJobsStorage`]is een vereiste app-instelling voor andere triggers dan HTTP. <br/>Voor versie 2. x en hoger van de functions runtime is de `FUNCTIONS_WORKER_RUNTIME` instelling [] vereist, die voor uw project wordt gegenereerd door kern hulpprogramma's. <br/> Wanneer u de [Azure Storage-emulator](../articles/storage/common/storage-use-emulator.md) lokaal hebt geïnstalleerd en hebt ingesteld [`AzureWebJobsStorage`] op `UseDevelopmentStorage=true` , maakt de kern hulpprogramma's gebruik van de emulator. De emulator is handig tijdens de ontwikkeling, maar u moet eerst een echte opslag verbinding testen voordat u de implementatie kunt uitvoeren.<br/> Waarden moeten teken reeksen en geen JSON-objecten of matrices zijn. Namen van instellingen mogen geen dubbele punt ( `:` ) of een dubbel onderstrepen ( `__` ) bevatten. Deze tekens zijn gereserveerd door de runtime.  |
| **`Host`** | Met de instellingen in deze sectie wordt het hostproces voor functies aangepast wanneer u projecten lokaal uitvoert. Deze instellingen zijn gescheiden van de host.jsvoor instellingen, die ook van toepassing zijn wanneer u projecten in azure uitvoert. |
| **`LocalHttpPort`** | Hiermee stelt u de standaard poort in die wordt gebruikt voor het uitvoeren van de lokale functions-host ( `func host start` en `func run` ). De `--port` opdracht regel optie heeft voor rang op deze instelling. |
| **`CORS`** | Hiermee worden de oorsprong gedefinieerd die zijn toegestaan voor het [delen van cross-Origin-resources (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Oorsprongen worden geleverd als een door komma's gescheiden lijst zonder spaties. De waarde van het Joker teken ( \* ) wordt ondersteund, waardoor aanvragen van elke oorsprong worden toegestaan. |
| **`CORSCredentials`** |  Als deze is ingesteld op `true` , worden `withCredentials` aanvragen toegestaan. |
| **`ConnectionStrings`** | Een verzameling. Gebruik deze verzameling niet voor de verbindings reeksen die worden gebruikt door uw functie bindingen. Deze verzameling wordt alleen gebruikt door frameworks die meestal verbindings reeksen ophalen uit de `ConnectionStrings` sectie van een configuratie bestand, zoals [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Verbindings reeksen in dit object worden toegevoegd aan de omgeving met het provider type [System. data. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Items in deze verzameling worden niet naar Azure gepubliceerd met andere app-instellingen. U moet deze waarden expliciet toevoegen aan de `Connection strings` verzameling van de instellingen van de functie-app. Als u een [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in uw functie code maakt, moet u de Connection String waarde opslaan met uw andere verbindingen in **Toepassings instellingen** in de portal. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
