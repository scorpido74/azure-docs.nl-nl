---
title: Overzicht van Azure Functions runtime versies
description: Azure Functions ondersteunt meerdere versies van de runtime. Meer informatie over de verschillen tussen deze en hoe u het kunt kiezen dat het beste bij u past.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: glenga
ms.openlocfilehash: 9ca7006bb842cbe235d2e982e611613e1fd74ed9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597382"
---
# <a name="azure-functions-runtime-versions-overview"></a>Overzicht van Azure Functions runtime versies

De belangrijkste versies van de Azure Functions runtime zijn gerelateerd aan de versie van .NET waarop de runtime is gebaseerd. De volgende tabel geeft de huidige versie van de runtime, het release niveau en de gerelateerde .NET-versie aan. 

| Runtime versie | Release niveau<sup>1</sup> | .NET-versie | 
| --------------- | ------------- | ------------ |
| controleert  | preview | .NET Core 3. x | 
| 2.x | Algemene beschikbaarheid | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> GA releases worden ondersteund voor productie scenario's.   
<sup>2</sup> Versie 1. x bevindt zich in de onderhouds modus. Uitbrei dingen worden alleen opgenomen in latere versies.   
<sup>3</sup> Ondersteunt alleen de ontwikkeling van de Azure Portal of lokaal op Windows-computers.

>[!NOTE]  
> Versie 3. x van de functions runtime is in Preview en wordt niet ondersteund voor productie omgevingen. Zie [deze aankondiging](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm)voor meer informatie over het uitproberen van versie 3. x.

In dit artikel worden enkele van de verschillen tussen de verschillende versies beschreven, hoe u elke versie kunt maken en hoe u versies wijzigt.

## <a name="languages"></a>Talen

Vanaf versie 2. x gebruikt de runtime een taal uitbreid model en alle functies in een functie-app moeten dezelfde taal delen. De taal van de functies in een functie-app wordt gekozen tijdens het maken van de app en wordt onderhouden in de [functies \_WORKER \_RUNTIME](functions-app-settings.md#functions_worker_runtime) instelling. 

Azure Functions 1. x experimentele talen kunnen het nieuwe model niet gebruiken, zodat ze niet worden ondersteund in 2. x. De volgende tabel geeft aan welke programmeer talen momenteel worden ondersteund in elke runtime versie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Zie voor meer informatie [Ondersteunde talen](supported-languages.md).

## <a name="creating-1x-apps"></a>Uitvoeren op een specifieke versie

Standaard worden functie-apps die zijn gemaakt in de Azure Portal en door de Azure CLI ingesteld op versie 2. x. Als dat mogelijk is, moet u deze runtime versie gebruiken. Als dat het geval is, kunt u nog steeds een functie-app uitvoeren op de runtime van versie 1. x. U kunt de runtime versie alleen wijzigen nadat u de functie-app hebt gemaakt, maar voordat u functies toevoegt. Zie [de huidige runtime versie weer geven en bijwerken](set-runtime-version.md#view-and-update-the-current-runtime-version)voor meer informatie over het vastmaken van de runtime versie aan 1. x.

U kunt ook een upgrade uitvoeren naar versie 3. x van de runtime, die in preview is. Doe dit als u uw functies moet kunnen uitvoeren op .NET Core 3. x. Zie [de huidige runtime versie weer geven en bijwerken](set-runtime-version.md#view-and-update-the-current-runtime-version)voor meer informatie over het uitvoeren van een upgrade naar 3. x.

## <a name="migrating-from-1x-to-later-versions"></a>Migreren van 1. x naar latere versies

U kunt ervoor kiezen om een bestaande app te migreren die is geschreven voor het gebruik van versie 1. x runtime om in plaats daarvan versie 2. x te gebruiken. De meeste wijzigingen die u moet aanbrengen, zijn gerelateerd aan wijzigingen in de taal runtime, zoals C# API-wijzigingen tussen .NET Framework 4,7 en .net Core 2. U moet er ook voor zorgen dat uw code en bibliotheken compatibel zijn met de taal-runtime die u kiest. Zorg er ten slotte voor dat u wijzigingen aanbrengt in de trigger, bindingen en functies die hieronder zijn gemarkeerd. Voor de beste migratie resultaten moet u een nieuwe functie-app maken voor versie 2. x en een poort van uw bestaande functie code van versie 1. x naar de nieuwe app.  

### <a name="changes-in-triggers-and-bindings"></a>Wijzigingen in triggers en bindingen

Versie 2. x vereist dat u de uitbrei dingen installeert voor specifieke triggers en bindingen die worden gebruikt door de functies in uw app. De enige uitzonde ring voor deze HTTP-en timer-triggers, waarvoor geen extensie is vereist.  Zie [bindings uitbreidingen registreren en installeren](./functions-bindings-register.md)voor meer informatie.

Er zijn ook enkele wijzigingen in de `function.json` of kenmerken van de functie tussen versies. Zo is de eigenschap Event hub `path` nu `eventHubName`. Zie de [bestaande bindings tabel](#bindings) voor koppelingen naar documentatie voor elke binding.

### <a name="changes-in-features-and-functionality"></a>Wijzigingen in functies en functionaliteit

Een aantal functies die ook zijn verwijderd, bijgewerkt of vervangen in de nieuwe versie. In deze sectie vindt u informatie over de wijzigingen die u ziet in versie 2. x nadat u versie 1. x hebt gebruikt.

In versie 2. x zijn de volgende wijzigingen aangebracht:

* Sleutels voor het aanroepen van HTTP-eind punten worden altijd versleuteld opgeslagen in Azure Blob-opslag. In versie 1. x zijn sleutels opgeslagen in azure File Storage standaard. Bij de upgrade van een app van versie 1. x naar versie 2. x worden bestaande geheimen in bestands opslag opnieuw ingesteld.

* De runtime van versie 2. x bevat geen ingebouwde ondersteuning voor webhook-providers. Deze wijziging is doorgevoerd om de prestaties te verbeteren. U kunt HTTP-triggers blijven gebruiken als eind punten voor webhooks.

* Het configuratie bestand voor de host (host. json) moet leeg zijn of de teken reeks `"version": "2.0"` hebben.

* Ter verbetering van de bewaking wordt het webjobs-dash board in de portal, dat gebruikmaakt van de instelling [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) , vervangen door Azure-toepassing Insights, dat de instelling [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) gebruikt. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie.

* Alle functies in een functie-app moeten dezelfde taal delen. Wanneer u een functie-app maakt, moet u een runtime stack kiezen voor de app. De runtime stack wordt opgegeven door de [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) waarde in toepassings instellingen. Deze vereiste is toegevoegd ter verbetering van de footprint en de opstart tijd. Wanneer u lokaal ontwikkelt, moet u deze instelling ook in het [bestand local. settings. json](functions-run-local.md#local-settings-file)toevoegen.

* De standaardtime-out voor functies in een App Service-abonnement wordt gewijzigd in 30 minuten. U kunt de time-out hand matig wijzigen in onbeperkt door gebruik te maken van de instelling [functionTimeout](functions-host-json.md#functiontimeout) in host. json.

* HTTP-gelijktijdigheids vertragingen worden standaard geïmplementeerd voor verbruiks plan functies, met een standaard waarde van 100 gelijktijdige aanvragen per instantie. U kunt dit wijzigen in de instelling [`maxConcurrentRequests`](functions-host-json.md#http) in het bestand host. json.

* Vanwege [.net core-beperkingen](https://github.com/Azure/azure-functions-host/issues/3414)is de ondersteuning F# voor script-functies (. FSX) verwijderd. Gecompileerde F# functies (. FS) worden nog steeds ondersteund.

* De URL-indeling van de webhooks van Event Grid trigger is gewijzigd in `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Een lokaal ontwikkelde toepassing migreren

Mogelijk hebt u bestaande functie-app-projecten die u lokaal hebt ontwikkeld met behulp van versie 1. x runtime. Als u een upgrade wilt uitvoeren naar versie 2. x, moet u een project voor een lokale functie-app maken met versie 2. x en de poort van uw bestaande code in de nieuwe app. U kunt het bestaande project en de code, een sortering van de ' in-place ' upgrade, hand matig bijwerken. Er zijn echter een aantal andere verbeteringen tussen versie 1. x en versie 2. x die u mogelijk nog steeds moet maken. In C# het object fout opsporing is bijvoorbeeld gewijzigd van `TraceWriter` naar `ILogger`. Door een nieuw versie 2. x-project te maken, start u met de bijgewerkte functies op basis van de meest recente versie 2. x-sjablonen.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio runtime-versies

In Visual Studio selecteert u de runtime versie wanneer u een project maakt. Azure Functions-hulpprogram ma's voor Visual Studio ondersteunt zowel grote runtime versies. De juiste versie wordt gebruikt bij het opsporen van fouten en het publiceren op basis van project instellingen. De versie-instellingen worden gedefinieerd in het `.csproj`-bestand in de volgende eigenschappen:

##### <a name="version-1x"></a>Versie 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versie 2. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Wanneer u fouten opspoort of uw project publiceert, wordt de juiste versie van de runtime gebruikt.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS code en Azure Functions Core Tools

[Azure functions core tools](functions-run-local.md) wordt gebruikt voor de ontwikkeling van de opdracht regel en ook door de [Azure functions extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code. Voor het ontwikkelen van versie 2. x installeert u versie 2. x van de kern Hulpprogramma's. Voor de ontwikkeling van versie 1. x is versie 1. x van de kern Hulpprogramma's vereist. Zie [install the Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools)(Engelstalig) voor meer informatie.

Voor Visual Studio code ontwikkeling moet u mogelijk ook de gebruikers instelling voor de `azureFunctions.projectRuntime` bijwerken zodat deze overeenkomt met de versie van de geïnstalleerde hulpprogram ma's.  Met deze instelling worden ook de sjablonen en talen bijgewerkt die worden gebruikt tijdens het maken van de functie-app.

### <a name="changing-version-of-apps-in-azure"></a>De versie van de apps in azure wijzigen

De versie van de functions-runtime die wordt gebruikt door gepubliceerde apps in azure, wordt bepaald door de [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) toepassings instelling. Een waarde van `~2` streeft naar versie 2. x runtime en `~1` streeft naar de versie 1. x runtime. U kunt deze instelling niet wille keurig wijzigen, omdat andere app-instellingen en code wijzigingen in uw functies waarschijnlijk vereist zijn. Zie [runtime-versies van Azure functions instellen](set-runtime-version.md)voor meer informatie over de aanbevolen manier om uw functie-app te migreren naar een andere runtime versie.

## <a name="bindings"></a>Bindingen

Vanaf versie 2. x maakt de runtime gebruik van een nieuw [model voor bindings uitbreid baarheid](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) dat de volgende voor delen biedt:

* Ondersteuning voor bindings uitbreidingen van derden.

* Ontkoppeling van runtime en bindingen. Met deze wijziging kunnen bindings uitbreidingen worden geversied en onafhankelijk worden vrijgegeven. U kunt bijvoorbeeld kiezen voor een upgrade naar een versie van een uitbrei ding die afhankelijk is van een nieuwere versie van een onderliggende SDK.

* Een lichtere uitvoerings omgeving waarbij alleen de bindingen die in gebruik zijn, bekend zijn en worden geladen door de runtime.

Met uitzonde ring van HTTP-en timer triggers, moeten alle bindingen expliciet worden toegevoegd aan het functie-app-project of zijn geregistreerd in de portal. Zie [bindings uitbreidingen registreren](./functions-bindings-expressions-patterns.md)voor meer informatie.

In de volgende tabel ziet u welke bindingen worden ondersteund in elke runtime versie.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Azure-functies lokaal programmeren en testen](functions-run-local.md)
* [Azure Functions runtime-versies instellen](set-runtime-version.md)
* [Releaseopmerkingen](https://github.com/Azure/azure-functions-host/releases)
