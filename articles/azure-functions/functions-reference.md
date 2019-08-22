---
title: Hulp bij het ontwikkelen van Azure Functions | Microsoft Docs
description: Meer informatie over de Azure Functions-concepten en-technieken die u nodig hebt voor het ontwikkelen van functies in azure, in alle programmeer talen en bindingen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: ontwikkelaars handleiding, Azure functions, functies, gebeurtenis verwerking, webhooks, dynamische compute, serverloze architectuur
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 2715aeae5d10241703fa876557d1edcea1723874
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874957"
---
# <a name="azure-functions-developers-guide"></a>Hand leiding Azure Functions ontwikkel aars
In Azure Functions delen specifieke functies enkele kern technische concepten en onderdelen, ongeacht de taal of binding die u gebruikt. Lees de informatie in dit overzicht die van toepassing is op alle voor waarden, voordat u naar een bepaalde taal of binding gaat gaan.

In dit artikel wordt ervan uitgegaan dat u het [Azure functions overzicht](functions-overview.md)al hebt gelezen.

## <a name="function-code"></a>Functie code
Een *functie* is het primaire concept in azure functions. Een functie bevat twee belang rijke onderdelen: uw code, die in verschillende talen kan worden geschreven, en een bepaalde configuratie, het bestand function. json. Voor gecompileerde talen wordt dit configuratie bestand automatisch gegenereerd vanuit aantekeningen in uw code. Voor script talen moet u zelf het configuratie bestand opgeven.

Het bestand function. json definieert de trigger, bindingen en andere configuratie-instellingen van de functie. Elke functie heeft één en slechts één trigger. De runtime gebruikt dit configuratie bestand om te bepalen welke gebeurtenissen moeten worden bewaakt en hoe gegevens moeten worden door gegeven en hoe gegevens kunnen worden opgehaald uit een functie-uitvoering. Hier volgt een voor beeld van een function. JSON-bestand.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Zie voor meer voor beelden [Function. json wiki page](https://github.com/Azure/azure-functions-host/wiki/function.json).

De `bindings` eigenschap is waar u zowel triggers als bindingen configureert. Elke binding deelt enkele algemene instellingen en enkele instellingen die specifiek zijn voor een bepaald type binding. Voor elke binding zijn de volgende instellingen vereist:

| Eigenschap | Waarden/typen | Opmerkingen |
| --- | --- | --- |
| `type` |string |Bindings type. Bijvoorbeeld `queueTrigger`. |
| `direction` |' in ', ' out ' |Geeft aan of de binding is voor het ontvangen van gegevens van de functie of het verzenden van gegevens van de functie. |
| `name` |string |De naam die wordt gebruikt voor de afhankelijke gegevens in de functie. Voor C#is dit de naam van een argument; voor Java script is het de sleutel in een lijst met sleutel/waarden. |

## <a name="function-app"></a>Function App
Een functie-app biedt een uitvoerings context in azure waarin uw functies worden uitgevoerd. Een functie-app bestaat uit een of meer afzonderlijke functies die met elkaar worden beheerd, geïmplementeerd en geschaald. Alle functies in een functie-app delen hetzelfde prijs plan, doorlopende implementatie en runtime versie. U kunt een functie-app beschouwen als een manier om uw functies te organiseren en gezamenlijk te beheren. 

> [!NOTE]
> Alle functies in een functie-app moeten in dezelfde taal zijn geschreven. In [eerdere versies](functions-versions.md) van de Azure functions runtime is dit niet vereist.

## <a name="folder-structure"></a>Mapstructuur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Hierboven vindt u de standaard (en aanbevolen) mapstructuur voor een functie-app. Als u de bestands locatie van een functie code wilt wijzigen, wijzigt u de `scriptFile` sectie van het bestand _Function. json_ . We raden u ook aan om [pakket implementatie](deployment-zip-push.md) te gebruiken voor het implementeren van uw project in uw functie-app in Azure. U kunt ook bestaande hulpprogram ma's zoals [continue integratie en implementatie](functions-continuous-deployment.md) en Azure DevOps gebruiken.

> [!NOTE]
> Als u een pakket hand matig implementeert, moet u ervoor zorgen dat u uw _host. json_ -bestand `wwwroot` en functie mappen rechtstreeks naar de map implementeert. Neem de `wwwroot` map niet op in uw implementaties. Anders eindigt u met `wwwroot\wwwroot` mappen.

#### <a name="use-local-tools-and-publishing"></a>Lokale hulpprogram ma's en publicatie gebruiken
Functie-apps kunnen worden gemaakt en gepubliceerd met behulp van verschillende hulpprogram ma's, waaronder [Visual Studio](./functions-develop-vs.md), [Visual Studio code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [eclips](./functions-create-maven-eclipse.md)en de [Azure functions core tools](./functions-develop-local.md). Zie [code en test Azure functions lokaal](./functions-develop-local.md)voor meer informatie.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a>Functies bewerken in de Azure Portal
Met de functions-editor die in de Azure Portal is ingebouwd, kunt u uw code en uw *functie. json* -bestand direct inline bijwerken. Dit wordt alleen aanbevolen voor kleine wijzigingen of de controle van concepten best practice het gebruik van een lokaal ontwikkelings programma zoals VS code.

## <a name="parallel-execution"></a>Parallelle uitvoering
Als er meerdere activerings gebeurtenissen sneller optreden dan een functie-runtime met één thread, kan deze de functie meerdere keren tegelijk aanroepen.  Als een functie-app het [verbruiks hosting plan](functions-scale.md#how-the-consumption-and-premium-plans-work)gebruikt, kan de functie-app automatisch worden uitgeschaald.  Elk exemplaar van de functie-app, of de app wordt uitgevoerd op het verbruiks-hosting plan of een regulier [app service hosting abonnement](../app-service/overview-hosting-plans.md), kan gelijktijdig gelijktijdige functie aanroepen verwerken met behulp van meerdere threads.  Het maximum aantal gelijktijdige functie aanroepen in elk functie-app-exemplaar is afhankelijk van het type trigger dat wordt gebruikt, evenals de resources die worden gebruikt door andere functies in de functie-app.

## <a name="functions-runtime-versioning"></a>Functions runtime versie beheer

U kunt de versie van de functions-runtime configureren `FUNCTIONS_EXTENSION_VERSION` met de app-instelling. De waarde "~ 2" geeft bijvoorbeeld aan dat uw functie-app 2. x als primaire versie gebruikt. Functie-apps worden bijgewerkt naar elke nieuwe secundaire versie zodra deze worden vrijgegeven. Zie [runtime-versies van Azure functions instellen](set-runtime-version.md)voor meer informatie over het weer geven van de exacte versie van uw functie-app.

## <a name="repositories"></a>Opslagplaatsen
De code voor Azure Functions is open source en opgeslagen in GitHub-opslag plaatsen:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions host](https://github.com/Azure/azure-functions-host/)
* [Azure Functions Portal](https://github.com/azure/azure-functions-ux)
* [Azure Functions sjablonen](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-extensies](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindingen
Hier volgt een tabel met alle ondersteunde bindingen.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Ondervindt u problemen met de fouten die afkomstig zijn van de bindingen? Raadpleeg de documentatie over de [Azure functions bindings fout codes](functions-bindings-error-pages.md) .

## <a name="reporting-issues"></a>Rapportage problemen
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie:

* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* [Azure-functies lokaal programmeren en testen](./functions-develop-local.md)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslag C# informatie voor Azure functions ontwikkel aars](functions-dotnet-class-library.md)
* [Naslag informatie voor Azure Functions NodeJS-ontwikkel aars](functions-reference-node.md)
