---
title: Overzicht van runtime-versies van Azure Functions
description: Azure Functions ondersteunt meerdere versies van de runtime. Leer de verschillen tussen hen en hoe u degene kiest die bij u geschikt is.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: e90752e89be7e381b06f8a87f76f123f0e4a8e3a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422481"
---
# <a name="azure-functions-runtime-versions-overview"></a>Overzicht van runtime-versies van Azure Functions

De belangrijkste versies van de runtime van Azure-functies zijn gerelateerd aan de versie van .NET waarop de runtime is gebaseerd. In de volgende tabel wordt de huidige versie van de runtime, het releaseniveau en de bijbehorende .NET-versie aangegeven. 

| Runtime-versie | Releaseniveau<sup>1</sup> | .NET-versie | 
| --------------- | ------------- | ------------ |
| 3.x | Algemene beschikbaarheid | .NET Core 3.1 | 
| 2.x | Algemene beschikbaarheid | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4.7.2<sup>3</sup> |

<sup>1</sup> GA-releases worden ondersteund voor productiescenario's.   
<sup>2</sup> Versie 1.x is in onderhoudsmodus. Verbeteringen worden alleen in latere versies geleverd.   
<sup>3</sup> Ondersteunt alleen de ontwikkeling in de Azure-portal of lokaal op Windows-computers.

In dit artikel worden enkele verschillen beschreven tussen de verschillende versies, hoe u elke versie maken en hoe u versies wijzigen.

## <a name="languages"></a>Talen

Vanaf versie 2.x gebruikt de runtime een taaluitbreidbaarheidsmodel en moeten alle functies in een functie-app dezelfde taal delen. De taal van functies in een functie-app wordt gekozen bij het maken van de app en wordt gehandhaafd in de [runtime-instelling voor functies.\_\_](functions-app-settings.md#functions_worker_runtime) 

Azure Functions 1.x experimentele talen kunnen het nieuwe model niet gebruiken, dus ze worden niet ondersteund in 2.x. In de volgende tabel wordt aangegeven welke programmeertalen momenteel in elke runtime-versie worden ondersteund.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Zie voor meer informatie [Ondersteunde talen](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Uitvoeren op een specifieke versie

Functie-apps die zijn gemaakt in de Azure-portal en door de Azure CLI zijn standaard ingesteld op versie 3.x. U deze versie indien nodig wijzigen. U de runtime-versie alleen wijzigen in 1.x nadat u uw functie-app hebt gemaakt, maar voordat u functies toevoegt.  Het verplaatsen tussen 2.x en 3.x is toegestaan, zelfs met apps die functies hebben, maar het is nog steeds aanbevolen om eerst in een nieuwe app te testen.

## <a name="migrating-from-1x-to-later-versions"></a>Migreren van 1.x naar latere versies

U ervoor kiezen om een bestaande app te migreren die is geschreven om de versie 1.x runtime te gebruiken om in plaats daarvan een nieuwere versie te gebruiken. De meeste wijzigingen die u moet aanbrengen, zijn gerelateerd aan wijzigingen in de taalruntime, zoals C#API-wijzigingen tussen .NET Framework 4.7 en .NET Core. U moet er ook voor zorgen dat uw code en bibliotheken compatibel zijn met de taalruntime die u kiest. Tot slot, zorg ervoor dat u eventuele wijzigingen in trigger, bindingen en functies hieronder markeren. Voor de beste migratieresultaten moet u een nieuwe functie-app in een nieuwe versie maken en uw bestaande versie 1.x-functiecode naar de nieuwe app porten.  

Hoewel het mogelijk is om een "in-place" upgrade uit te voeren door de app-configuratie handmatig bij te werken, bevat het gaan van 1.x naar een hogere versie enkele baanbrekende wijzigingen. In C#wordt het foutopsporingsobject bijvoorbeeld gewijzigd `TraceWriter` `ILogger`van . Door een nieuw versie 3.x-project te maken, begint u met bijgewerkte functies op basis van de nieuwste versie 3.x-sjablonen.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Wijzigingen in triggers en bindingen na versie 1.x

Vanaf versie 2.x moet u de extensies installeren voor specifieke triggers en bindingen die worden gebruikt door de functies in uw app. De enige uitzondering voor deze HTTP- en timertriggers, waarvoor geen extensie nodig is.  Zie [Bindende extensies registreren en installeren](./functions-bindings-register.md)voor meer informatie.

Er zijn ook een paar wijzigingen in de *functie.json* of kenmerken van de functie tussen versies. De eigenschap Event `path` Hub is `eventHubName`nu bijvoorbeeld . Zie de [bestaande bindingstabel](#bindings) voor koppelingen naar documentatie voor elke binding.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Wijzigingen in functies en functionaliteit na versie 1.x

Een paar functies zijn verwijderd, bijgewerkt of vervangen na versie 1.x. In deze sectie worden de wijzigingen beschreven die u in latere versies ziet nadat u versie 1.x hebt gebruikt.

In versie 2.x zijn de volgende wijzigingen aangebracht:

* Sleutels voor het aanroepen van HTTP-eindpunten worden altijd versleuteld opgeslagen in Azure Blob-opslag. In versie 1.x zijn sleutels standaard opgeslagen in Azure File-opslag. Wanneer u een app upgradet van versie 1.x naar versie 2.x, worden bestaande geheimen die zich in bestandsopslag bevinden, opnieuw ingesteld.

* De versie 2.x runtime bevat geen ingebouwde ondersteuning voor webhook-providers. Deze wijziging is aangebracht om de prestaties te verbeteren. U http-triggers nog steeds gebruiken als eindpunten voor webhooks.

* Het hostconfiguratiebestand (host.json) moet leeg `"version": "2.0"`zijn of de tekenreeks hebben.

* Om de controle te verbeteren, wordt het [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) WebJobs-dashboard in de portal, [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) die de instelling heeft gebruikt, vervangen door Azure Application Insights, dat de instelling gebruikt. Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie .

* Alle functies in een functie-app moeten dezelfde taal delen. Wanneer u een functie-app maakt, moet u een runtime-stack voor de app kiezen. De runtime stack wordt [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) opgegeven door de waarde in toepassingsinstellingen. Deze vereiste is toegevoegd om de voetafdruk en opstarttijd te verbeteren. Wanneer u lokaal ontwikkelt, moet u deze instelling ook opnemen in het [bestand local.settings.json](functions-run-local.md#local-settings-file).

* De standaardtime-out voor functies in een App Service-abonnement wordt gewijzigd in 30 minuten. U de time-out handmatig terugzetten naar onbeperkt met behulp van de instelling [functionTime-out](functions-host-json.md#functiontimeout) in host.json.

* HTTP-gelijktijdigheidsthrottles worden standaard geïmplementeerd voor functies van het verbruiksplan, met een standaardvan 100 gelijktijdige aanvragen per instantie. U dit [`maxConcurrentRequests`](functions-host-json.md#http) wijzigen in de instelling in het bestand host.json.

* Vanwege [.NET Core-beperkingen](https://github.com/Azure/azure-functions-host/issues/3414)is ondersteuning voor F#script (.fsx)-functies verwijderd. Gecompileerde F#-functies (.fs) worden nog steeds ondersteund.

* De URL-indeling van gebeurtenisrastertriggerwebhooks `https://{app}/runtime/webhooks/{triggerName}`is gewijzigd in .

## <a name="migrating-from-2x-to-3x"></a>Migreren van 2.x naar 3.x

Azure Functions versie 3.x is zeer backwards compatibel met versie 2.x.  Veel apps moeten veilig kunnen upgraden naar 3.x zonder wijzigingen in de code.  Terwijl de overstap naar 3.x wordt aangemoedigd, moet u uitgebreide tests uitvoeren voordat u de belangrijkste versie in productie-apps wijzigt.

### <a name="breaking-changes-between-2x-and-3x"></a>Wijzigingen tussen 2.x en 3.x verbreken

Hieronder volgen de wijzigingen waar u rekening mee moet houden voordat u een 2.x-app upgradet naar 3.x.

#### <a name="javascript"></a>Javascript

* Uitvoerbindingen die `context.done` via- of retourwaarden zijn `context.bindings`toegewezen, gedragen zich nu hetzelfde als instellen in .

* Timer trigger object is camelCase in plaats van PascalCase

* Gebeurtenishub geactiveerde `dataType` functies met binair ontvangen een array van `binary` in plaats van `string`.

* De HTTP-aanvraag payload is niet `context.bindingData.req`meer toegankelijk via .  Het kan nog steeds worden geopend `context.req`als `context.bindings`een invoerparameter, en in .

* Node.js 8 wordt niet meer ondersteund en wordt niet uitgevoerd in 3.x-functies.

#### <a name="net"></a>.NET

* [Synchrone serverbewerkingen zijn standaard uitgeschakeld](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Versie van apps wijzigen in Azure

De versie van de runtime van functies die [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) wordt gebruikt door gepubliceerde apps in Azure, wordt bepaald door de toepassingsinstelling. De volgende belangrijke runtime-versiewaarden worden ondersteund:

| Waarde | Runtime-doel |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Wijzig deze instelling niet willekeurig, omdat andere app-instellingwijzigingen en wijzigingen in uw functiecode mogelijk vereist zijn.

### <a name="locally-developed-application-versions"></a>Lokaal ontwikkelde toepassingsversies

U de volgende updates uitvoeren om apps te gebruiken om de beoogde versies lokaal te wijzigen.

#### <a name="visual-studio-runtime-versions"></a>Runtime-versies van Visual Studio

In Visual Studio selecteert u de runtime-versie wanneer u een project maakt. Azure Functions-hulpprogramma's voor Visual Studio ondersteunen de drie belangrijkste runtime-versies. De juiste versie wordt gebruikt bij het debuggen en publiceren op basis van projectinstellingen. De versie-instellingen worden `.csproj` in het bestand in de volgende eigenschappen gedefinieerd:

##### <a name="version-1x"></a>Versie 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versie 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versie 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x en `Microsoft.NET.Sdk.Functions` .NET vereist `3.0.0`dat de extensie ten minste is .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>2.x-apps bijwerken naar 3.x in Visual Studio

U een bestaande functie targeting 2.x openen en naar `.csproj` 3.x gaan door het bestand te bewerken en de bovenstaande waarden bij te werken.  Visual Studio beheert runtime-versies automatisch voor u op basis van projectmetagegevens.  Het is echter mogelijk als u nog nooit een 3.x-app hebt gemaakt voordat Visual Studio de sjablonen en runtime voor 3.x op uw machine heeft.  Dit kan zich voordoen met een fout als 'geen functies runtime beschikbaar die overeenkomt met de versie opgegeven in het project.'  Als u de nieuwste sjablonen en runtime wilt ophalen, gaat u door de ervaring om een nieuw functieproject te maken.  Wacht tot Visual Studio de nieuwste sjablonen heeft opgehaald wanneer u bij het scherm versie en sjabloonselecteren bent.  Zodra de nieuwste .NET Core 3-sjablonen beschikbaar zijn en worden weergegeven, moet u elk project dat is geconfigureerd voor versie 3.x kunnen uitvoeren en debuggen.

> [!IMPORTANT]
> Versie 3.x-functies kunnen alleen in Visual Studio worden ontwikkeld als u Visual Studio-versie 16.4 of nieuwer gebruikt.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS-code en Azure-functies Core-hulpprogramma's

[Azure Functions Core Tools](functions-run-local.md) wordt gebruikt voor de ontwikkeling van opdrachtregelen en ook door de [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code. Installeer versie 3.x van de Core Tools om zich te ontwikkelen tegen versie 3.x. Versie 2.x ontwikkeling vereist versie 2.x van de Core Tools, enzovoort. Zie [De Core-hulpprogramma's voor Azure Functions installeren](functions-run-local.md#install-the-azure-functions-core-tools)voor meer informatie.

Voor de ontwikkeling van Visual Studio Code moet u `azureFunctions.projectRuntime` mogelijk ook de gebruikersinstelling bijwerken voor de versie van de geïnstalleerde hulpprogramma's.  Met deze instelling worden ook de sjablonen en talen bijgewerkt die worden gebruikt tijdens het maken van functie-apps.  Als u `~3` apps wilt `azureFunctions.projectRuntime` maken in `~3`u, wordt de gebruikersinstelling bijgewerkt naar .

![Runtime-instelling azure-functies-extensie](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven en Java apps

U Java-apps migreren van versie 2.x naar 3.x door [de 3.x-versie van de kernhulpprogramma's](functions-run-local.md#install-the-azure-functions-core-tools) te installeren die lokaal moeten worden uitgevoerd.  Nadat u hebt gecontroleerd of uw app correct werkt bij versie 3.x, werkt u het bestand van `POM.xml` de app bij om de `FUNCTIONS_EXTENSION_VERSION` instelling te wijzigen in, `~3`zoals in het volgende voorbeeld:

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

Vanaf versie 2.x maakt de runtime gebruik van een nieuw [bindend extensibiliteitsmodel](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) dat de volgende voordelen biedt:

* Ondersteuning voor bindende verlengingen van derden.

* Ontkoppeling van runtime en bindingen. Met deze wijziging kunnen bindende extensies onafhankelijk worden geversioneerd en vrijgegeven. U er bijvoorbeeld voor kiezen om te upgraden naar een versie van een extensie die is gebaseerd op een nieuwere versie van een onderliggende SDK.

* Een lichtere uitvoeringsomgeving, waarbij alleen de gebruikte bindingen bekend zijn en geladen zijn door de runtime.

Met uitzondering van HTTP- en timertriggers moeten alle bindingen expliciet worden toegevoegd aan het functie-app-project of worden geregistreerd in de portal. Zie [Bindende extensies registreren](./functions-bindings-expressions-patterns.md)voor meer informatie .

In de volgende tabel ziet u welke bindingen worden ondersteund in elke runtime-versie.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Azure-functies lokaal programmeren en testen](functions-run-local.md)
* [Azure-versies voor runtime targeten](set-runtime-version.md)
* [Releaseopmerkingen](https://github.com/Azure/azure-functions-host/releases)
