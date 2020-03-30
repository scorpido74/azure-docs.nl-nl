---
title: Testen van Azure Sustainable Functions-eenheden
description: Meer informatie over het testen van duurzame functies.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231228"
---
# <a name="durable-functions-unit-testing"></a>Testen van duurzame functies

Unit testing is een belangrijk onderdeel van moderne software ontwikkeling praktijken. Unittests controleren het gedrag van bedrijfslogica en beschermen tegen het introduceren van onopgemerkte veranderingen in de toekomst. Duurzame functies kunnen gemakkelijk groeien in complexiteit, zodat de invoering van unit tests zal helpen om te voorkomen dat het breken van veranderingen. In de volgende secties wordt uitgelegd hoe u de drie functietypen - Orchestration client, orchestrator en activiteitsfuncties testen.

> [!NOTE]
> Dit artikel biedt richtlijnen voor het testen van eenheden voor apps voor duurzame functies die zich richten op duurzame functies 1.x. Het is nog niet bijgewerkt om rekening te houden met wijzigingen geïntroduceerd in duurzame functies 2.x. Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

## <a name="prerequisites"></a>Vereisten

De voorbeelden in dit artikel vereisen kennis van de volgende concepten en kaders:

* Moduletests uitvoeren

* Durable Functions

* [xUnit](https://xunit.github.io/) - Testkader

* [moq](https://github.com/moq/moq4) - Mocking framework

## <a name="base-classes-for-mocking"></a>Basisklassen voor spotten

Mocking wordt ondersteund via drie abstracte klassen in Duurzame functies 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Deze klassen zijn basisklassen `DurableOrchestrationClient` `DurableOrchestrationContext`voor `DurableActivityContext` , en definiëren Orchestration Client, Orchestrator en Activiteitmethoden. De mocks stellen het verwachte gedrag in voor basisklassemethoden, zodat de eenheidstest de bedrijfslogica kan verifiëren. Er is een workflow in twee stappen voor het testen van de bedrijfslogica in de Orchestration Client en Orchestrator:

1. Gebruik de basisklassen in plaats van de concrete implementatie bij het definiëren van orchestration client en orchestrator functie handtekeningen.
2. In de eenheidstests bespot het gedrag van de basisklassen en controleer de bedrijfslogica.

Meer informatie in de volgende alinea's voor testfuncties die gebruikmaken van de orchestration client binding en de orchestrator trigger binding.

## <a name="unit-testing-trigger-functions"></a>Triggerfuncties voor het testen van eenheden

In deze sectie valideert de eenheidstest de logica van de volgende HTTP-triggerfunctie voor het starten van nieuwe orkestraties.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

De testtaak van de eenheid is `Retry-After` om de waarde van de koptekst in het antwoordlaadvermogen te verifiëren. Dus de eenheid stest `DurableOrchestrationClientBase` zal een aantal van de methoden om voorspelbaar gedrag te waarborgen bespotten.

Ten eerste is een mock van `DurableOrchestrationClientBase`de basisklasse vereist. De mock kan een nieuwe `DurableOrchestrationClientBase`klasse zijn die implementeert. Echter, met behulp van een spottend kader zoals [moq](https://github.com/moq/moq4) vereenvoudigt het proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Vervolgens `StartNewAsync` wordt de methode bespot om een bekende instantie-ID terug te geven.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Vervolgens `CreateCheckStatusResponse` wordt bespot om altijd een lege HTTP 200-reactie terug te geven.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`ILogger`wordt ook bespot:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Nu `Run` wordt de methode aangeroepen uit de eenheidstest:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        loggerMock.Object);
 ```

 De laatste stap is het vergelijken van de uitvoer met de verwachte waarde:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Na het combineren van alle stappen heeft de eenheidstest de volgende code:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Unit testen orchestrator functies

Orchestrator functies zijn nog interessanter voor unit testen, omdat ze meestal veel meer zakelijke logica.

In deze sectie valideren de eenheidstests de output van de `E1_HelloSequence` functie Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

De testcode van de eenheid begint met het maken van een mock:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Vervolgens worden de aanroepen van de activiteitsmethode bespot:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Vervolgens zal de `HelloSequence.Run` eenheidstest de methode oproepen:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

En tenslotte wordt de output gevalideerd:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Na het combineren van alle stappen heeft de eenheidstest de volgende code:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Activiteitsfuncties voor het testen van eenheden

Activiteitsfuncties kunnen op dezelfde manier worden getest als niet-duurzame functies.

In deze sectie valideert de eenheidstest het gedrag van de `E1_SayHello` functie Activiteit:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

En de eenheidstests zullen het formaat van de uitvoer verifiëren. De eenheidstests kunnen de parametertypen direct of mock `DurableActivityContextBase` class gebruiken:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Meer informatie over moq](https://github.com/Moq/moq4/wiki/Quickstart)
