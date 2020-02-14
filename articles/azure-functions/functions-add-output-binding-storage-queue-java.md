---
title: Uw Java-functie verbinden met Azure Storage
description: Meer informatie over het verbinden van een door HTTP geactiveerde Java-functie naar Azure Storage met behulp van een uitvoer binding voor de wachtrij opslag.
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: 72e3aad15ea8ef922d89a67891e223b65473b909
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198544"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Uw Java-functie verbinden met Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel wordt beschreven hoe u de functie die u hebt gemaakt in het [vorige Quick](functions-create-first-java-maven.md) start-artikel integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Als u deze verbinding eenvoudiger wilt maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet u de stappen in [deel 1 van de Java-Snelstartgids](functions-create-first-java-maven.md)volt ooien.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

U kunt nu de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In een Java-project worden de bindingen gedefinieerd als bindings aantekeningen voor de functie methode. Het bestand *Function. json* wordt vervolgens automatisch gegenereerd op basis van deze aantekeningen.

Blader naar de locatie van de functie code onder _src/main/Java_, open het *functie. java* -project bestand en voeg de volgende para meter toe aan de `run` methode definitie:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

De para meter `msg` is een [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) type dat een verzameling teken reeksen vertegenwoordigt die als berichten naar een uitvoer binding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslag wachtrij met de naam `outqueue`. De connection string voor het opslag account wordt ingesteld met de methode `connection`. In plaats van de connection string zelf, geeft u de toepassings instelling door die het opslag account bevat connection string.

De definitie van de `run` methode moet er nu uitzien als in het volgende voor beeld:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

U kunt nu de nieuwe para meter `msg` gebruiken om naar de uitvoer binding van uw functie code te schrijven. Voeg de volgende regel code toe vóór het antwoord van het slagen om de waarde van `name` toe te voegen aan de `msg`-uitvoer binding.

```java
msg.setValue(name);
```

Wanneer u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

De `run` methode moet er nu uitzien als in het volgende voor beeld:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>De tests bijwerken

Omdat de archetype ook een reeks tests maakt, moet u deze tests bijwerken om de nieuwe `msg`-para meter in de `run` methode hand tekening te verwerken.  

Blader naar de locatie van de test code onder _src/test/java_, open het *functie. java* -project bestand en vervang de regel met code onder `//Invoke` met de volgende code.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

U bent nu klaar om de nieuwe uitvoer binding lokaal uit te proberen.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Gebruik, net als voorheen, de volgende opdracht om het project te bouwen en de functions-runtime lokaal te starten:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Omdat u uitbreidings bundels in de host. json hebt ingeschakeld, is de [opslag bindings uitbreiding](functions-bindings-storage-blob.md#add-to-your-functions-app) tijdens het opstarten gedownload en geïnstalleerd, samen met de andere micro soft-bindings extensies.

Activeer, net als voorheen, de functie vanaf de opdracht regel met behulp van krul in een nieuw terminal venster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

De uitvoer binding maakt ook een wachtrij met de naam `outqueue` in uw opslag account en voegt een bericht toe met dezelfde teken reeks.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij te bekijken en te controleren of er een bericht is toegevoegd. U kunt uw wachtrij ook weer geven met behulp van de [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in de [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Het project opnieuw implementeren 

Voer de volgende opdracht opnieuw uit om uw gepubliceerde app bij te werken:  

```azurecli
mvn azure-functions:deploy
```

U kunt ook krul gebruiken om de geïmplementeerde functie te testen. Geef, net als voorheen, de waarde `AzureFunctions` in de hoofd tekst van de POST-aanvraag naar de URL, zoals in dit voor beeld:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

U kunt [de opslag wachtrij opnieuw bekijken](#query-the-storage-queue) om te controleren of de uitvoer binding een nieuw bericht in de wachtrij genereert, zoals verwacht.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw door HTTP geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Zie voor meer informatie over het ontwikkelen van Azure Functions met Java de [Azure functions Java-ontwikkelaars handleiding](functions-reference-java.md) en [Azure functions triggers en bindingen](functions-triggers-bindings.md). Zie voor voor beelden van complete functie projecten in Java de [Java functions](/samples/browse/?products=azure-functions&languages=Java)-voor beelden. 

Schakel vervolgens Application Insights bewaking in voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/