---
title: Versie beheer in Durable Functions-Azure
description: Meer informatie over het implementeren van versie beheer in de Durable Functions-extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: ef64a43cbed7f033a938351506b7f78142ff044c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097625"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versie beheer in Durable Functions (Azure Functions)

Het is onvermijdelijk dat functies worden toegevoegd, verwijderd en gewijzigd gedurende de levens duur van een toepassing. [Durable functions](durable-functions-overview.md) staat het koppelen van functies op verschillende manieren toe, en deze keten heeft invloed op de manier waarop u versie beheer kunt afhandelen.

## <a name="how-to-handle-breaking-changes"></a>Het afbreken van wijzigingen afhandelen

Er zijn verschillende voor beelden van belang rijke wijzigingen die u moet kennen. In dit artikel worden de meest voorkomende items besproken. In het hoofd thema achter deze functies ziet u dat zowel nieuwe als bestaande functie-indelingen worden beïnvloed door wijzigingen in de functie code.

### <a name="changing-activity-function-signatures"></a>Functie handtekeningen van de activiteit wijzigen

Een wijziging in de hand tekening verwijst naar een wijziging in de naam, de invoer of de uitvoer van een functie. Als dit soort wijziging wordt aangebracht in een activiteit functie, kan de Orchestrator-functie die hiervan afhankelijk is, worden verbroken. Als u de Orchestrator-functie bijwerkt om deze wijziging toe te passen, kunt u bestaande sessies in de vlucht verstoren.

Stel dat we de volgende functie hebben.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Deze vereenvoudigde-functie neemt de resultaten van **Foo** en geeft deze door aan een **balk**. We gaan ervan uit dat we de retour waarde van **Foo** moeten wijzigen `bool` van `int` in ter ondersteuning van een breder scala aan resultaat waarden. Het resultaat ziet er als volgt uit:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Deze wijziging is van toepassing op alle nieuwe exemplaren van de Orchestrator-functie, maar breekt alle vlucht-exemplaren af. Denk bijvoorbeeld na over het geval waarin een Orchestrator-exemplaar **Foo**aanroept, een Booleaanse waarde terugkrijgt en vervolgens controle punten. Als de handtekening wijziging op dit moment wordt geïmplementeerd, mislukt het exemplaar van het controle punt onmiddellijk wanneer het wordt hervat en wordt de aanroep `context.CallActivityAsync<int>("Foo")`opnieuw afgespeeld. Dit komt doordat het resultaat in de geschiedenis tabel, `bool` maar de nieuwe code probeert deze te deserialiseren naar `int`.

Dit is slechts een van de vele manieren waarop een handtekening wijziging bestaande instanties kan verstoren. Over het algemeen geldt dat als een Orchestrator het aanroepen van een functie moet wijzigen, de wijziging waarschijnlijk problematisch is.

### <a name="changing-orchestrator-logic"></a>Orchestrator-logica wijzigen

De andere klasse van versie problemen is het wijzigen van de functie code van orchestrator, op een manier die de replay-logica voor exemplaren in de vlucht.

Houd rekening met de volgende Orchestrator-functie:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

We gaan ervan uit dat u een schijnbaar onschuldige wijziging wilt aanbrengen om een andere functie aanroep toe te voegen.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Met deze wijziging wordt een nieuwe functie aanroepen naar **SendNotification** tussen **Foo** en **Bar**. Er zijn geen wijzigingen in de hand tekening. Het probleem doet zich voor wanneer een bestaand exemplaar wordt hervat vanaf de aanroep naar de **Bar**. Als tijdens het opnieuw afspelen de oorspronkelijke aanroep van **Foo** wordt `true`geretourneerd, roept de Orchestrator replay aan bij **SendNotification** die zich niet in de uitvoerings geschiedenis bevindt. Als gevolg hiervan mislukt het duurzame taak raamwerk met een `NonDeterministicOrchestrationException` omdat er een aanroep van **SendNotification** is aangetroffen wanneer de aanroep naar de **balk**werd verwacht.

## <a name="mitigation-strategies"></a>Strategieën voor risico beperking

Hier volgen enkele strategieën voor het oplossen van problemen met versie beheer:

* Niets doen
* Alle exemplaren in de vlucht stoppen
* Gelijktijdige implementaties

### <a name="do-nothing"></a>Niets doen

De eenvoudigste manier om een belang rijke wijziging af te handelen, is te laten mislukken. Nieuwe instanties voeren de gewijzigde code uit.

Of dit een probleem is, is afhankelijk van het belang van uw vlucht instanties. Als u actief bent in de ontwikkeling en u geen zorgen hebt over in-Flight-instanties, is dit mogelijk voldoende. U moet echter wel omgaan met uitzonde ringen en fouten in uw diagnostische pijp lijn. Als u deze dingen wilt voor komen, moet u rekening houden met de andere versie opties.

### <a name="stop-all-in-flight-instances"></a>Alle exemplaren in de vlucht stoppen

Een andere optie is om alle exemplaren in de vlucht te stoppen. U kunt dit doen door de inhoud van de wachtrij voor interne **controle-wachtrij** en werk **item-** wacht rijen te wissen. De exemplaren blijven permanent vastzitten waar ze zich bevinden, maar ze zullen uw telemetrie niet in de wirwar houden met fout berichten. Dit is ideaal voor het snel ontwerpen van prototypen.

> [!WARNING]
> De details van deze wacht rijen kunnen na verloop van tijd veranderen, dus vertrouw niet op deze manier voor productie werkbelastingen.

### <a name="side-by-side-deployments"></a>Gelijktijdige implementaties

De meest recente controle methode om ervoor te zorgen dat belang rijke wijzigingen veilig worden geïmplementeerd, is door ze naast uw oudere versies te implementeren. Dit kan worden gedaan met behulp van een van de volgende technieken:

* Implementeer alle updates als volledig nieuwe functies (nieuwe namen).
* Implementeer alle updates als een nieuwe functie-app met een ander opslag account.
* Implementeer een nieuwe kopie van de functie-app, maar met `TaskHub` een bijgewerkte naam. Dit is de aanbevolen techniek.

### <a name="how-to-change-task-hub-name"></a>De naam van een taak hub wijzigen

De taak hub kan als volgt worden geconfigureerd in het bestand *host. json* :

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

De standaardwaarde is `DurableFunctionsHub`.

Alle Azure storage entiteiten krijgen een naam op basis `HubName` van de configuratie waarde. Door de taak hub een nieuwe naam te geven, zorgt u ervoor dat er afzonderlijke wacht rijen en geschiedenis tabellen worden gemaakt voor de nieuwe versie van uw toepassing.

We raden u aan de nieuwe versie van de functie-app te implementeren in een nieuwe [implementatie sleuf](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Met implementatie sleuven kunt u meerdere exemplaren van uw functie-app naast elkaar uitvoeren, met slechts één van de apps als de actieve *productie* site. Wanneer u klaar bent om de nieuwe indelings logica beschikbaar te maken voor uw bestaande infra structuur, kan de nieuwe versie eenvoudig worden gewisseld naar de productie site.

> [!NOTE]
> Deze strategie werkt het beste wanneer u HTTP-en webhook-triggers gebruikt voor Orchestrator-functies. Voor niet-HTTP-triggers, zoals wacht rijen of Event Hubs, moet de trigger definitie [worden afgeleid van een app-instelling](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) die wordt bijgewerkt als onderdeel van de wissel bewerking.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verwerken van problemen met prestaties en schalen](durable-functions-perf-and-scale.md)
