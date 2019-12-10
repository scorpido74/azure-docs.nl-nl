---
title: Overzicht van Azure Functions runtime versies
description: Azure Functions ondersteunt meerdere versies van de runtime. Leer over de verschillen tussen deze versies en hoe de juiste te kiezen.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 874d2e657c2c9d7cba7874ff9815c61f9bbe8ef7
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941695"
---
# <a name="azure-functions-runtime-versions-overview"></a>Overzicht van Azure Functions runtime versies

De belangrijkste versies van de Azure Functions runtime zijn gerelateerd aan de versie van .NET waarop de runtime is gebaseerd. De volgende tabel geeft de huidige versie van de runtime, het release niveau en de gerelateerde .NET-versie aan. 

| Runtime versie | Release niveau<sup>1</sup> | .NET-versie | 
| --------------- | ------------- | ------------ |
| controleert | Algemene beschikbaarheid | .NET Core 3,1 | 
| 2.x | Algemene beschikbaarheid | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> ga-releases worden ondersteund voor productie scenario's.   
<sup>2</sup> versie 1. x bevindt zich in de onderhouds modus. Uitbrei dingen worden alleen opgenomen in latere versies.   
<sup>3</sup> ondersteunt alleen de ontwikkeling van de Azure portal of lokaal op Windows-computers.

In dit artikel worden enkele van de verschillen tussen de verschillende versies beschreven, hoe u elke versie kunt maken en hoe u versies wijzigt.

## <a name="languages"></a>Talen

Vanaf versie 2. x gebruikt de runtime een taal uitbreid model en alle functies in een functie-app moeten dezelfde taal delen. De taal van functies in een functie-app wordt gekozen tijdens het maken van de app en wordt beheerd in de [functies\_werk nemer\_runtime](functions-app-settings.md#functions_worker_runtime) -instelling. 

Azure Functions 1. x experimentele talen kunnen het nieuwe model niet gebruiken, zodat ze niet worden ondersteund in 2. x. De volgende tabel geeft aan welke programmeertalen momenteel worden ondersteund in elke runtimeversie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Zie voor meer informatie [Ondersteunde talen](supported-languages.md).

## <a name="creating-1x-apps"></a>Uitvoeren op een specifieke versie

Standaard worden functie-apps die zijn gemaakt in de Azure Portal en door de Azure CLI ingesteld op versie 2. x. U kunt deze versie naar wens wijzigen. U kunt de runtime versie alleen wijzigen in 1. x nadat u de functie-app hebt gemaakt, maar voordat u functies toevoegt.  Verplaatsen tussen 2. x en 3. x is toegestaan, zelfs bij apps met functies, maar het wordt nog steeds aanbevolen om eerst te testen in een nieuwe app.

## <a name="migrating-from-1x-to-later-versions"></a>Migreren van 1. x naar latere versies

U kunt ervoor kiezen om een bestaande app te migreren die is geschreven voor het gebruik van versie 1. x runtime om in plaats daarvan een nieuwere versie te gebruiken. De meeste wijzigingen die u moet aanbrengen, zijn gerelateerd aan wijzigingen in de taal runtime, zoals C# API-wijzigingen tussen .NET Framework 4,7 en .net core. U moet er ook voor zorgen dat uw code en bibliotheken compatibel zijn met de taal die u kiest. Tot slot moet u letten op eventuele wijzigingen in de trigger, bindingen en functies die hieronder beschreven staan. Voor de beste migratie resultaten moet u een nieuwe functie-app maken in een nieuwe versie en de functie code van uw bestaande versie 1. x naar de nieuwe app poort.  

Hoewel het mogelijk is om een ' in-place ' upgrade uit te voeren door de app-configuratie hand matig bij te werken, van 1. x naar een hogere versie bevat een aantal belang rijke wijzigingen. In C#is het object voor fout opsporing bijvoorbeeld gewijzigd van `TraceWriter` naar `ILogger`. Door een nieuw versie 3. x-project te maken, start u met de bijgewerkte functies op basis van de meest recente versie 3. x-sjablonen.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Wijzigingen in triggers en bindingen na versie 1. x

Vanaf versie 2. x moet u de uitbrei dingen voor specifieke triggers en bindingen installeren die worden gebruikt door de functies in uw app. De enige uitzondering hierop zijn HTTP- en timer-triggers, die geen extensie vereisen.  Zie [bindings uitbreidingen registreren en installeren](./functions-bindings-register.md)voor meer informatie.

Er zijn ook enkele wijzigingen in de *functie. json* of kenmerken van de functie tussen versies. Zo is de eigenschap Event hub `path` nu `eventHubName`. Zie de [bestaande bindings tabel](#bindings) voor koppelingen naar documentatie voor elke binding.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Wijzigingen in functies en functionaliteit na versie 1. x

Er zijn enkele functies verwijderd, bijgewerkt of vervangen na versie 1. x. In deze sectie vindt u meer informatie over de wijzigingen die u in latere versies ziet nadat u versie 1. x hebt gebruikt.

In versie 2.x zijn de volgende wijzigingen  aangebracht:

* Sleutels voor het aanroepen van HTTP-eind punten worden altijd versleuteld opgeslagen in Azure Blob-opslag. In versie 1.x werden sleutels standaard opgeslagen in Azure File opslag. Bij het upgraden van een app vanaf versie 1.x naar versie 2.x worden bestaande geheimen in de bestandsopslag opnieuw ingesteld.

* De runtime versie 2.x bevat geen ingebouwde ondersteuning voor webhook-providers. Deze wijziging is doorgevoerd om de prestaties te verbeteren. U kunt HTTP-triggers blijven gebruiken als eind punten voor webhooks.

* Het configuratiebestand van de host (host.json) moet leeg zijn of de tekenreeks `"version": "2.0"` bevatten.

* Voor het verbeteren van bewaking wordt het WebJobs-dashboard in de portal dat gebruik maakte van de instelling [ `AzureWebJobsDashboard` ](functions-app-settings.md#azurewebjobsdashboard) vervangen door Azure Application Insights, dat gebruikmaakt van de [ `APPINSIGHTS_INSTRUMENTATIONKEY` ](functions-app-settings.md#appinsights_instrumentationkey) instelling. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie.

* Alle functies in een functie-app moeten dezelfde taal delen. Wanneer u een functie-app maakt, moet u een runtimestack voor de app kiezen. De runtimestack wordt opgegeven door de [ `FUNCTIONS_WORKER_RUNTIME` ](functions-app-settings.md#functions_worker_runtime) waarde in de toepassingsinstellingen. Deze vereiste is toegevoegd om de footprint en de opstarttijd te verbeteren. Wanneer u lokaal ontwikkelt moet u ook deze instelling in het [bestand local.settings.json](functions-run-local.md#local-settings-file) opnemen.

* De standaardtime-out voor functies in een App Service-plan is gewijzigd naar 30 minuten. U kunt handmatig de time-out op onbeperkt instellen met behulp van de [functionTimeout](functions-host-json.md#functiontimeout) in host.json.

* HTTP-concurrency-vertragingen zijn standaard ingesteld voor functies in een verbruiksplan, met een standaardwaarde van 100 gelijktijdige aanvragen per instantie. U kunt dit wijzigen via de [ `maxConcurrentRequests` ](functions-host-json.md#http) instelling in het bestand host.json.

* Vanwege [.NET core beperkingen](https://github.com/Azure/azure-functions-host/issues/3414) is ondersteuning voor functies in F#-script (.fsx) verwijderd. Gecompileerde F#-functies (.fs) worden nog steeds ondersteund.

* De URL-indeling van de webhooks van Event Grid trigger is gewijzigd in `https://{app}/runtime/webhooks/{triggerName}`.

## <a name="migrating-from-2x-to-3x"></a>Migreren van 2. x naar 3. x

Azure Functions versie 3. x is een uiterst achterwaarts compatibel met versie 2. x.  Veel apps moeten veilig kunnen worden bijgewerkt naar 3. x zonder code wijzigingen.  Bij het overstappen op 3. x wordt aanbevolen uitgebreide tests uit te voeren voordat u de primaire versie wijzigt in productie-apps.

### <a name="breaking-changes-between-2x-and-3x"></a>De wijzigingen tussen 2. x en 3. x verbreken

Hieronder vindt u de wijzigingen die u moet kennen voordat u een upgrade uitvoert van een 2. x-app naar 3. x.

#### <a name="javascript"></a>Javascript

* Uitvoer bindingen die via `context.done` of retour waarden zijn toegewezen, gedragen zich nu hetzelfde als de instelling in `context.bindings`.

* Het trigger object voor de timer is camelCase in plaats van PascalCase

* Door Event hub geactiveerde functies met `dataType` binair wordt een matrix van `binary` ontvangen in plaats van `string`.

* De nettolading van de HTTP-aanvraag is niet langer toegankelijk via `context.bindingData.req`.  Het kan nog steeds worden geopend als een invoer parameter, `context.req`, en in `context.bindings`.

* Node. js 8 wordt niet meer ondersteund en kan niet worden uitgevoerd in 3. x-functies.

#### <a name="net"></a>.NET

* [Synchrone Server bewerkingen zijn standaard uitgeschakeld](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>De versie van de apps in azure wijzigen

De versie van de functions-runtime die wordt gebruikt door gepubliceerde apps in azure, wordt bepaald door de [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) toepassings instelling. De volgende belang rijke runtime-versie waarden worden ondersteund:

| Waarde | Runtime-doel |
| ------ | -------- |
| `~3` | controleert |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> U kunt deze instelling niet wille keurig wijzigen, omdat wijzigingen in de instellingen van de app en wijzigingen in uw functie code mogelijk vereist zijn.

### <a name="locally-developed-application-versions"></a>Lokaal ontwikkelde toepassings versies

U kunt de volgende updates functie-apps lokaal wijzigen doel versies.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio runtime-versies

In Visual Studio selecteert u de runtime versie wanneer u een project maakt. Azure Functions-hulpprogramma's voor Visual Studio bieden ondersteuning voor beide grote runtime-versies. De juiste versie wordt gebruikt voor foutopsporing en publicatie op basis van de instellingen voor het project. De versie-instellingen worden gedefinieerd in het `.csproj` bestand in de volgende eigenschappen:

##### <a name="version-1x"></a>Versie 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versie 2. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versie 3. x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Voor Azure Functions 3. x en .NET moet de `Microsoft.Sdk.NET.Functions` extensie ten minste `3.0.0`zijn.

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>2\. x-apps bijwerken naar 3. x in Visual Studio

U kunt een bestaande functie die is gericht op 2. x, openen en naar 3. x verplaatsen door het `.csproj` bestand te bewerken en de bovenstaande waarden bij te werken.  Visual Studio beheert runtime versies automatisch voor u op basis van meta gegevens van het project.  Het is echter mogelijk dat u nooit een 3. x-app hebt gemaakt voordat Visual Studio nog niet beschikt over de sjablonen en runtime voor 3. x op de computer.  Dit kan zichzelf opleveren met een fout zoals ' er is geen functions runtime beschikbaar die overeenkomt met de versie die is opgegeven in het project. '  Als u de meest recente sjablonen en runtime wilt ophalen, gaat u door de ervaring met het maken van een nieuw functie project.  Wanneer u het scherm versie en sjabloon selecteren selecteert, wacht u tot Visual Studio de meest recente sjablonen heeft opgehaald.  Zodra de laatste .NET Core 3-sjablonen beschikbaar zijn en worden weer gegeven, moet u alle projecten die zijn geconfigureerd voor versie 3. x, kunnen uitvoeren en fouten opsporen.

> [!IMPORTANT]
> De functies van versie 3. x kunnen alleen worden ontwikkeld in Visual Studio als u versie 16,4 of hoger gebruikt.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS code en Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) wordt gebruikt voor het ontwikkelen vanaf de opdrachtregel en ook door de [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code. Als u wilt ontwikkelen met versie 3. x, installeert u versie 3. x van de kern Hulpprogramma's. Voor de ontwikkeling van versie 2. x is versie 2. x van de kern Hulpprogramma's vereist, enzovoort. Zie [install the Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools)(Engelstalig) voor meer informatie.

Voor ontwikkeling in Visual Studio Code moet u mogelijk ook de gebruikersinstelling voor de `azureFunctions.projectRuntime` bijwerken zodat deze overeenkomt met de versie van de hulpprogramma's die zijn geïnstalleerd.  Deze instelling werkt ook de sjablonen en talen bij die worden gebruikt tijdens het maken van de functie-app.  Als u apps wilt maken in `~3` kunt u de instelling van de `azureFunctions.projectRuntime` gebruiker bijwerken naar `~3`.

![Runtime-instelling Azure Functions extensie](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven-en Java-apps

U kunt java-apps van versie 2. x naar 3. x migreren door [de 3. x-versie van de kern hulpprogramma's te installeren](functions-run-local.md#install-the-azure-functions-core-tools) die nodig zijn om lokaal uit te voeren.  Nadat u hebt gecontroleerd of uw app lokaal wordt uitgevoerd op versie 3. x, werkt u het `POM.xml`-bestand van de app bij om de `FUNCTIONS_EXTENSION_VERSION`-instelling te wijzigen in `~3`, zoals in het volgende voor beeld:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

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
