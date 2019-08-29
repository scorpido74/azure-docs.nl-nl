---
title: Azure Functions testen
description: Maak geautomatiseerde tests voor een C# functie in Visual Studio en JavaScript-functie in VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functies, gebeurtenisverwerking, webhooks, dynamisch berekenen, architectuur zonder server, testen
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 0bd6222a6f2a2582fb715dbaf364fe23e41630d5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085148"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategieën voor het testen van uw code in Azure Functions

In dit artikel ziet u hoe u geautomatiseerde tests uit te maken voor Azure Functions. 

Testen van alle code wordt aanbevolen, maar krijgt u mogelijk de beste resultaten door de logica van een functie afsluit en tests buiten de functie te maken. Om logica direct te abstraheren van een functie coderegels beperkt en kunt de functie alleen verantwoordelijk voor het aanroepen van andere klassen of modules. In dit artikel zijn echter van ziet u hoe u geautomatiseerde tests uit op basis van een HTTP- en een timer geactiveerde functie maken.

De inhoud die volgt is opgesplitst in twee verschillende secties die zijn bedoeld om u te richten op verschillende talen en omgevingen. U kunt meer tests inbouwen in:

- [C#in Visual Studio met xUnit](#c-in-visual-studio)
- [JavaScript in VS Code met Jest](#javascript-in-vs-code)

De voor beeld-opslag plaats is beschikbaar op [github](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#in Visual Studio
Het volgende voorbeeld wordt beschreven hoe u een C# functie-app in Visual Studio en worden uitgevoerd en getest met [xUnit](https://xunit.github.io).

![Azure Functions met testen C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Instellen

Als u uw omgeving instelt, een functie maken en testen van de app. De volgende stappen helpen u bij het maken van de apps en functies die vereist zijn ter ondersteuning van de tests:

1. [Maak een nieuwe Azure Functions-app](./functions-create-first-azure-function.md) en noem het *functies*
2. [Een HTTP-functie maken vanuit de sjabloon](./functions-create-first-azure-function.md) en noem het *HttpTrigger*.
3. [Een timerfunctie maken vanuit de sjabloon](./functions-create-scheduled-function.md) en noem het *TimerTrigger*.
4. [Maak een xUnit-test-app](https://xunit.github.io/docs/getting-started-dotnet-core) in Visual Studio door te klikken op **File > New C# > Project > Visual > .net core > xUnit test project** en geef het de naam *functions. test*. 
5. Gebruik Nuget om een verwijzing toe te voegen vanuit de test-app [micro soft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Naslaginformatie over de *functies* app](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) van *Functions.Test* app.

### <a name="create-test-classes"></a>Test klassen maken

Nu dat de toepassingen die zijn gemaakt, kunt u de klassen gebruikt voor het uitvoeren van de geautomatiseerde tests uit.

Elke functie heeft een exemplaar van [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) voor het afhandelen van logboekregistratie van berichten. Enkele tests uit een geen berichten aanmelden of hebben geen gevolgen voor hoe logboekregistratie wordt geïmplementeerd. Andere tests moeten het evalueren van berichten in het logboek geregistreerd om te bepalen of een test wordt doorgegeven.

De `ListLogger` klasse is bedoeld voor het implementeren van de `ILogger` interface en houdt in de interne lijst met berichten voor de evaluatie tijdens een test.

**Klik** met de rechter muisknop op de *functies. test* toepassing en selecteer **> klasse toevoegen**, noem deze **NullScope.cs** en voer de volgende code in:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Klik vervolgens met de **rechter** muisknop op de *functies. test* toepassing en selecteer **> klasse toevoegen**, noem deze **ListLogger.cs** en voer de volgende code in:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

De `ListLogger` de volgende leden van de klasse wordt geïmplementeerd als aangegaan door de `ILogger` interface:

- **BeginScope**: Bereiken voegen context toe aan uw logboek registratie. In dit geval wijst de test alleen naar het statische exemplaar op de `NullScope` klasse om de test te laten functioneren.

- **IsEnabled**: Er wordt een standaard `false` waarde van gegeven.

- **Logboek**: Deze methode maakt gebruik van `formatter` de geleverde functie om het bericht op te maken en vervolgens de `Logs` resulterende tekst aan de verzameling toe te voegen.

De `Logs` verzameling is een exemplaar van `List<string>` en in de constructor is geïnitialiseerd.

Volgende **met de rechtermuisknop op** op de *Functions.Test* toepassing en selecteer **toevoegen > klasse**, geef deze de naam **LoggerTypes.cs** en voer de de volgende code:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
Deze opsomming geeft het type logger die worden gebruikt door de tests. 

Volgende **met de rechtermuisknop op** op de *Functions.Test* toepassing en selecteer **toevoegen > klasse**, geef deze de naam **TestFactory.cs** en voer de de volgende code:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
De `TestFactory` klasse implementeert de volgende leden:

- **Gegevens**: Deze eigenschap retourneert een [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) -verzameling van voorbeeld gegevens. De sleutel-waardeparen vertegenwoordigen de waarden die in een queryreeks worden doorgegeven.

- **CreateDictionary**: Deze methode accepteert een sleutel/waarde-paar als argumenten en retourneert een `Dictionary` nieuwe die wordt `QueryCollection` gebruikt om query teken reeks waarden weer te geven.

- **CreateHttpRequest**: Met deze methode maakt u een HTTP-aanvraag die is geïnitialiseerd met de opgegeven query reeks parameters.

- **CreateLogger**: Op basis van het logboek type retourneert deze methode een logger-klasse die wordt gebruikt voor het testen. De `ListLogger` houdt van geregistreerde berichten beschikbaar voor evaluatie in tests.

Volgende **met de rechtermuisknop op** op de *Functions.Test* toepassing en selecteer **toevoegen > klasse**, geef deze de naam **FunctionsTests.cs** en voer de de volgende code:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
De leden die zijn geïmplementeerd in deze klasse zijn:

- **Http_trigger_should_return_known_string**: Met deze test wordt een aanvraag met de query teken reeks `name=Bill` waarden van naar een http-functie gemaakt en wordt gecontroleerd of de verwachte reactie wordt geretourneerd.

- **Http_trigger_should_return_string_from_member_data**: Deze test maakt gebruik van xUnit-kenmerken om voorbeeld gegevens aan de HTTP-functie toe te voegen.

- **Timer_should_log_message**: Met deze test wordt een exemplaar `ListLogger` van gemaakt en door gegeven aan een timer-functie. Nadat de functie wordt uitgevoerd, wordt het logboek gecontroleerd om te controleren of dat de verwachte bericht aanwezig is.

Als u toegang wilt krijgen tot toepassings instellingen in uw tests, kunt u [System. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables)gebruiken.

### <a name="run-tests"></a>Tests uitvoeren

De tests uitvoeren, gaat u naar de **Test Explorer** en klikt u op **voert u alle**.

![Azure Functions met testen C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Fouten opsporen in tests

Om op te sporen de tests, stel een onderbrekingspunt in op een test, gaat u naar de **Explorer testen** en klikt u op **uitvoeren > fouten opsporen in laatste uitvoeren**.

## <a name="javascript-in-vs-code"></a>JavaScript in VS Code

Het volgende voorbeeld wordt beschreven hoe u een JavaScript-functie-app maken in VS Code en uitvoert en test met [Jest](https://jestjs.io). Deze procedure maakt gebruik van de [functies van Visual Studio Code-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) te maken van Azure Functions.

![Azure Functions met JavaScript testen in VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Instellen

Als u uw omgeving instelt, een nieuwe Node.js-app in een lege map worden geïnitialiseerd door uit te voeren `npm init`.

```bash
npm init -y
```
Vervolgens installeert u Jest door het uitvoeren van de volgende opdracht uit:

```bash
npm i jest
```
Nu bijwerken _package.json_ te vervangen door de bestaande testopdracht met de volgende opdracht:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Test-modules maken
Met het project is geïnitialiseerd, kunt u de modules gebruikt voor het uitvoeren van de geautomatiseerde tests uit. Beginnen met het maken van een nieuwe map met de naam *testen* voor het opslaan van de modules ondersteuning.

In de *testen* map Voeg een nieuw bestand toe, geef deze de naam **defaultContext.js**, en voeg de volgende code toe:

```javascript
module.exports = {
    log: jest.fn()
};
```
Deze module mocks de *log* functie om weer te geven van de standaardcontext kan worden uitgevoerd.

Vervolgens voegt u een nieuw bestand toe, geef deze de naam **defaultTimer.js**, en voeg de volgende code toe:

```javascript
module.exports = {
    IsPastDue: false
};
```
Deze module implementeert de `IsPastDue` eigenschap passief is als de instantie van een valse timer.

Gebruik vervolgens de functies van Visual Studio Code-extensie voor [maken van een nieuwe functie van de JavaScript-HTTP-](https://code.visualstudio.com/tutorials/functions-extension/getting-started) en noem het *HttpTrigger*. Nadat de functie is gemaakt, Voeg een nieuw bestand in dezelfde map met de naam **index.test.js**, en voeg de volgende code toe:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
De HTTP-functie van de sjabloon retourneert een tekenreeks "Hallo" samengevoegd met de naam die is opgegeven in de querytekenreeks. Deze test wordt een valse exemplaar van een aanvraag gemaakt en wordt doorgegeven aan de HTTP-functie. De test wordt gecontroleerd dat de *log* methode eenmaal wordt aangeroepen en de geretourneerde tekst is gelijk aan 'Hello factuur'.

Gebruik vervolgens de functies van Visual Studio Code-extensie om te maken van een nieuwe functie van de JavaScript-Timer en geef deze de naam *TimerTrigger*. Nadat de functie is gemaakt, Voeg een nieuw bestand in dezelfde map met de naam **index.test.js**, en voeg de volgende code toe:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
De functie van de timer van de sjabloon wordt een bericht aan het einde van de hoofdtekst van de functie vastlegt. Deze test zorgt ervoor dat de *log* functie eenmaal wordt aangeroepen.

### <a name="run-tests"></a>Tests uitvoeren
De tests uitvoeren, drukt u op **CTRL + ~** naar het opdrachtvenster open en voer `npm test`:

```bash
npm test
```

![Azure Functions met JavaScript testen in VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Fouten opsporen in tests

Om op te sporen uw tests, toevoegen de volgende configuratie voor uw *launch.json* bestand:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Vervolgens stelt een onderbrekingspunt in uw test en druk op **F5**.

## <a name="next-steps"></a>Volgende stappen

Nu dat u hebt geleerd hoe u geautomatiseerde tests uit voor uw functies schrijft, kunt u doorgaan met deze resources:
- [Een niet met HTTP geactiveerde functie hand matig uitvoeren](./functions-manually-run-non-http.md)
- [Azure Functions-foutafhandeling](./functions-bindings-error-pages.md)
- [Azure-functie Event Grid Trigger lokale foutopsporing](./functions-debug-event-grid-trigger-local.md)
