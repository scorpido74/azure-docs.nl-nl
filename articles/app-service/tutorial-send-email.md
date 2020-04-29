---
title: 'Zelf studie: e-mail met Logic Apps verzenden'
description: Meer informatie over het aanroepen van bedrijfs processen vanuit uw App Service-app. Verzend e-mail berichten, tweets en Facebook-berichten, voeg ze toe aan de verzend lijsten en nog veel meer.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 4073b49a134356943bd7da8d54bf574f2e0d5eea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604856"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Zelf studie: e-mail verzenden en andere bedrijfs processen aanroepen via App Service

In deze zelf studie leert u hoe u uw App Service-app integreert met uw bedrijfs processen. Dit is gebruikelijk voor web app-scenario's, zoals:

- Bevestigings-e-mail voor een trans actie verzenden
- Gebruiker toevoegen aan Facebook-groep
- Maak verbinding met systemen van derden als SAP, Sales Force, enzovoort.
- Uitwisseling van standaard B2B-berichten

In deze zelf studie verzendt u e-mail berichten met Gmail vanuit uw App Service-app met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md). Er zijn andere manieren om e-mail berichten te verzenden vanuit een web-app, zoals de SMTP-configuratie van uw taal raamwerk. Logic Apps biedt uw App Service-app echter veel meer kracht zonder complexiteit toe te voegen aan uw code. Logic Apps biedt een eenvoudige configuratie-interface voor de populairste bedrijfs integraties, en uw app kan ze op elk gewenst moment aanroepen met een HTTP-aanvraag.

## <a name="prerequisite"></a>Vereiste

Implementeer een app met het taal raamwerk van uw keuze om App Service. Zie hieronder om een zelf studie te volgen voor het implementeren van een voor beeld-app:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Zelf studie: een ASP.NET Core-en SQL Database-app bouwen in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Zelf studie: een node. js-en MongoDB-app bouwen in azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Zelf studie: een PHP-en MySQL-app bouwen in azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Zelf studie: een Python-web-app (Django) uitvoeren met PostgreSQL in Azure App Service](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Een Ruby- en een Postgres-app maken in Azure App Service op Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>De logische app maken

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app door de instructies in [uw logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)te volgen. Wanneer u de **Logic apps Designer**ziet, keert u terug naar deze zelf studie.
1. Selecteer op de welkomst pagina voor Logic Apps Designer **Wanneer een HTTP-aanvraag wordt ontvangen** onder **beginnen met een gemeen schappelijke trigger**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. In het dialoog venster voor **Wanneer een HTTP-aanvraag wordt ontvangen**, selecteert u voor **beeld-Payload gebruiken om een schema te genereren**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopieer de volgende JSON van het voor beeld naar het tekstvak en selecteer **gereed**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Het schema wordt nu gegenereerd voor de gewenste aanvraag gegevens. In de praktijk kunt u alleen de werkelijke aanvraag gegevens vastleggen die door de toepassings code worden gegenereerd en Azure het JSON-schema laten genereren. 
1. Selecteer op de pagina Logic Apps Designer de optie **Opslaan**. 

    U kunt nu de URL van de HTTP-aanvraag trigger zien. Selecteer het Kopieer pictogram om het te kopiëren voor later gebruik.

    ![](./media/tutorial-send-email/http-request-url.png)

    Deze definitie van de HTTP-aanvraag is een trigger naar alles wat u in deze logische app wilt doen. Dit is Gmail of iets anders. Later roept u deze URL op in uw App Service-app. Zie de [HTTP-aanvraag/antwoord-verwijzing](../connectors/connectors-native-reqres.md)voor meer informatie over de aanvraag trigger.

1. Klik onder aan de ontwerp functie op **nieuwe stap**, typ **Gmail** in het zoekvak acties en zoek en selecteer **e-mail verzenden (v2)**.
    
    > [!TIP]
    > U kunt zoeken naar andere typen integraties, zoals SendGrid, MailChimp, Office 365 en Sales Force. Zie [Logic apps-documentatie](https://docs.microsoft.com/azure/logic-apps/)voor meer informatie.
1. In het dialoog venster **Gmail** selecteert u **Aanmelden** en meldt u zich aan bij het Gmail-account waaruit u het e-mail bericht wilt verzenden.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Wanneer u bent aangemeld, klikt u in het tekstvak **naar** en wordt automatisch het dialoog venster dynamische inhoud geopend.

1. Naast de actie **Wanneer een HTTP-aanvraag wordt ontvangen** , selecteert u **meer weer geven**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    U ziet nu de drie eigenschappen van uw voor beeld-JSON-gegevens die u eerder hebt gebruikt. In deze stap gebruikt u deze eigenschappen van de HTTP-aanvraag om een e-mail bericht te maken.
1. Omdat u de waarde voor het veld **aan** selecteert, kiest u **e-mail**. Als u wilt, schakelt u het dialoog venster dynamische inhoud uit door te klikken op **dynamische inhoud toevoegen**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. Selecteer in de vervolg keuzelijst **nieuwe para meter toevoegen** het **onderwerp** en de **hoofd tekst**.

1. Klik in het tekstvak **onderwerp** en kies op dezelfde manier **taak**. Terwijl de cursor zich nog in het vak **onderwerp** bevindt, typt u *gemaakt*. 

1. Klik in de **hoofd tekst**en kies op dezelfde manier de optie **verval datum**. Verplaats de cursor naar links van **eind datum** en typ *dit werk item moet worden voltooid*.

    > [!TIP]
    > Als u HTML-inhoud rechtstreeks in de hoofd tekst van de e-mail wilt bewerken, selecteert u de **code weergave** boven aan het venster Logic apps Designer. Zorg ervoor dat u de code van de dynamische inhoud behoudt (bijvoorbeeld `@{triggerBody()?['due']}`)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Voeg vervolgens een asynchroon HTTP-antwoord toe aan de HTTP-trigger. Klik tussen de HTTP-trigger en de Gmail-actie **+** op het teken en selecteer **een parallelle vertakking toevoegen**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Zoek in het zoekvak naar **antwoord**en selecteer vervolgens de **reactie** actie.

    ![](./media/tutorial-send-email/choose-response-action.png)

    Standaard verzendt de reactie actie een HTTP-200. Dat is voldoende voor deze zelf studie. Zie de [HTTP-aanvraag/antwoord-verwijzing](../connectors/connectors-native-reqres.md)voor meer informatie.

1. Selecteer aan de bovenkant van de Logic Apps Designer opnieuw **Opslaan** . 

## <a name="add-http-request-code-to-app"></a>HTTP-aanvraag code toevoegen aan de app

Zorg ervoor dat u de URL van de HTTP-aanvraag trigger van eerder hebt gekopieerd. Omdat het gevoelige informatie bevat, is het best practice dat u deze niet rechtstreeks in de code plaatst. Met App Service kunt u in plaats daarvan naar een omgevings variabele verwijzen met behulp van app-instellingen. 

Maak in de [Cloud shell](https://shell.azure.com)de app-instelling met de volgende opdracht (Vervang * \<app-name>*, * \<resource-group-name>* en * \<Logic-app-URL>*):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Maak in uw code een standaard HTTP post naar de URL met behulp van een HTTP-client taal die beschikbaar is voor uw taal raamwerk, met de volgende configuratie:

- De aanvraag tekst bevat dezelfde JSON-indeling die u hebt opgegeven voor uw logische app:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- De aanvraag bevat de kop `Content-Type: application/json`. 
- Als u de prestaties wilt optimaliseren, moet u de aanvraag asynchroon verzenden indien mogelijk.

Klik op het tabblad Voorkeurs taal/Framework hieronder om een voor beeld te bekijken.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

In ASP.NET kunt u het HTTP-bericht verzenden met de klasse [System .net. http. httpclient maakt](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) . Bijvoorbeeld:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Als u deze code test op de voor beeld-app voor de [zelf studie: een ASP.net-app bouwen in azure met SQL database](app-service-web-tutorial-dotnet-sqldatabase.md), u kunt deze gebruiken om een e-mail bevestiging te verzenden `Todo` in de [actie maken](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)nadat het item is toegevoegd. Als u de asynchrone code hierboven wilt gebruiken, moet u de actie maken in asynchroon omzetten.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

In ASP.NET Core kunt u het HTTP-bericht verzenden met de klasse [System .net. http. httpclient maakt](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) . Bijvoorbeeld:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Deze code is geschreven voor het eenvoudig te demonstreren. In de praktijk moet u voor `HttpClient` elke aanvraag geen object instantiëren. Volg de richt lijnen in [use IHttpClientFactory om flexibele HTTP-aanvragen te implementeren](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Als u deze code op de voor beeld-app test [: een ASP.net core-en SQL database-app bouwen in azure app service](app-service-web-tutorial-dotnetcore-sqldb.md), kunt u deze gebruiken om een e-mail bevestiging te verzenden in de [actie maken](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)nadat het `Todo` item is toegevoegd.

# <a name="nodejs"></a>[Node.js](#tab/node)

In node. js kunt u het HTTP-bericht eenvoudig verzenden met een NPM-pakket, zoals [Axios](https://www.npmjs.com/package/axios). Bijvoorbeeld:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Als u deze code test op de voor beeld-app voor de [zelf studie: een node. js-en MongoDb-app bouwen in azure](app-service-web-tutorial-nodejs-mongodb-app.md), u kunt deze gebruiken om een e-mail bevestiging te verzenden in de [functie Create](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)nadat [het artikel is opgeslagen](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

# <a name="php"></a>[PHP](#tab/php)

In PHP kunt u het HTTP-bericht eenvoudig verzenden met [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Bijvoorbeeld:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Als u deze code op de voor beeld-app test [: een PHP-en MySQL-app bouwen in azure](app-service-web-tutorial-php-mysql.md), kunt u deze gebruiken om een e-mail bevestiging te verzenden in de [functie route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), net vóór de instructie return.

# <a name="python"></a>[Python](#tab/python)

In python kunt u het HTTP-bericht eenvoudig verzenden met [aanvragen](https://pypi.org/project/requests/). Bijvoorbeeld:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Als u deze code test op de voor beeld-app voor de [zelf studie: een Python-web-app (Django) uitvoeren met postgresql in azure app service](containers/tutorial-python-postgresql-app.md), kunt u deze gebruiken om een e-mail bevestiging te verzenden in de [ost-functie:p](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), net vóór de instructie return.

# <a name="ruby"></a>[Ruby](#tab/ruby)

In Ruby kunt u het HTTP-bericht eenvoudig verzenden met [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Bijvoorbeeld:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Als u deze code test in de voor beeld-app voor het [bouwen van een Ruby-en post gres-app in azure app service op Linux](containers/tutorial-ruby-postgres-app.md), kunt u deze gebruiken om een e-mail bevestiging te verzenden in de actie [maken](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [Wanneer @task.save deze slaagt](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

# <a name="more-resources"></a>Meer bronnen

[Zelfstudie: een RESTful API hosten met CORS in Azure App Service](app-service-web-tutorial-rest-api.md)  
[HTTP-aanvraag/antwoord referentie voor Logic Apps](../connectors/connectors-native-reqres.md)  
[Snelstartgids: uw eerste werk stroom maken met behulp van Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)