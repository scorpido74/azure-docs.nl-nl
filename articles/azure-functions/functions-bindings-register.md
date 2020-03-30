---
title: Azure Functions-bindingsextensies registreren
description: Leer een bindingsextensie Azure Functions registreren op basis van uw omgeving.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277516"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions-bindingsextensies registreren

In Azure Functions versie 2.x zijn [bindingen](./functions-triggers-bindings.md) beschikbaar als afzonderlijke pakketten van de runtime van de functies. Terwijl .NET toegang tot bindingen via NuGet-pakketten heeft, bieden extensiebundels andere functies toegang tot alle bindingen via een configuratie-instelling.

Houd rekening met de volgende items met betrekking tot bindende extensies:

- Bindende extensies worden niet expliciet geregistreerd in Functies 1.x, behalve bij [het maken van een C#-klassenbibliotheek met Visual Studio.](#local-csharp)

- HTTP- en timertriggers worden standaard ondersteund en vereisen geen extensie.

In de volgende tabel wordt aangegeven wanneer en hoe u bindingen registreert.

| Ontwikkelomgeving |Registratie<br/> in functies 1.x  |Registratie<br/> in functies 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisch|Automatisch|
|Non-.NET talen of lokale Azure Core Tools-ontwikkeling|Automatisch|[Azure Functions Core Tools en extensiebundels gebruiken](#extension-bundles)|
|C#-klassenbibliotheek met Visual Studio|[NuGet-gereedschappen gebruiken](#vs)|[NuGet-gereedschappen gebruiken](#vs)|
|C#-klassenbibliotheek met Visual Studio Code|N.v.t.|[Gebruik .NET Core CLI](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Uitbreidingsbundels voor lokale ontwikkeling

Extensiebundels is een implementatietechnologie waarmee u een compatibele set extensies voor het binden van functies toevoegen aan uw functie-app. Er wordt een vooraf gedefinieerde set extensies toegevoegd wanneer u uw app bouwt. Uitbreidingspakketten die in een bundel zijn gedefinieerd, zijn compatibel met elkaar, waardoor u conflicten tussen pakketten voorkomen. U schakelt extensiebundels in het host.json-bestand van de app in.  

U extensiebundels gebruiken met versie 2.x en latere versies van de runtime Functies. Controleer bij het lokaal ontwikkelen of u de nieuwste versie van [Azure Functions Core Tools gebruikt.](functions-run-local.md#v2)

Gebruik extensiebundels voor lokale ontwikkeling met Azure Functions Core Tools, Visual Studio Code en wanneer u op afstand bouwt.

Als u geen extensiebundels gebruikt, moet u de .NET Core 2.x SDK op uw lokale computer installeren voordat u bindende extensies installeert. Uitbreidingsbundels verwijdert deze vereiste voor lokale ontwikkeling. 

Als u extensiebundels wilt gebruiken, werkt u het `extensionBundle` *bestand host.json* bij om de volgende vermelding op te nemen voor:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>C-klassenbibliotheek\# met Visual Studio

In **Visual Studio**u pakketten installeren vanuit de Package Manager Console met de opdracht [Installatiepakket,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) zoals in het volgende voorbeeld wordt weergegeven:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

De naam van de verpakking die voor een bepaalde binding wordt gebruikt, is vermeld in het referentieartikel voor die binding. Zie bijvoorbeeld de [sectie Pakketten van het bindende referentieartikel ServiceBus](functions-bindings-service-bus.md#functions-1x).

Vervang `<TARGET_VERSION>` in het voorbeeld door een specifieke `3.0.0-beta5`versie van het pakket, zoals . Geldige versies worden vermeld op de afzonderlijke pakketpagina's op [NuGet.org](https://nuget.org). De belangrijkste versies die overeenkomen met de runtime 1.x of 2.x van functies zijn opgegeven in het referentieartikel voor de binding.

Als u `Install-Package` een binding wilt verwijzen, hoeft u geen [extensiebundels](#extension-bundles)te gebruiken. Deze benadering is specifiek voor klassenbibliotheken die zijn gebouwd in Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>C# klassenbibliotheek met Visual Studio Code

Installeer in **Visual Studio Code**pakketten voor een C#-klassebibliotheekproject vanaf de opdrachtprompt met de opdracht [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) in de .NET Core CLI. In het volgende voorbeeld wordt uitgelegd hoe u een binding toevoegt:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

De .NET Core CLI kan alleen worden gebruikt voor Azure Functions 2.x ontwikkeling.

Vervang `<BINDING_TYPE_NAME>` door de naam van het pakket dat de binding bevat die u nodig hebt. U vindt het gewenste bindende referentieartikel in de [lijst met ondersteunde bindingen.](./functions-triggers-bindings.md#supported-bindings)

Vervang `<TARGET_VERSION>` in het voorbeeld door een specifieke `3.0.0-beta5`versie van het pakket, zoals . Geldige versies worden vermeld op de afzonderlijke pakketpagina's op [NuGet.org](https://nuget.org). De belangrijkste versies die overeenkomen met de runtime 1.x of 2.x van functies zijn opgegeven in het referentieartikel voor de binding.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Voorbeeld van azure-functietrigger en bindend voorbeeld](./functions-bindings-example.md)
