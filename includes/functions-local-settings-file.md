---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205672"
---
## <a name="local-settings-file"></a>Lokale instellingsbestand

De bestanden instellingen voor de lokale instellingen.settings.settings.json-bestanden worden gebruikt door lokale ontwikkeltools. Instellingen in het bestand local.settings.json worden alleen gebruikt wanneer u projecten lokaal uitvoert. Het bestand met lokale instellingen heeft deze structuur:

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
| **`IsEncrypted`** | Wanneer deze instelling `true`is ingesteld op , worden alle waarden versleuteld met een lokale machinesleutel. Gebruikt `func settings` met commando's. Standaardwaarde `false`is . |
| **`Values`** | Array met toepassingsinstellingen en verbindingstekenreeksen die worden gebruikt wanneer een project lokaal wordt uitgevoerd. Deze tekenreeksparen (key-value) komen overeen met toepassingsinstellingen in [`AzureWebJobsStorage`]uw functie-app in Azure, zoals . Veel triggers en bindingen hebben een eigenschap die verwijst `Connection` naar een verbindingstekenreeks-app-instelling, zoals voor de [Blob-opslagtrigger](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Voor deze eigenschappen hebt u een toepassingsinstelling nodig die in de `Values` array is gedefinieerd. <br/>[`AzureWebJobsStorage`]is een vereiste app-instelling voor andere triggers dan HTTP. <br/>Versie 2.x en hoger van de`FUNCTIONS_WORKER_RUNTIME`runtime van functies vereist de instelling [ ] die voor uw project wordt gegenereerd door Core Tools. <br/> Wanneer u de [Azure-opslagemulator](../articles/storage/common/storage-use-emulator.md) lokaal [`AzureWebJobsStorage`] hebt `UseDevelopmentStorage=true`geïnstalleerd en u bent ingesteld op , gebruikt Core Tools de emulator. De emulator is handig tijdens de ontwikkeling, maar u moet testen met een werkelijke opslagverbinding voordat u wordt geïmplementeerd.<br/> Waarden moeten tekenreeksen zijn en geen JSON-objecten of -arrays. Het instellen van namen kan`:`geen dubbele punt`__`( ) of een dubbele onderstreping ( ) bevatten. Deze tekens worden gereserveerd door de runtime.  |
| **`Host`** | Instellingen in deze sectie passen het hostproces functies aan wanneer u projecten lokaal uitvoert. Deze instellingen staan los van de instellingen host.json, die ook van toepassing zijn wanneer u projecten uitvoert in Azure. |
| **`LocalHttpPort`** | Hiermee stelt u de standaardpoort in`func host start` `func run`die wordt gebruikt bij het uitvoeren van de lokale host functies (en ). De `--port` opdrachtregeloptie heeft voorrang op deze instelling. |
| **`CORS`** | Hiermee bepaalt u de oorsprong die is toegestaan voor [het delen van resource (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)van oorsprong. Oorsprong wordt geleverd als een door komma's gescheiden lijst zonder spaties. De wildcardwaarde\*( ) wordt ondersteund, waardoor aanvragen van elke oorsprong mogelijk zijn. |
| **`CORSCredentials`** |  Wanneer ingesteld `true`op `withCredentials` , kan aanvragen. |
| **`ConnectionStrings`** | Een collectie. Gebruik deze verzameling niet voor de verbindingstekenreeksen die door uw functiebindingen worden gebruikt. Deze verzameling wordt alleen gebruikt door frameworks die `ConnectionStrings` doorgaans verbindingstekenreeksen krijgen uit de sectie van een configuratiebestand, zoals [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Verbindingstekenreeksen in dit object worden toegevoegd aan de omgeving met het providertype [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Items in deze verzameling worden niet gepubliceerd in Azure met andere app-instellingen. U moet deze waarden expliciet `Connection strings` toevoegen aan het verzamelen van de instellingen van uw functie-app. Als u een [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in uw functiecode maakt, moet u de waarde van de verbindingstekenreeks opslaan met uw andere verbindingen in **Toepassingsinstellingen** in de portal. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
