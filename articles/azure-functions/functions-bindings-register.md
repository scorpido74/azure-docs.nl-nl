---
title: Azure Functions bindings uitbreidingen registreren
description: Meer informatie over het registreren van een Azure Functions bindings uitbreiding op basis van uw omgeving.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 43bc278ea3cbd14690f1a9ac9263872536b5b174
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224778"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions bindings uitbreidingen registreren

Met ingang van Azure Functions versie 2. x zijn [bindingen](./functions-triggers-bindings.md) beschikbaar als afzonderlijke pakketten vanuit de functions-runtime. Terwijl .NET functions toegang tot bindingen via NuGet-pakketten maakt, bieden uitbreidings bundels een andere functie toegang tot alle bindingen via een configuratie-instelling.

Houd rekening met de volgende punten met betrekking tot bindings extensies:

- Bindings uitbreidingen worden niet expliciet geregistreerd in functions 1. x, behalve wanneer [u een C#-klassebibliotheek maakt met Visual Studio](#local-csharp).

- HTTP-en timer-triggers worden standaard ondersteund en vereisen geen uitbrei ding.

De volgende tabel geeft aan wanneer en hoe u bindingen registreert.

| Ontwikkelomgeving |Registratie<br/> in functions 1. x  |Registratie<br/> in functies 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisch|Automatisch<sup>*</sup>|
|Ontwikkeling van Non-.NET-talen of lokale Azure core-Hulpprogram Ma's|Automatisch|[Azure Functions Core Tools-en uitbreidings bundels gebruiken](#extension-bundles)|
|C# Class-bibliotheek met Visual Studio|[NuGet-hulpprogram ma's gebruiken](#vs)|[NuGet-hulpprogram ma's gebruiken](#vs)|
|C#-klassen bibliotheek met Visual Studio code|N.v.t.|[.NET Core SLI gebruiken](#vs-code)|

<sup>*</sup> Portal maakt gebruik van uitbreidings bundels.

## <a name="extension-bundles"></a><a name="extension-bundles"></a>Uitbreidings bundels

Met uitbreidings bundels kunt u een compatibele set van functies bindings extensies toevoegen aan uw functie-app. Wanneer u bundels gebruikt, wordt er een vooraf gedefinieerde set extensies toegevoegd wanneer u uw app bouwt. Extensie pakketten die in een bundel zijn gedefinieerd, worden geverifieerd om compatibel te zijn met elkaar, waarmee u conflicten tussen pakketten kunt voor komen. Met uitbreidings bundels kunt u voor komen dat u .NET-project code publiceert met een project met non-.NET-functies. U schakelt uitbreidings bundels in de host.jsvan de app in.  

U kunt uitbreidings bundels gebruiken met versie 2. x en latere versies van de functions-runtime. 

Gebruik uitbreidings bundels voor lokale ontwikkeling met behulp van Azure Functions Core Tools, Visual Studio code en wanneer u op afstand bouwt. Zorg ervoor dat u de nieuwste versie van [Azure functions core tools](functions-run-local.md#v2)gebruikt bij het ontwikkelen van lokale. Uitbreidings bundels worden ook gebruikt bij het ontwikkelen van functies in de Azure Portal. 

Als u geen uitbreidings bundels gebruikt, moet u de .NET Core 2. x SDK installeren op uw lokale computer voordat u de [bindings uitbreidingen expliciet installeert](#explicitly-install-extensions). Een uitbrei ding. csproj-bestand, waarmee expliciet de vereiste extensies worden gedefinieerd, wordt toegevoegd aan uw project. Met uitbreidings bundels worden deze vereisten voor lokale ontwikkeling verwijderd. 

Als u uitbreidings bundels wilt gebruiken, moet u de *host.jsin* het bestand bijwerken met de volgende vermelding voor `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>Expliciete extensies installeren

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>NuGet-pakketten

Voor een project op basis van C#-klassen bibliotheek moet u de extensies rechtstreeks installeren. Uitbreidings bundels zijn speciaal ontworpen voor projecten die geen C#-klassen bibliotheek zijn.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> C \# Class-bibliotheek met Visual Studio

In **Visual Studio**kunt u pakketten installeren via de Package Manager-console met behulp van de opdracht [installeren-pakket](/nuget/tools/ps-ref-install-package) , zoals wordt weer gegeven in het volgende voor beeld:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

De naam van het pakket dat voor een bepaalde binding wordt gebruikt, is opgenomen in het referentie artikel voor die binding. Zie de [sectie pakketten in het artikel service bus binding referentie](functions-bindings-service-bus.md#functions-1x)voor een voor beeld.

Vervang `<TARGET_VERSION>` in het voor beeld door een specifieke versie van het pakket, zoals `3.0.0-beta5` . Geldige versies worden weer gegeven op de afzonderlijke pakket pagina's op [NuGet.org](https://nuget.org). De primaire versies die overeenkomen met functions runtime 1. x of 2. x worden opgegeven in het referentie artikel voor de binding.

Als u gebruikt `Install-Package` om te verwijzen naar een binding, hoeft u geen [uitbreidings bundels](#extension-bundles)te gebruiken. Deze benadering is specifiek voor klassen bibliotheken die zijn gemaakt in Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> C#-klassen bibliotheek met Visual Studio code

In **Visual Studio code**installeert u pakketten voor een C# Class Library-project vanaf de opdracht prompt met behulp van de [DotNet add package](/dotnet/core/tools/dotnet-add-package) opdracht in de .net core SLI. In het volgende voor beeld ziet u hoe u een binding toevoegt:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

De .NET Core SLI kan alleen worden gebruikt voor de ontwikkeling van Azure Functions 2. x.

Vervang door `<BINDING_TYPE_NAME>` de naam van het pakket dat de binding bevat die u nodig hebt. U kunt het artikel over de gewenste bindings verwijzing vinden in de [lijst met ondersteunde bindingen](./functions-triggers-bindings.md#supported-bindings).

Vervang `<TARGET_VERSION>` in het voor beeld door een specifieke versie van het pakket, zoals `3.0.0-beta5` . Geldige versies worden weer gegeven op de afzonderlijke pakket pagina's op [NuGet.org](https://nuget.org). De primaire versies die overeenkomen met functions runtime 1. x of 2. x worden opgegeven in het referentie artikel voor de binding.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure function-trigger en binding-voor beeld](./functions-bindings-example.md)
