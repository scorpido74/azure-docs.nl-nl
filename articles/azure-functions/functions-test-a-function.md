---
title: Azure Functions testen
description: Maak geautomatiseerde tests voor een C# functie in Visual Studio en JavaScript-functie in VS Code
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: c60cd631e703f929eaae56138a2acd3687121924
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226575"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategieën voor het testen van uw code in Azure Functions

In dit artikel ziet u hoe u geautomatiseerde tests uit te maken voor Azure Functions. 

Testen van alle code wordt aanbevolen, maar krijgt u mogelijk de beste resultaten door de logica van een functie afsluit en tests buiten de functie te maken. Om logica direct te abstraheren van een functie coderegels beperkt en kunt de functie alleen verantwoordelijk voor het aanroepen van andere klassen of modules. In dit artikel zijn echter van ziet u hoe u geautomatiseerde tests uit op basis van een HTTP- en een timer geactiveerde functie maken.

De inhoud die volgt is opgesplitst in twee verschillende secties die zijn bedoeld om u te richten op verschillende talen en omgevingen. U kunt meer tests inbouwen in:

- [C#in Visual Studio met xUnit](#c-in-visual-studio)
- [Java script in VS code met jest](#javascript-in-vs-code)

De voor beeld-opslag plaats is beschikbaar op [github](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#in Visual Studio
In het volgende voor beeld wordt beschreven hoe C# u een functie-app maakt in Visual Studio en voert en test met [xUnit](https://xunit.github.io).

![Azure Functions met testen C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Instellen

Als u uw omgeving instelt, een functie maken en testen van de app. De volgende stappen helpen u bij het maken van de apps en functies die vereist zijn ter ondersteuning van de tests:

1. [Een nieuwe functions-app maken](./functions-create-first-azure-function.md) en *hieraan* een naam toe te voegen
2. [Maak een HTTP-functie op basis van de sjabloon](./functions-create-first-azure-function.md) en geef deze de naam *http trigger*.
3. [Maak een timer functie op basis van de sjabloon](./functions-create-scheduled-function.md) en noem deze *Timer trigger*.
4. [Maak een xUnit-test-app](https://xunit.github.io/docs/getting-started-dotnet-core) in Visual Studio door te klikken op **File > New C# > Project > Visual > .net core > xUnit test project** en geef het de naam *functions. test*. 
5. Gebruik Nuget om een verwijzing toe te voegen vanuit de test-app [micro soft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Raadpleeg de *functions* -app](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) vanuit *functions. app testen* .

### <a name="create-test-classes"></a>Test klassen maken

Nu dat de toepassingen die zijn gemaakt, kunt u de klassen gebruikt voor het uitvoeren van de geautomatiseerde tests uit.

Voor elke functie wordt een instantie van [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) gebruikt voor het afhandelen van bericht logboek registratie. Enkele tests uit een geen berichten aanmelden of hebben geen gevolgen voor hoe logboekregistratie wordt geïmplementeerd. Andere tests moeten het evalueren van berichten in het logboek geregistreerd om te bepalen of een test wordt doorgegeven.

De `ListLogger` klasse is bedoeld om de `ILogger`-interface te implementeren en in interne lijst met berichten te houden die tijdens een test worden geëvalueerd.

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

De klasse `ListLogger` implementeert de volgende leden als een contract door de `ILogger`-Interface:

- **BeginScope**: bereiken voegen context toe aan uw logboek registratie. In dit geval wijst de test alleen naar het statische exemplaar op de klasse `NullScope` om de test te laten functioneren.

- **IsEnabled**: er is een standaard waarde van `false`.

- **Log**: deze methode maakt gebruik van de geleverde `formatter` functie om het bericht op te maken en voegt vervolgens de resulterende tekst toe aan de `Logs` verzameling.

De verzameling `Logs` is een instantie van `List<string>` en wordt geïnitialiseerd in de constructor.

Klik vervolgens met de **rechter** muisknop op de *functies. test* toepassing en selecteer **> klasse toevoegen**, noem deze **LoggerTypes.cs** en voer de volgende code in:

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

Klik vervolgens met de **rechter** muisknop op de *functies. test* toepassing en selecteer **> klasse toevoegen**, noem deze **TestFactory.cs** en voer de volgende code in:

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
De klasse `TestFactory` implementeert de volgende leden:

- **Gegevens**: deze eigenschap retourneert een [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) -verzameling van voorbeeld gegevens. De sleutel-waardeparen vertegenwoordigen de waarden die in een queryreeks worden doorgegeven.

- **CreateDictionary**: deze methode accepteert een sleutel/waarde-paar als argumenten en retourneert een nieuwe `Dictionary` die wordt gebruikt om `QueryCollection` te maken om query teken reeks waarden weer te geven.

- **CreateHttpRequest**: met deze methode maakt u een HTTP-aanvraag die is geïnitialiseerd met de opgegeven query reeks parameters.

- **CreateLogger**: op basis van het logboek type retourneert deze methode een logger klasse die wordt gebruikt voor het testen. Met de `ListLogger` worden vastgelegde berichten bijgehouden die beschikbaar zijn voor evaluatie in tests.

Klik vervolgens met de **rechter** muisknop op de *functies. test* toepassing en selecteer **> klasse toevoegen**, noem deze **FunctionsTests.cs** en voer de volgende code in:

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
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
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

- **Http_trigger_should_return_known_string**: met deze test wordt een aanvraag gemaakt met de query teken reeks waarden van `name=Bill` naar een http-functie en wordt gecontroleerd of de verwachte reactie wordt geretourneerd.

- **Http_trigger_should_return_string_from_member_data**: deze test maakt gebruik van xUnit-kenmerken om voorbeeld gegevens aan de http-functie toe te voegen.

- **Timer_should_log_message**: met deze test wordt een exemplaar van `ListLogger` gemaakt en door gegeven aan een timer-functie. Nadat de functie wordt uitgevoerd, wordt het logboek gecontroleerd om te controleren of dat de verwachte bericht aanwezig is.

Als u toegang wilt krijgen tot toepassings instellingen in uw tests, kunt u [System. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables)gebruiken.

### <a name="run-tests"></a>Tests uitvoeren

Als u de tests wilt uitvoeren, gaat u naar de **test Verkenner** en klikt u op **alles uitvoeren**.

![Azure Functions met testen C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Fouten opsporen in tests

Als u fouten wilt opsporen in de tests, stelt u een onderbrekings punt in op een test, gaat u naar de **test Verkenner** en klikt u op **uitvoeren > fout opsporing laatst uitgevoerd**.

## <a name="javascript-in-vs-code"></a>JavaScript in VS Code

In het volgende voor beeld wordt beschreven hoe u een Java script-functie-app maakt in VS code en voert en test met [jest](https://jestjs.io). Deze procedure maakt gebruik van de [extensie VS code functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) om Azure functions te maken.

![Azure Functions met JavaScript testen in VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Instellen

Als u uw omgeving wilt instellen, initialiseert u een nieuwe node. js-app in een lege map door `npm init`uit te voeren.

```bash
npm init -y
```
Vervolgens installeert u Jest door het uitvoeren van de volgende opdracht uit:

```bash
npm i jest
```
Werk nu _package. json_ bij om de bestaande test opdracht te vervangen door de volgende opdracht:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Test-modules maken
Met het project is geïnitialiseerd, kunt u de modules gebruikt voor het uitvoeren van de geautomatiseerde tests uit. Maak eerst een nieuwe map met de naam *tests* om de ondersteunings modules op te slaan.

Voeg in de map *testen* een nieuw bestand toe, geef het de naam **defaultContext. js**en voeg de volgende code toe:

```javascript
module.exports = {
    log: jest.fn()
};
```
In deze module wordt de *logboek* functie voor de standaard uitvoerings context weer gegeven.

Voeg vervolgens een nieuw bestand toe, geef het de naam **defaultTimer. js**en voeg de volgende code toe:

```javascript
module.exports = {
    IsPastDue: false
};
```

Deze module implementeert de eigenschap `IsPastDue` op standaard als een valse Timer-instantie. Timer configuraties zoals NCRONTAB-expressies zijn hier niet vereist omdat de test harnas de functie rechtstreeks aanroept om het resultaat te testen.

Gebruik vervolgens de extensie VS code functions om [een nieuwe Java script HTTP-functie te maken](/azure/javascript/tutorial-vscode-serverless-node-01) en deze *http trigger*te noemen. Nadat de functie is gemaakt, voegt u een nieuw bestand toe aan dezelfde map met de naam **index. test. js**en voegt u de volgende code toe:

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
De HTTP-functie van de sjabloon retourneert een tekenreeks "Hallo" samengevoegd met de naam die is opgegeven in de querytekenreeks. Deze test wordt een valse exemplaar van een aanvraag gemaakt en wordt doorgegeven aan de HTTP-functie. De test controleert of de *logboek* methode eenmaal wordt aangeroepen en de geretourneerde tekst is gelijk aan "Hello Bill".

Gebruik vervolgens de extensie VS code functions om een nieuwe Java Script-timer functie te maken en geef deze de naam *Timer trigger*. Nadat de functie is gemaakt, voegt u een nieuw bestand toe aan dezelfde map met de naam **index. test. js**en voegt u de volgende code toe:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
De functie van de timer van de sjabloon wordt een bericht aan het einde van de hoofdtekst van de functie vastlegt. Deze test zorgt ervoor dat de *logboek* functie eenmaal wordt genoemd.

### <a name="run-tests"></a>Tests uitvoeren
Als u de tests wilt uitvoeren, drukt u op **CTRL + ~** om het opdracht venster te openen en voert u `npm test`uit:

```bash
npm test
```

![Azure Functions met JavaScript testen in VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Fouten opsporen in tests

Als u fouten wilt opsporen in uw tests, voegt u de volgende configuratie toe aan het bestand *Launch. json* :

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Stel vervolgens een onderbrekings punt in uw test in en druk op **F5**.

## <a name="next-steps"></a>Volgende stappen

Nu dat u hebt geleerd hoe u geautomatiseerde tests uit voor uw functies schrijft, kunt u doorgaan met deze resources:
- [Een niet met HTTP geactiveerde functie hand matig uitvoeren](./functions-manually-run-non-http.md)
- [Fout afhandeling Azure Functions](./functions-bindings-error-pages.md)
- [Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger](./functions-debug-event-grid-trigger-local.md)
