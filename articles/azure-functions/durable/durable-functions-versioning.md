---
title: Versiebeheer in duurzame functies - Azure
description: Meer informatie over het implementeren van versiebeheer in de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232763"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versiebeheer in duurzame functies (Azure-functies)

Het is onvermijdelijk dat functies worden toegevoegd, verwijderd en gewijzigd gedurende de levensduur van een toepassing. [Duurzame functies](durable-functions-overview.md) maken het mogelijk om functies aan elkaar te ketenen op een manier die voorheen niet mogelijk was, en deze chaining beïnvloedt hoe u versiebeheer verwerken.

## <a name="how-to-handle-breaking-changes"></a>Omgaan met brekende wijzigingen

Er zijn verschillende voorbeelden van het doorbreken van veranderingen om bewust van te zijn. Dit artikel bespreekt de meest voorkomende. Het hoofdthema achter alle van hen is dat zowel nieuwe als bestaande functie orchestrations worden beïnvloed door wijzigingen in functiecode.

### <a name="changing-activity-or-entity-function-signatures"></a>Handtekeningen van activiteit of entiteitsfunctie wijzigen

Een handtekeningwijziging verwijst naar een wijziging in de naam, invoer of uitvoer van een functie. Als dit soort wijzigingen worden aangebracht in een activiteit of entiteitsfunctie, kan deze elke orchestrator-functie verbreken die ervan afhangt. Als u de orchestrator-functie bijwerkt om aan deze wijziging tegemoet te komen, u bestaande in-flight-exemplaren verbreken.

Stel dat we de volgende orchestrator-functie hebben.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Deze simplistische functie neemt de resultaten van **Foo** en geeft het door aan **Bar**. Laten we aannemen dat we de retourwaarde `bool` `int` van **Foo** moeten wijzigen van naar een breder scala aan resultaatwaarden. Het resultaat ziet er als volgt uit:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> De vorige C#-voorbeelden zijn gericht op duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

Deze wijziging werkt prima voor alle nieuwe exemplaren van de orchestrator-functie, maar breekt alle in-flight-exemplaren. Houd bijvoorbeeld rekening met het geval waarin een `Foo`instantie voor orkestratie een functie aanroept met de naam , een booleaanse waarde terugkrijgt en vervolgens controlepunten. Als de handtekeningwijziging op dit punt wordt geïmplementeerd, mislukt de controlepuntinstantie onmiddellijk `context.CallActivityAsync<int>("Foo")`wanneer deze wordt hervat en wordt de oproep opnieuw afgespeeld naar . Deze fout gebeurt omdat het resultaat `bool` in de geschiedenistabel is, maar `int`de nieuwe code probeert te deserialiseren in .

Dit voorbeeld is slechts een van de vele verschillende manieren waarop een handtekeningwijziging bestaande exemplaren kan verbreken. In het algemeen, als een orchestrator de manier moet veranderen het een functie roept, dan is de verandering waarschijnlijk problematisch.

### <a name="changing-orchestrator-logic"></a>Veranderende orchestrator logica

De andere klasse van versieproblemen komt voort uit het wijzigen van de orchestrator-functiecode op een manier die de replay-logica voor in-flight-exemplaren verwart.

Overweeg de volgende orchestrator-functie:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Laten we aannemen dat u een schijnbaar onschuldige verandering wilt aanbrengen om een andere functieoproep toe te voegen.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> De vorige C#-voorbeelden zijn gericht op duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

Met deze wijziging wordt een nieuwe functieaanroep toegevoegd aan **SendNotification** tussen **Foo** en **Bar**. Er zijn geen handtekeningwijzigingen. Het probleem doet zich voor wanneer een bestaande instantie wordt hervat van de aanroep naar **Bar**. Tijdens replay, als de **Foo** oorspronkelijke `true`oproep naar Foo terug, dan is de orchestrator replay zal bellen naar **SendNotification**, die niet in de uitvoeringsgeschiedenis. Als gevolg hiervan mislukt het framework `NonDeterministicOrchestrationException` duurzame taak met een omdat het een oproep naar **SendNotification** heeft ondervonden wanneer wordt verwacht dat het een oproep naar **Bar zou zien.** Hetzelfde type probleem kan optreden bij het toevoegen van `CreateTimer`oproepen `WaitForExternalEvent`aan "duurzame" API's, waaronder , , enz.

## <a name="mitigation-strategies"></a>Mitigatiestrategieën

Hier zijn enkele van de strategieën voor het omgaan met versiebeheer uitdagingen:

* Niets doen
* Alle in-flight exemplaren stoppen
* Side-by-side implementaties

### <a name="do-nothing"></a>Niets doen

De eenvoudigste manier om een brekende wijziging aan te pakken, is door in-flight orchestration-exemplaren te laten mislukken. Met nieuwe instanties wordt de gewijzigde code uitgevoerd.

Of dit soort mislukking een probleem is, hangt af van het belang van uw in-flight instances. Als u in actieve ontwikkeling en niet de zorg over in-flight gevallen, kan dit goed genoeg zijn. U moet echter wel omgaan met uitzonderingen en fouten in uw diagnostische pijplijn. Als u die dingen wilt vermijden, overweeg dan de andere versieopties.

### <a name="stop-all-in-flight-instances"></a>Alle in-flight exemplaren stoppen

Een andere optie is om alle in-flight exemplaren te stoppen. Stopping all instances can be done by clearing the contents of the internal **control-queue** and **workitem-queue** queues. De exemplaren zullen voor altijd vast blijven zitten waar ze zijn, maar ze zullen uw logboeken niet verrommelen met foutberichten. Deze aanpak is ideaal in snelle prototypeontwikkeling.

> [!WARNING]
> De details van deze wachtrijen kunnen in de loop van de tijd veranderen, dus vertrouw niet op deze techniek voor productieworkloads.

### <a name="side-by-side-deployments"></a>Side-by-side implementaties

De meest fail-proof manier om ervoor te zorgen dat brekende wijzigingen veilig worden geïmplementeerd, is door ze naast uw oudere versies te implementeren. Dit kan met behulp van een van de volgende technieken:

* Implementeer alle updates als geheel nieuwe functies, waardoor bestaande functies als-is. Dit kan lastig zijn omdat de bellers van de nieuwe functieversies ook volgens dezelfde richtlijnen moeten worden bijgewerkt.
* Implementeer alle updates als een nieuwe functie-app met een ander opslagaccount.
* Implementeer een nieuwe kopie van de functie-app met `taskHub` hetzelfde opslagaccount, maar met een bijgewerkte naam. Side-by-side implementaties is de aanbevolen techniek.

### <a name="how-to-change-task-hub-name"></a>De naam van de taakhub wijzigen

De taakhub kan als volgt in het bestand *host.json* worden geconfigureerd:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functies 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

De standaardwaarde voor duurzame functies `DurableFunctionsHub`v1.x is . Vanaf duurzame functies v2.0 is de standaardnaam van de taakhub dezelfde `TestHubName` als de naam van de functie-app in Azure of als deze buiten Azure wordt uitgevoerd.

Alle Azure Storage-entiteiten worden `hubName` benoemd op basis van de configuratiewaarde. Door de taakhub een nieuwe naam te geven, zorgt u ervoor dat afzonderlijke wachtrijen en geschiedenistabel worden gemaakt voor de nieuwe versie van uw toepassing. De functie-app stopt echter met het verwerken van gebeurtenissen voor orkestraties of entiteiten die zijn gemaakt onder de vorige naam van de taakhub.

We raden u aan de nieuwe versie van de functie-app te implementeren in een nieuwe [implementatiesleuf.](../functions-deployment-slots.md) Met implementatiesleuven u meerdere kopieën van uw functie-app naast elkaar uitvoeren, met slechts één van hen als actieve *productiesleuf.* Wanneer u klaar bent om de nieuwe orchestration-logica bloot te stellen aan uw bestaande infrastructuur, kan het net zo eenvoudig zijn als het verwisselen van de nieuwe versie in de productiesleuf.

> [!NOTE]
> Deze strategie werkt het beste wanneer u HTTP en webhook triggers gebruikt voor orchestrator-functies. Voor niet-HTTP-triggers, zoals wachtrijen of gebeurtenishubs, moet de triggerdefinitie [afkomstig zijn van een app-instelling](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) die wordt bijgewerkt als onderdeel van de swapbewerking.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het omgaan met prestatie- en schaalproblemen](durable-functions-perf-and-scale.md)
