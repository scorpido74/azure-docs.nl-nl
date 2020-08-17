---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 6fd8c3c5839d4cc897caa2dff70af87980e547eb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206746"
---
## <a name="local-settings-file"></a>Lokale instellingsbestand

In het bestand local.settings.json worden app-instellingen, verbindingsreeksen en instellingen opgeslagen die door lokale ontwikkelprogramma's worden gebruikt. Instellingen in het bestand local.settings.json worden alleen gebruikt als u projecten lokaal uitvoert. Het bestand met lokale instelling heeft de volgende structuur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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
| **`IsEncrypted`** | Wanneer deze instelling is ingesteld op `true`, worden alle waarden versleuteld met een lokale machinesleutel. Gebruikt met `func settings`-opdrachten. De standaardwaarde is `false`. |
| **`Values`** | Matrix van toepassingsinstellingen en verbindingsreeksen die worden gebruikt wanneer een project lokaal wordt uitgevoerd. De sleutelwaardeparen (tekenreeks-tekenreeks) corresponderen met toepassingsinstellingen in uw functie-app in Azure, zoals [`AzureWebJobsStorage`]. Veel triggers en bindingen hebben een eigenschap die verwijst naar een app-instelling voor verbindingsreeksen, bijvoorbeeld `Connection` voor [Blob-opslagtrigger](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Voor deze eigenschappen hebt u een toepassingsinstelling nodig die is gedefinieerd in de matrix `Values`. <br/>[`AzureWebJobsStorage`] is een vereiste app-instelling voor andere triggers dan HTTP. <br/>Voor versie 2.x en hoger van de Functions-runtime is de instelling [`FUNCTIONS_WORKER_RUNTIME`] vereist, die wordt gegenereerd voor uw project door Core Tools. <br/> Wanneer u de [Azure-opslagemulator](../articles/storage/common/storage-use-emulator.md) lokaal hebt geïnstalleerd en u [`AzureWebJobsStorage`] hebt ingesteld op `UseDevelopmentStorage=true`, gebruikt Core Tools de emulator. De emulator is nuttig tijdens ontwikkeling, maar u moet testen met een echte opslagverbinding voor de implementatie.<br/> Waarden moeten tekenreeksen zijn en geen JSON-objecten of matrices. Instellingsnamen kunnen geen dubbele punt (`:`) bevatten of een dubbel onderstrepingsteken (`__`). Deze tekens zijn gereserveerd door de runtime. <br/>Als u een functie wilt uitschakelen wanneer u lokaal iets uitvoert, voegt u `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` toe aan de verzameling, waar `<FUNCTION_NAME>` de naam van de functie is. Raadpleeg voor meer informatie [Functies uitschakelen in Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson)  |
| **`Host`** | Met de instellingen in dit gedeelte wordt het Functions-hostproces aangepast wanneer u projecten lokaal uitvoert. Deze instellingen staan los van de host.json-instellingen, die ook van toepassing zijn wanneer u projecten uitvoert in Azure. |
| **`LocalHttpPort`** | Hiermee stelt u de standaardpoort in die wordt gebruikt wanneer de lokale Functions-host wordt uitgevoerd (`func host start` en `func run`). De opdrachtregeloptie `--port` heeft voorrang op deze instelling. |
| **`CORS`** | Definieert de oorsprong die is toegestaan voor [CORS (Cross-origin-resource delen)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Oorsprongen worden aangeleverd als een door komma's gescheiden lijst zonder spaties. De jokertekenwaarde (\*) wordt ondersteund, waarmee verzoeken van elke oorsprong toegestaan zijn. |
| **`CORSCredentials`** |  Als deze instelling is ingesteld op `true`, zijn `withCredentials`-verzoeken toegestaan. |
| **`ConnectionStrings`** | Een verzameling. Gebruik deze verzameling niet voor de verbindingsreeksen die worden gebruikt door uw functiebindingen. Deze verzameling wordt alleen gebruikt door frameworks die doorgaans een verbindingsreeks krijgen van het gedeelte `ConnectionStrings` van een configuratiebestand, bijvoorbeeld [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Verbindingsreeksen in dit object zijn toegevoegd aan de omgeving met het providertype van [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Items in deze verzameling worden niet gepubliceerd naar Azure met andere app-instellingen. U moet deze waarden expliciet toevoegen aan de verzameling `Connection strings` van de instellingen van uw functie-app. Als u een [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) maakt in uw functiecode, moet u de verbindingsreekswaarde bewaren met uw andere verbindingen in **Toepassingsinstellingen** in de portal. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
