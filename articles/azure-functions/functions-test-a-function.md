---
title: Azure Functions testen
description: Geautomatiseerde tests maken voor een C#-functie in Visual Studio en JavaScript-functie in VS-code
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: a37fd886e1bc70226b2e54750540dfcb79ee5973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768874"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategieën voor het testen van uw code in Azure Functions

In dit artikel wordt uitgelegd hoe u geautomatiseerde tests voor Azure-functies maakt. 

Het testen van alle code wordt aanbevolen, maar u de beste resultaten krijgen door de logica van een functie in te pakken en tests buiten de functie te maken. Het abstraheren van logica beperkt de coderegels van een functie en maakt het mogelijk dat de functie als enige verantwoordelijk is voor het aanroepen van andere klassen of modules. Dit artikel laat echter zien hoe u geautomatiseerde tests maken op basis van een HTTP- en timergeactiveerde functie.

De inhoud die volgt is opgesplitst in twee verschillende secties die bedoeld zijn om verschillende talen en omgevingen te targeten. U leren om tests in te bouwen in:

- [C# in Visual Studio met xUnit](#c-in-visual-studio)
- [JavaScript in VS-code met Jest](#javascript-in-vs-code)

De voorbeeldopslagplaats is beschikbaar op [GitHub.](https://github.com/Azure-Samples/azure-functions-tests)

## <a name="c-in-visual-studio"></a>C# in Visual Studio
In het volgende voorbeeld wordt beschreven hoe u een C#-functie-app maakt in Visual Studio en met [xUnit](https://xunit.github.io)uitvoeren en testen.

![Azure-functies testen met C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Instellen

Als u uw omgeving wilt instellen, maakt u een functie- en test-app. Met de volgende stappen u de apps en functies maken die nodig zijn om de tests te ondersteunen:

1. [Een nieuwe app Functies maken](./functions-create-first-azure-function.md) en de naam van de functie *benoemen*
2. [Maak een HTTP-functie op basis van de sjabloon](./functions-create-first-azure-function.md) en noem deze *httptrigger*.
3. [Maak een timerfunctie op basis van de sjabloon](./functions-create-scheduled-function.md) en noem deze *TimerTrigger*.
4. [Maak een xUnit Test-app](https://xunit.github.io/docs/getting-started-dotnet-core) in Visual Studio door op **Bestand > Nieuwe > Project > Visual C# > .NET Core > xUnit Test Project** te klikken en deze functie te *benoemen.Test*. 
5. NuGet gebruiken om een verwijzing van de test-app toe te voegen aan [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Raadpleeg de *app Functies* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) van de app *Functions.Test.*

### <a name="create-test-classes"></a>Testklassen maken

Nu de toepassingen zijn gemaakt, u de klassen maken die worden gebruikt om de geautomatiseerde tests uit te voeren.

Elke functie neemt een exemplaar van [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) om berichtlogboekregistratie te behandelen. Sommige tests registreren geen berichten of maken zich geen zorgen over de manier waarop logboekregistratie wordt geïmplementeerd. Andere tests moeten berichten evalueren die zijn geregistreerd om te bepalen of een test wordt uitgevoerd.

De `ListLogger` klasse implementeert de `ILogger` interface en houdt een interne lijst van berichten voor evaluatie tijdens een test.

**Klik met** de rechtermuisknop op de *toepassing Functions.Test* en selecteer **> klasse toevoegen,** **geef deze NullScope.cs** een naam en voer de volgende code in:

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

Klik vervolgens **met de rechtermuisknop** op de toepassing *Functions.Test* en selecteer **> klasse toevoegen,** **geef deze ListLogger.cs** een naam en voer de volgende code in:

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

De `ListLogger` klasse implementeert de volgende `ILogger` leden zoals gecontracteerd door de interface:

- **BeginScope:** Scopes voegen context toe aan uw logboekregistratie. In dit geval wijst de test alleen `NullScope` naar de statische instantie op de klasse om de test te laten functioneren.

- **IsEnabled**: Er `false` is een standaardwaarde van.

- **Logboek:** Deze methode `formatter` gebruikt de meegeleverde functie om het bericht `Logs` op te maken en voegt vervolgens de resulterende tekst toe aan de verzameling.

De `Logs` collectie is `List<string>` een voorbeeld van en wordt geïnitialiseerd in de constructeur.

Klik vervolgens **met de rechtermuisknop** op de toepassing *Functions.Test* en selecteer **> klasse toevoegen,** **geef deze LoggerTypes.cs** een naam en voer de volgende code in:

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
Deze opsomming geeft het type logger aan dat door de tests wordt gebruikt. 

Klik vervolgens **met de rechtermuisknop** op de toepassing *Functions.Test* en selecteer **> klasse toevoegen,** **geef deze TestFactory.cs** een naam en voer de volgende code in:

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

- **Gegevens**: Deze eigenschap retourneert een [ienumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) verzameling van voorbeeldgegevens. De sleutelwaardeparen vertegenwoordigen waarden die worden doorgegeven aan een querytekenreeks.

- **CreateDictionary**: Deze methode accepteert een sleutel/waardepaar `Dictionary` als argumenten `QueryCollection` en retourneert een nieuw gebruikt om te maken om querytekenreekswaarden weer te geven.

- **CreatehttpRequest:** Met deze methode wordt een HTTP-aanvraag geparafeerd met de opgegeven querytekenreeksparameters.

- **CreateLogger:** Op basis van de logger type, deze methode retourneert een logger klasse gebruikt voor het testen. De `ListLogger` bijhouden van geregistreerde berichten beschikbaar voor evaluatie in tests.

Klik vervolgens **met de rechtermuisknop** op de toepassing *Functions.Test* en selecteer **> klasse toevoegen,** **geef deze FunctionsTests.cs** een naam en voer de volgende code in:

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
De leden die in deze klasse worden geïmplementeerd zijn:

- **Http_trigger_should_return_known_string:** met deze test wordt een `name=Bill` aanvraag aanmaken met de querytekenreekswaarden van een HTTP-functie en wordt gecontroleerd of het verwachte antwoord wordt geretourneerd.

- **Http_trigger_should_return_string_from_member_data:** Deze test gebruikt xUnit-kenmerken om voorbeeldgegevens aan de HTTP-functie te verstrekken.

- **Timer_should_log_message**: Deze test `ListLogger` maakt een instantie van en geeft deze door aan een timerfuncties. Zodra de functie is uitgevoerd, wordt het logboek gecontroleerd om ervoor te zorgen dat het verwachte bericht aanwezig is.

Als u toegang wilt krijgen tot toepassingsinstellingen in uw tests, u [System.Environment.GetEnvironmentVariable gebruiken.](./functions-dotnet-class-library.md#environment-variables)

### <a name="run-tests"></a>Tests uitvoeren

Als u de tests wilt uitvoeren, navigeert u naar de **Testverkenner** en klikt u op **Alles uitvoeren**.

![Azure-functies testen met C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Foutopsporingstests

Als u de tests wilt debuggen, stelt u een breekpunt in op een test, navigeert u naar de **Test Explorer** en klikt u op Uitvoeren **> Foutopsporingslaatste run**.

## <a name="javascript-in-vs-code"></a>JavaScript in VS-code

In het volgende voorbeeld wordt beschreven hoe u een JavaScript-functie-app maakt in VS-code en met [Jest](https://jestjs.io)uitvoeren en testen. Deze procedure maakt gebruik van de [vs-codefuncties-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) om Azure-functies te maken.

![Azure-functies testen met JavaScript in VS-code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Instellen

Als u uw omgeving wilt instellen, initialiseert u een nieuwe `npm init`App Node.js in een lege map door .

```bash
npm init -y
```
Installeer Vervolgens Jest door de volgende opdracht uit te voeren:

```bash
npm i jest
```
Update _nu package.json_ bij om de bestaande testopdracht te vervangen door de volgende opdracht:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Testmodules maken
Met het project geïnitialiseerd, u de modules maken die worden gebruikt om de geautomatiseerde tests uit te voeren. Begin met het maken van een nieuwe map met de naam *testen* om de ondersteuningsmodules vast te houden.

Voeg in de *testmap* een nieuw bestand toe, geef **het standaardContext.js**en voeg de volgende code toe:

```javascript
module.exports = {
    log: jest.fn()
};
```
Met deze module wordt de *logboekfunctie* gemockd om de standaarduitvoeringscontext weer te geven.

Voeg vervolgens een nieuw bestand toe, geef **het standaardTimer.js**een naam en voeg de volgende code toe:

```javascript
module.exports = {
    IsPastDue: false
};
```

Deze module implementeert de `IsPastDue` eigenschap te staan is als een nep timer instantie. Timerconfiguraties zoals NCRONTAB-expressies zijn hier niet vereist, omdat het testharnas gewoon de functie rechtstreeks aanroept om de uitkomst te testen.

Gebruik vervolgens de uitbreiding VS-codefuncties om [een nieuwe JavaScript HTTP-functie](/azure/javascript/tutorial-vscode-serverless-node-01) te maken en deze HttpTrigger een naam *te geven.* Zodra de functie is gemaakt, voegt u een nieuw bestand toe in dezelfde map met de naam **index.test.js**en voegt u de volgende code toe:

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
De HTTP-functie van de sjabloon retourneert een tekenreeks van 'Hallo' die is gekoppeld aan de naam in de querytekenreeks. Deze test maakt een nep-exemplaar van een aanvraag en geeft deze door aan de HTTP-functie. De test controleert of de *logmethode* eenmaal wordt aangeroepen en de geretourneerde tekst is gelijk aan "Hello Bill".

Gebruik vervolgens de uitbreiding VS-codefuncties om een nieuwe JavaScript Timer-functie te maken en deze TimerTrigger een naam te *geven.* Zodra de functie is gemaakt, voegt u een nieuw bestand toe in dezelfde map met de naam **index.test.js**en voegt u de volgende code toe:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
De timerfunctie van de sjabloon registreert een bericht aan het einde van de hoofdtekst van de functie. Deze test zorgt ervoor dat de *logfunctie* eenmalig wordt aangeroepen.

### <a name="run-tests"></a>Tests uitvoeren
Als u de tests wilt uitvoeren, drukt u `npm test`op Ctrl + **~** om het opdrachtvenster te openen en voert u uit:

```bash
npm test
```

![Azure-functies testen met JavaScript in VS-code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Foutopsporingstests

Als u uw tests wilt debuggen, voegt u de volgende configuratie toe aan uw *bestand launch.json:*

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

Stel vervolgens een breekpunt in uw test en druk op **F5**.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u geautomatiseerde tests voor uw functies schrijven, gaat u verder met deze bronnen:
- [Handmatig een niet door HTTP geactiveerde functie uitvoeren](./functions-manually-run-non-http.md)
- [Foutafhandeling azure-functies](./functions-bindings-error-pages.md)
- [Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger](./functions-debug-event-grid-trigger-local.md)
