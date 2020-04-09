---
title: 'Zelfstudie: e-mail verzenden met Logische Apps'
description: Meer informatie over het aanroepen van bedrijfsprocessen vanuit uw App Service-app. Stuur e-mails, tweets en Facebook-berichten, voeg toe aan mailinglijsten en nog veel meer.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 44f2dfb83e96e1d8fa31ee1acf350193b954dbd8
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892851"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Zelfstudie: E-mail verzenden en andere bedrijfsprocessen van App Service aanroepen

In deze zelfstudie leert u hoe u uw App Service-app integreren met uw bedrijfsprocessen. Dit is gebruikelijk bij web-app-scenario's, zoals:

- Bevestigingse-mail verzenden voor een transactie
- Gebruiker toevoegen aan Facebook-groep
- Maak verbinding met systemen van derden zoals SAP, SalesForce, enz.
- Exchange-standaard B2B-berichten

In deze zelfstudie verstuurt u e-mails met Gmail vanuit uw App Service-app met [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Er zijn andere manieren om e-mails te verzenden vanuit een web-app, zoals SMTP-configuratie die wordt geleverd door uw taalframework. Logic Apps brengt echter veel meer kracht naar uw App Service-app zonder dat uw code wordt toegevoegd. Logic Apps biedt een eenvoudige configuratie-interface voor de meest populaire bedrijfsintegraties en uw app kan ze op elk gewenst moment bellen met een HTTP-verzoek.

## <a name="prerequisite"></a>Vereiste

Implementeer een app met het taalkader van uw keuze naar App Service. Zie hieronder:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Zelfstudie: Een ASP.NET Core- en SQL Database-app maken in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Zelfstudie: Een Node.js- en MongoDB-app bouwen in Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Zelfstudie: Een PHP- en MySQL-app bouwen in Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Zelfstudie: Een Python-webapp (Django) uitvoeren met PostgreSQL in Azure App Service](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Een Ruby- en een Postgres-app maken in Azure App Service op Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>De logische app maken

1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app door de instructies in [Uw logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)te volgen. Wanneer u de **Logic Apps Designer**ziet, keert u terug naar deze zelfstudie.
1. Selecteer op de welkomstpagina voor Logic Apps Designer de optie **Wanneer een HTTP-aanvraag wordt ontvangen** onder Start met een algemene **trigger**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. Selecteer in het dialoogvenster voor **Wanneer een HTTP-aanvraag wordt ontvangen,** de optie **Voorbeeldpayload gebruiken om schema te genereren**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopieer het volgende voorbeeld JSON naar het tekstvak en selecteer **Gereed**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Het schema wordt nu gegenereerd voor de gewenste aanvraaggegevens. In de praktijk u gewoon de werkelijke aanvraaggegevens vastleggen die uw toepassingscode genereert en Azure het JSON-schema voor u laten genereren. 
1. Selecteer Boven aan de Logic Apps Designer de optie **Opslaan**. 

    U nu de URL van uw HTTP-aanvraagtrigger zien. Selecteer het kopieerpictogram om het te kopiëren voor later gebruik.

    ![](./media/tutorial-send-email/http-request-url.png)

    Deze HTTP-aanvraagdefinitie is een trigger voor alles wat u wilt doen in deze logische app, of het nu Gmail of iets anders is. Later zult u deze URL aanroepen in uw App Service-app. Zie de [http-aanvraag-/antwoordverwijzing](../connectors/connectors-native-reqres.md)voor meer informatie over de trigger van de aanvraag.

1. Klik onder aan de ontwerper op **Nieuwe stap,** typ **Gmail** in het zoekvak acties en zoek en selecteer **E-mail verzenden (V2).**
    
    > [!TIP]
    > U zoeken naar andere soorten integraties, zoals SendGrid, MailChimp, Office 365 en SalesForce. Zie [Logic Apps-documentatie](https://docs.microsoft.com/azure/logic-apps/)voor meer informatie.
1. Selecteer in het dialoogvenster **Gmail** de optie **Aanmelden** en aanmelden bij het Gmail-account waaruit u de e-mail wilt verzenden.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Als u bent aangemeld, klikt u in het tekstvak **Aan** en wordt het dialoogvenster dynamische inhoud automatisch geopend.

1. Selecteer **meer**zien naast de actie **Wanneer een HTTP-aanvraag is ontvangen** .

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    U ziet nu de drie eigenschappen van uw voorbeeld JSON-gegevens die u eerder hebt gebruikt. In deze stap gebruikt u deze eigenschappen uit de HTTP-aanvraag om een e-mail te maken.
1. Aangezien u de waarde voor het veld **Aan** selecteert, kiest **u e-mail**. Schakel desgevraagd het dialoogvenster dynamische inhoud uit door op **Dynamische inhoud toevoegen**te klikken.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. Selecteer **Onderwerp** en **hoofdtekst**in de vervolgkeuzelijst **Nieuwe parameter** toevoegen .

1. Klik in het tekstvak **Onderwerp** en kies op dezelfde manier **taak**. Als de cursor zich nog steeds in het vak **Onderwerp** begeeft, *wordt het tekstvak gemaakt*. 

1. Klik in het **lichaam,** en op dezelfde manier, kies **verschuldigd**. De cursor naar links van **due** verplaatsen en typ *Dit werkitem moet worden betaald op*.

    > [!TIP]
    > Als u HTML-inhoud rechtstreeks in de e-mailtekst wilt bewerken, selecteert u **Codeweergave** boven aan het venster Logic Apps Designer. Zorg ervoor dat u de dynamische inhoudscode bewaart `@{triggerBody()?['due']}`(bijvoorbeeld)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Voeg vervolgens een asynchrone HTTP-reactie toe aan de HTTP-trigger. Klik tussen de HTTP-trigger en **+** de actie Gmail op het bord en selecteer **Een parallelle vertakking toevoegen**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Selecteer in het zoekvak **naar respons**en selecteer vervolgens de **actie Antwoord.**

    ![](./media/tutorial-send-email/choose-response-action.png)

    Standaard stuurt de reactieactie een HTTP 200. Dat is goed genoeg voor deze tutorial. Zie de [http-aanvraag/-antwoordverwijzing](../connectors/connectors-native-reqres.md)voor meer informatie .

1. Selecteer opnieuw **opslaan** boven aan de Logic Apps Designer. 

## <a name="add-http-request-code-to-app"></a>HTTP-aanvraagcode toevoegen aan app

Zorg ervoor dat u de URL van de HTTP-aanvraagtrigger van eerder hebt gekopieerd. Omdat het gevoelige informatie bevat, is het best practice dat u deze niet rechtstreeks in de code plaatst. Met App Service u in plaats daarvan verwijzen naar een omgevingsvariabele, met behulp van app-instellingen. 

Maak in de [Cloud Shell](https://shell.azure.com)de app-instelling met de volgende opdracht (vervang * \<de app-naam>, * * \<>van de resourcegroep *en * \<>van de logica-app-url): *

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Maak in uw code een standaard HTTP-bericht naar de URL met behulp van een HTTP-clienttaal die beschikbaar is voor uw taalkader, met de volgende configuratie:

- De aanvraaginstantie bevat dezelfde JSON-indeling die u aan uw logische app hebt geleverd:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- De aanvraag bevat `Content-Type: application/json`de kop . 
- Om de prestaties te optimaliseren, stuurt u de aanvraag indien mogelijk asynchroon.

Klik op het tabblad voorkeurtaal/framework hieronder om een voorbeeld te zien.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

In ASP.NET u de HTTP-post verzenden met de klasse [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Bijvoorbeeld:

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

Als u deze code test in de voorbeeld-app voor [zelfstudie: een ASP.NET-app bouwen in Azure met SQL Database,](app-service-web-tutorial-dotnet-sqldatabase.md)u deze gebruiken om een e-mailbevestiging te verzenden in de [actie Maken](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)nadat het `Todo` item is toegevoegd. Als u de bovenstaande asynchrone code wilt gebruiken, converteert u de actie Maken naar asynchroon.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

In ASP.NET Core u de HTTP-post verzenden met de klasse [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Bijvoorbeeld:

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
> Deze code is geschreven voor eenvoud van demonstratie. In de praktijk u een `HttpClient` object niet voor elke aanvraag instantiëren. Volg de richtlijnen bij [IHttpClientFactory gebruiken omhttps://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient veerkrachtige HTTP-aanvragen te implementeren]( -http-requests).

Als u deze code test in de voorbeeld-app voor [zelfstudie: een ASP.NET Core- en SQL Database-app in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)maken, u deze gebruiken om een e-mailbevestiging te verzenden in de actie [Maken](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)nadat het `Todo` item is toegevoegd.

# <a name="nodejs"></a>[Node.js](#tab/node)

In Node.js u de HTTP-post eenvoudig verzenden met een npm-pakket zoals [axios.](https://www.npmjs.com/package/axios) Bijvoorbeeld:

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

Als u deze code test in de voorbeeld-app voor [zelfstudie: een Node.js- en MongoDB-app in Azure bouwen,](app-service-web-tutorial-nodejs-mongodb-app.md)u deze gebruiken om een e-mailbevestiging in de [functie maken](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)te verzenden, nadat [het artikel is opgeslagen.](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)

# <a name="php"></a>[PHP](#tab/php)

In PHP, u de HTTP-post gemakkelijk met [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Bijvoorbeeld:

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

Als u deze code test in de voorbeeld-app voor [Zelfstudie: Een PHP- en MySQL-app bouwen in Azure,](app-service-web-tutorial-php-mysql.md)u deze gebruiken om een e-mailbevestiging te verzenden in de [functie Route::post,](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)vlak voor de retourinstructie.

# <a name="python"></a>[Python](#tab/python)

In Python u de HTTP-post eenvoudig verzenden met [aanvragen.](https://pypi.org/project/requests/) Bijvoorbeeld:

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

Als u deze code test in de voorbeeld-app voor [Zelfstudie: Voer een Python-web-app (Django) uit met PostgreSQL in Azure App Service,](containers/tutorial-python-postgresql-app.md)u deze gebruiken om een e-mailbevestiging te verzenden in de [functie Route::post,](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)vlak voor de retourinstructie.

# <a name="ruby"></a>[Ruby](#tab/ruby)

In Ruby u de HTTP-post eenvoudig verzenden met [JSONClient.](https://www.rubydoc.info/gems/httpclient/JSONClient) Bijvoorbeeld:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Als u deze code test in de voorbeeld-app voor [een Ruby- en Postgres-app bouwen in Azure App Service op Linux,](containers/tutorial-ruby-postgres-app.md)u deze gebruiken om een e-mailbevestiging te verzenden in de [actie maken,](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [wanneer @task.save dit slaagt.](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)

---

# <a name="more-resources"></a>Meer bronnen

[Zelfstudie: een RESTful API hosten met CORS in Azure App Service](app-service-web-tutorial-rest-api.md)  
[HTTP-aanvraag-/antwoordverwijzing voor Logische apps](../connectors/connectors-native-reqres.md)  
[Snelstart: uw eerste werkstroom maken met Azure Logic Apps - Azure-portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)