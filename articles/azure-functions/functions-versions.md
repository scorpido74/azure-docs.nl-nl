---
title: Overzicht van Azure Functions runtime versies
description: Azure Functions ondersteunt meerdere versies van de runtime. Leer over de verschillen tussen deze versies en hoe de juiste te kiezen.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226539"
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

Vanaf versie 2. x gebruikt de runtime een taal uitbreid model en alle functies in een functie-app moeten dezelfde taal delen. De taal van functies in een functie-app wordt gekozen tijdens het maken van de app en wordt beheerd in de [functies\_werk nemer\_runtime](functions-app-settings.md#functions_worker_runtime) -instelling. 

Azure Functions 1. x experimentele talen kunnen het nieuwe model niet gebruiken, zodat ze niet worden ondersteund in 2. x. De volgende tabel geeft aan welke programmeertalen momenteel worden ondersteund in elke runtimeversie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Zie voor meer informatie [Ondersteunde talen](supported-languages.md).

## <a name="creating-1x-apps"></a>Uitvoeren op een specifieke versie

Standaard worden functie-apps die zijn gemaakt in de Azure Portal en door de Azure CLI ingesteld op versie 2. x. Als dat mogelijk is, moet u deze runtime versie gebruiken. Als dat het geval is, kunt u nog steeds een functie-app uitvoeren op de runtime van versie 1. x. U kunt alleen de runtime-versie wijzigen na het maken van een functie-app wanneer u nog geen functies hebt toegevoegd. Zie [de huidige runtime versie weer geven en bijwerken](set-runtime-version.md#view-and-update-the-current-runtime-version)voor meer informatie over het vastmaken van de runtime versie aan 1. x.

U kunt ook een upgrade uitvoeren naar versie 3. x van de runtime, die in preview is. Doe dit als u uw functies moet kunnen uitvoeren op .NET Core 3. x. Zie [de huidige runtime versie weer geven en bijwerken](set-runtime-version.md#view-and-update-the-current-runtime-version)voor meer informatie over het uitvoeren van een upgrade naar 3. x.

## <a name="migrating-from-1x-to-later-versions"></a>Migreren van 1. x naar latere versies

U kunt ervoor kiezen om een bestaande app die gebruikmaakt van de versie 1.x-runtime te migreren naar gebruik van versie 2.x. De meeste wijzigingen die u moet aanbrengen hebben betrekking op wijzigingen in de taal, zoals C# API-wijzigingen tussen .NET Framework 4.7 en .NET Core 2. U moet er ook voor zorgen dat uw code en bibliotheken compatibel zijn met de taal die u kiest. Tot slot moet u letten op eventuele wijzigingen in de trigger, bindingen en functies die hieronder beschreven staan. Voor de beste resultaten bij een migratie maakt u een nieuwe functie-app voor versie 2.x en poort uw bestaande versie 1.x-functie code naar de nieuwe app.  

### <a name="changes-in-triggers-and-bindings"></a>Wijzigingen in triggers en bindingen

Versie 2.x vereist dat u de extensies voor specifieke triggers en bindingen die worden gebruikt door de functies in uw app installeert. De enige uitzondering hierop zijn HTTP- en timer-triggers, die geen extensie vereisen.  Zie [bindings uitbreidingen registreren en installeren](./functions-bindings-register.md)voor meer informatie.

Er zijn ook enkele wijzigingen in de `function.json` of kenmerken van de functie tussen versies. Zo is de eigenschap Event hub `path` nu `eventHubName`. Zie de [bestaande bindings tabel](#bindings) voor koppelingen naar documentatie voor elke binding.

### <a name="changes-in-features-and-functionality"></a>Wijzigingen in functies en functionaliteit

Een aantal functies is ook verwijderd, bijgewerkt of vervangen in de nieuwe versie. Deze sectie bevat de wijzigingen die u in versie 2.x ziet na versie 1.x te hebben gebruikt.

In versie 2.x zijn de volgende wijzigingen  aangebracht:

* Sleutels voor het aanroepen van HTTP-eind punten worden altijd versleuteld opgeslagen in Azure Blob-opslag. In versie 1.x werden sleutels standaard opgeslagen in Azure File opslag. Bij het upgraden van een app vanaf versie 1.x naar versie 2.x worden bestaande geheimen in de bestandsopslag opnieuw ingesteld.

* De runtime versie 2.x bevat geen ingebouwde ondersteuning voor webhook-providers. Deze wijziging is doorgevoerd om de prestaties te verbeteren. U kunt HTTP-triggers blijven gebruiken als eind punten voor webhooks.

* Het configuratie bestand voor de host (host. json) moet leeg zijn of de teken reeks `"version": "2.0"`hebben.

* Ter verbetering van de bewaking wordt het webjobs-dash board in de portal, dat gebruikmaakt van de instelling [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) , vervangen door Azure-toepassing Insights, dat de instelling [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) gebruikt. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie.

* Alle functies in een functie-app moeten dezelfde taal delen. Wanneer u een functie-app maakt, moet u een runtimestack voor de app kiezen. De runtime stack wordt opgegeven door de [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) waarde in toepassings instellingen. Deze vereiste is toegevoegd om de footprint en de opstarttijd te verbeteren. Wanneer u lokaal ontwikkelt, moet u deze instelling ook in het [bestand local. settings. json](functions-run-local.md#local-settings-file)toevoegen.

* De standaardtime-out voor functies in een App Service-plan is gewijzigd naar 30 minuten. U kunt de time-out hand matig wijzigen in onbeperkt door gebruik te maken van de instelling [functionTimeout](functions-host-json.md#functiontimeout) in host. json.

* HTTP-concurrency-vertragingen zijn standaard ingesteld voor functies in een verbruiksplan, met een standaardwaarde van 100 gelijktijdige aanvragen per instantie. U kunt dit wijzigen in de instelling [`maxConcurrentRequests`](functions-host-json.md#http) in het bestand host. json.

* Vanwege [.net core-beperkingen](https://github.com/Azure/azure-functions-host/issues/3414)is de ondersteuning F# voor script-functies (. FSX) verwijderd. Gecompileerde F#-functies (.fs) worden nog steeds ondersteund.

* De URL-indeling van de webhooks van Event Grid trigger is gewijzigd in `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Een lokaal ontwikkelde toepassing migreren

U hebt mogelijk bestaande projecten voor apps die lokaal zijn ontwikkeld voor gebruik van de versie 1.x runtime. Om te upgraden naar versie 2.x maakt u een lokale functie-app-project op basis van versie 2.x en poort uw bestaande code naar de nieuwe app. U kunt handmatig het bestaande project en de code bijwerken als een soort 'in-place' upgrade. Er is echter een aantal andere verbeteringen tussen versie 1.x en versie 2.x die u mogelijk nog steeds moet aanbrengen. In C# het object fout opsporing is bijvoorbeeld gewijzigd van `TraceWriter` naar `ILogger`. Als u een nieuw versie 2.x-project maakt, begint u met bijgewerkte functies op basis van de meest recente versie 2.x-sjablonen.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio runtime-versies

In Visual Studio selecteert u de runtime versie wanneer u een project maakt. Azure Functions-hulpprogramma's voor Visual Studio bieden ondersteuning voor beide grote runtime-versies. De juiste versie wordt gebruikt voor foutopsporing en publicatie op basis van de instellingen voor het project. De versie-instellingen worden gedefinieerd in het `.csproj`-bestand in de volgende eigenschappen:

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

Wanneer u fouten opspoort of uw project publiceert wordt de juiste versie van de runtime gebruikt.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS code en Azure Functions Core Tools

[Azure functions core tools](functions-run-local.md) wordt gebruikt voor de ontwikkeling van de opdracht regel en ook door de [Azure functions extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code. Installeer versie 2.x van de Core-hulpprogramma's om te ontwikkelen voor versie 2.x. Voor de ontwikkeling van versie 1. x is versie 1. x van de kern Hulpprogramma's vereist. Zie [install the Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools)(Engelstalig) voor meer informatie.

Voor Visual Studio code ontwikkeling moet u mogelijk ook de gebruikers instelling voor de `azureFunctions.projectRuntime` bijwerken zodat deze overeenkomt met de versie van de geïnstalleerde hulpprogram ma's.  Deze instelling werkt ook de sjablonen en talen bij die worden gebruikt tijdens het maken van de functie-app.

### <a name="changing-version-of-apps-in-azure"></a>De versie van de apps in azure wijzigen

De versie van de functions-runtime die wordt gebruikt door gepubliceerde apps in azure, wordt bepaald door de [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) toepassings instelling. Een waarde van `~2` streeft naar versie 2. x runtime en `~1` streeft naar de versie 1. x runtime. Deze instelling kunt u niet willekeurig  wijzigen omdat er waarschijnlijk andere wijzigingen voor app-instelling en wijzigingen van de code in uw functies vereist zijn. Zie [runtime-versies van Azure functions instellen](set-runtime-version.md)voor meer informatie over de aanbevolen manier om uw functie-app te migreren naar een andere runtime versie.

## <a name="bindings"></a>Bindingen

Vanaf versie 2. x maakt de runtime gebruik van een nieuw [model voor bindings uitbreid baarheid](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) dat de volgende voor delen biedt:

* Ondersteuning voor bindinguitbreidingen van derden.

* Ontkoppeling van de runtime en bindingen. Door deze wijziging kunnen bindinguitbreidingen onafhankelijk van elkaar worden samengesteld en vrijgegeven. U kunt bijvoorbeeld kiezen voor een upgrade naar een versie van een uitbrei ding die afhankelijk is van een nieuwere versie van een onderliggende SDK.

* Een lichtere uitvoeringsomgeving, waarbij alleen de bindingen die in gebruik zijn door de runtime bekend worden en geladen.

Met uitzondering van de HTTP- en timer-triggers, moeten alle bindingen expliciet worden toegevoegd aan het functie-app-project en geregistreerd in de portal. Zie [bindings uitbreidingen registreren](./functions-bindings-expressions-patterns.md)voor meer informatie.

In de volgende tabel ziet u welke bindingen worden ondersteund in elke runtimeversie.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Azure-functies lokaal programmeren en testen](functions-run-local.md)
* [Azure Functions runtime-versies instellen](set-runtime-version.md)
* [Releaseopmerkingen](https://github.com/Azure/azure-functions-host/releases)
