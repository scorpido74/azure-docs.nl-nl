---
title: Azure Functions bindings uitbreidingen registreren
description: Meer informatie over het registreren van een Azure Functions bindings uitbreiding op basis van uw omgeving.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086470"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions bindings uitbreidingen registreren

In Azure Functions versie 2. x zijn [bindingen](./functions-triggers-bindings.md) beschikbaar als afzonderlijke pakketten vanuit de functions-runtime. Terwijl .NET functions toegang tot bindingen via NuGet-pakketten maakt, bieden uitbreidings bundels een andere functie toegang tot alle bindingen via een configuratie-instelling.

Houd rekening met de volgende punten met betrekking tot bindings extensies:

- Bindings uitbreidingen worden niet expliciet geregistreerd in functions 1. x, behalve wanneer [u een C# klassen bibliotheek maakt met Visual Studio](#local-csharp).

- HTTP-en timer-triggers worden standaard ondersteund en vereisen geen uitbrei ding.

De volgende tabel geeft aan wanneer en hoe u bindingen registreert.

| Ontwikkelomgeving |Registratie<br/> in functions 1. x  |Registratie<br/> in functies 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisch|Automatisch|
|Ontwikkeling van Non-.NET-talen of lokale Azure core-Hulpprogram Ma's|Automatisch|[Azure Functions Core Tools-en uitbreidings bundels gebruiken](#extension-bundles)|
|C#klassen bibliotheek met Visual Studio|[NuGet-hulpprogram ma's gebruiken](#vs)|[NuGet-hulpprogram ma's gebruiken](#vs)|
|C#klassen bibliotheek met Visual Studio code|N/A|[.NET Core SLI gebruiken](#vs-code)|

## <a name="extension-bundles"></a>Uitbreidings bundels voor lokale ontwikkeling

Uitbreidings bundels is een lokale ontwikkel technologie voor de runtime van versie 2. x waarmee u een compatibele set functies binding extensies kunt toevoegen aan uw functie-app-project. Deze extensie pakketten worden vervolgens opgenomen in het implementatie pakket wanneer u implementeert in Azure. Bundels maakt het mogelijk om alle bindingen die door micro soft zijn gepubliceerd, beschikbaar te maken via een instelling in het bestand *host. json* . Extensie pakketten die in een bundel zijn gedefinieerd, zijn compatibel met elkaar, waarmee u conflicten tussen pakketten kunt voor komen. Zorg ervoor dat u de nieuwste versie van [Azure functions core tools](functions-run-local.md#v2)gebruikt bij het ontwikkelen van lokale.

Gebruik uitbreidings bundels voor alle lokale ontwikkel aars met behulp van Azure Functions Core Tools of Visual Studio code.

Als u geen uitbreidings bundels gebruikt, moet u de .NET Core 2. x SDK installeren op uw lokale computer voordat u de binding-extensies installeert. Bundels verwijdert deze vereiste voor lokale ontwikkeling. 

Als u uitbreidings bundels wilt gebruiken, moet u het bestand *host. json* bijwerken zodat het `extensionBundle`de volgende vermelding bevat voor:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

De volgende eigenschappen zijn beschikbaar in `extensionBundle`:

| Eigenschap | Description |
| -------- | ----------- |
| **`id`** | De naam ruimte voor de uitbreidings bundels van Microsoft Azure-functies. |
| **`version`** | De versie van de bundel die moet worden geïnstalleerd. De functions-runtime kiest altijd de Maxi maal toegestane versie die is gedefinieerd door het versie bereik of-interval. Met de bovenstaande versie waarde kunnen alle bundel versies van 1.0.0 tot maar niet inclusief 2.0.0. Zie de [interval notatie voor het opgeven van versie reeksen](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)voor meer informatie. |

Bundel versies worden als pakketten in de bundel wijziging verhoogd. De belangrijkste versie wijzigingen treden op wanneer pakketten in de bundel worden verhoogd met een primaire versie, die meestal samen vallen met een wijziging in de hoofd versie van de functions-runtime.  

De huidige set met uitbrei dingen die zijn geïnstalleerd door de standaard bundel, wordt opgesomd in deze [extensies. JSON-bestand](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="vs"></a>C\# Class-bibliotheek met Visual Studio

In **Visual Studio**kunt u pakketten installeren via de Package Manager-console met behulp van de opdracht [installeren-pakket](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , zoals wordt weer gegeven in het volgende voor beeld:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

De naam van het pakket dat voor een bepaalde binding wordt gebruikt, is opgenomen in het referentie artikel voor die binding. Zie de [sectie pakketten in het artikel service bus binding referentie](functions-bindings-service-bus.md#packages---functions-1x)voor een voor beeld.

Vervang `<TARGET_VERSION>` in het voor beeld door een specifieke versie van het pakket, `3.0.0-beta5`zoals. Geldige versies worden weer gegeven op de afzonderlijke pakket pagina's op [NuGet.org](https://nuget.org). De primaire versies die overeenkomen met functions runtime 1. x of 2. x worden opgegeven in het referentie artikel voor de binding.

Als u gebruikt `Install-Package` om te verwijzen naar een binding, hoeft u geen [uitbreidings bundels](#extension-bundles)te gebruiken. Deze benadering is specifiek voor klassen bibliotheken die zijn gemaakt in Visual Studio.

## <a name="vs-code"></a>C# klassen bibliotheek met Visual Studio code

> [!NOTE]
> U kunt het beste [uitbreidings bundels](#extension-bundles) gebruiken om functies automatisch een compatibele set met bindings extensie pakketten te laten installeren.

In **Visual Studio code**installeert u pakketten voor een C# Class Library-project vanaf de opdracht prompt met behulp van de [DotNet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) opdracht in de .net core SLI. In het volgende voor beeld ziet u hoe u een binding toevoegt:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

De .NET Core SLI kan alleen worden gebruikt voor de ontwikkeling van Azure Functions 2. x.

Vervang `<BINDING_TYPE_NAME>` door de naam van het pakket dat is opgenomen in het referentie artikel voor uw gewenste binding. U kunt het artikel over de gewenste bindings verwijzing vinden in de [lijst met ondersteunde bindingen](./functions-triggers-bindings.md#supported-bindings).

Vervang `<TARGET_VERSION>` in het voor beeld door een specifieke versie van het pakket, `3.0.0-beta5`zoals. Geldige versies worden weer gegeven op de afzonderlijke pakket pagina's op [NuGet.org](https://nuget.org). De primaire versies die overeenkomen met functions runtime 1. x of 2. x worden opgegeven in het referentie artikel voor de binding.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure function-trigger en binding-voor beeld](./functions-bindings-example.md)
