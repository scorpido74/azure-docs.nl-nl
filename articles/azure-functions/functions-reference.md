---
title: Richtlijnen voor het ontwikkelen van Azure-functies
description: Leer de concepten en technieken azure functions die u nodig hebt om functies in Azure te ontwikkelen, voor alle programmeertalen en bindingen.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: b6af3d7ab1fdd35391c9a189162c57dfb259f2d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405342"
---
# <a name="azure-functions-developer-guide"></a>Ontwikkelaarshandleiding voor Azure Functions
In Azure-functies delen specifieke functies een aantal belangrijke technische concepten en componenten, ongeacht de taal of binding die u gebruikt. Voordat u in het leren van details die specifiek zijn voor een bepaalde taal of binding, moet u lezen door middel van dit overzicht dat van toepassing is op alle van hen.

In dit artikel wordt ervan uitgegaan dat u het [overzicht Azure-functies](functions-overview.md)al hebt gelezen.

## <a name="function-code"></a>Functiecode
Een *functie* is het primaire concept in Azure-functies. Een functie bevat twee belangrijke stukken - uw code, die kan worden geschreven in een verscheidenheid van talen, en sommige config, de function.json bestand. Voor gecompileerde talen wordt dit config-bestand automatisch gegenereerd uit annotaties in uw code. Voor scripttalen moet u het config-bestand zelf opgeven.

Het bestand function.json definieert de trigger, bindingen en andere configuratie-instellingen van de functie. Elke functie kan slechts één trigger hebben. De runtime gebruikt dit config-bestand om de gebeurtenissen te bepalen die moeten worden gecontroleerd en hoe gegevens kunnen worden doorgegeven aan en gegevens van een functie-uitvoering kunnen worden verzonden. Het volgende is een voorbeeld function.json bestand.

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

Zie [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md)voor meer informatie.

De `bindings` eigenschap is waar u zowel triggers als bindingen configureert. Elke binding deelt een paar gemeenschappelijke instellingen en een aantal instellingen die specifiek zijn voor een bepaald type binding. Elke binding vereist de volgende instellingen:

| Eigenschap | Waarden/typen | Opmerkingen |
| --- | --- | --- |
| `type` |tekenreeks |Bindend type. Bijvoorbeeld `queueTrigger`. |
| `direction` |'in', 'out' |Geeft aan of de binding is voor het ontvangen van gegevens in de functie of het verzenden van gegevens van de functie. |
| `name` |tekenreeks |De naam die wordt gebruikt voor de gebonden gegevens in de functie. Voor C#, dit is een argument naam; voor JavaScript is dit de sleutel in een lijst met sleutels/waarde. |

## <a name="function-app"></a>Function App
Een functie-app biedt een uitvoeringscontext in Azure waarin uw functies worden uitgevoerd. Als zodanig is het de eenheid van implementatie en beheer voor uw functies. Een functie-app bestaat uit een of meer afzonderlijke functies die worden beheerd, geïmplementeerd en samen worden geschaald. Alle functies in een functie-app delen hetzelfde prijsplan, implementatiemethode en runtime-versie. Zie een functie-app als een manier om je functies te ordenen en collectief te beheren. Zie [Een functie-app beheren](functions-how-to-use-azure-function-app-settings.md)voor meer informatie. 

> [!NOTE]
> Alle functies in een functie-app moeten in dezelfde taal worden geschreven. In [eerdere versies](functions-versions.md) van de runtime azure-functies was dit niet vereist.

## <a name="folder-structure"></a>Mapstructuur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Het bovenstaande is de standaardmapstructuur (en aanbevolen) voor een functie-app. Als u de bestandslocatie van de code van `scriptFile` een functie wilt wijzigen, wijzigt u het gedeelte van het _functie.json-bestand._ We raden u ook aan [pakketimplementatie](deployment-zip-push.md) te gebruiken om uw project te implementeren in uw functie-app in Azure. U ook bestaande hulpprogramma's zoals [continue integratie en implementatie](functions-continuous-deployment.md) en Azure DevOps gebruiken.

> [!NOTE]
> Als u een pakket handmatig implementeert, moet u uw _host.json-bestands-_ en functiemappen rechtstreeks naar de `wwwroot` map implementeren. Neem de `wwwroot` map niet op in uw implementaties. Anders kom je `wwwroot\wwwroot` uit bij mappen.

#### <a name="use-local-tools-and-publishing"></a>Lokale tools en publicatie gebruiken
Functie-apps kunnen worden geschreven en gepubliceerd met behulp van een verscheidenheid aan tools, waaronder [Visual Studio](./functions-develop-vs.md), Visual [Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md), en de Azure Functions [Core Tools](./functions-develop-local.md). Zie [Azure-functies lokaal coderen en testen](./functions-develop-local.md)voor meer informatie.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a>Functies bewerken in de Azure-portal
Met de functie-editor die in de Azure-portal is ingebouwd, u uw code en uw *function.json-bestand* direct inline bijwerken. Dit wordt alleen aanbevolen voor kleine wijzigingen of proofs of concept - beste praktijk is het gebruik van een lokale ontwikkeling tool zoals VS Code.

## <a name="parallel-execution"></a>Parallelle uitvoering
Wanneer meerdere triggeringgebeurtenissen sneller optreden dan een runtime met één thread kan deze verwerken, kan de runtime de functie meerdere keren parallel aanroepen.  Als een functie-app het [hostingplan Verbruik](functions-scale.md#how-the-consumption-and-premium-plans-work)gebruikt, kan de functie-app automatisch worden uitgeschaald.  Elk exemplaar van de functie-app, of de app nu wordt uitgevoerd op het hostingplan Voor verbruik of een regulier [App Service-hostingplan,](../app-service/overview-hosting-plans.md)kan gelijktijdige functie-aanroepen parallel verwerken met behulp van meerdere threads.  Het maximum aantal gelijktijdige functie-aanroepen in elke functie-app-instantie varieert afhankelijk van het type trigger dat wordt gebruikt en de bronnen die worden gebruikt door andere functies in de functie-app.

## <a name="functions-runtime-versioning"></a>Runtime-versie van functies

U de versie van de `FUNCTIONS_EXTENSION_VERSION` runtime van de functies configureren met behulp van de app-instelling. De waarde '~3' geeft bijvoorbeeld aan dat uw functie-app 3.x als belangrijkste versie gebruikt. Functie-apps worden geüpgraded naar elke nieuwe secundaire versie die wordt uitgebracht. Zie [Azure Functions runtime-versies targeten](set-runtime-version.md)voor meer informatie, waaronder het weergeven van de exacte versie van uw functie-app.

## <a name="repositories"></a>Opslagplaatsen
De code voor Azure-functies is open source en is opgeslagen in GitHub-repositories:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions-host](https://github.com/Azure/azure-functions-host/)
* [Azure-portal voor functies](https://github.com/azure/azure-functions-ux)
* [Azure-functiessjablonen](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-extensies](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindingen
Hier is een tabel van alle ondersteunde bindingen.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Heeft u problemen met fouten die afkomstig zijn van de bindingen? Controleer de documentatie [van Azure Functions Binding Error Codes.](functions-bindings-error-pages.md)

## <a name="reporting-issues"></a>Problemen melden
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie:

* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* [Azure-functies lokaal programmeren en testen](./functions-develop-local.md)
* [Aanbevolen procedures voor Azure-functies](functions-best-practices.md)
* [Naslaginformatie over Azure Functions C#-ontwikkelaars](functions-dotnet-class-library.md)
* [Naslaginformatie over Azure Functions Node.js-ontwikkelaars](functions-reference-node.md)
