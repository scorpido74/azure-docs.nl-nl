---
title: Uw Java-functie verbinden met Azure Storage
description: Meer informatie over het verbinden van een Java-functie die door HTTP is geactiveerd met Azure Storage met behulp van een binding voor de uitvoer van wachtrijopslag.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78272806"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Uw Java-functie verbinden met Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel ziet u hoe u de functie die u in het [vorige quickstart-artikel](functions-create-first-java-maven.md) hebt gemaakt, integreren met een Azure Storage-wachtrij. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-verzoek naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen verbindingstekenreeks vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om deze verbinding eenvoudiger te maken, gebruikt u het opslagaccount dat u met uw functie-app hebt gemaakt. De verbinding met dit account is al `AzureWebJobsStorage`opgeslagen in een app-instelling met de naam .  

## <a name="prerequisites"></a>Vereisten

Voordat u dit artikel start, voert u de stappen in [deel 1 van de Java quickstart](functions-create-first-java-maven.md)uit.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Uitbreidingsbundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

U nu de binding van de opslaguitvoer toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In een Java-project worden de bindingen gedefinieerd als bindende annotaties op de functiemethode. Het *bestand function.json* wordt vervolgens automatisch gegenereerd op basis van deze annotaties.

Blader naar de locatie van uw functiecode onder _src/main/java,_ open het *projectbestand Function.java* en voeg de volgende parameter toe aan de `run` methodedefinitie:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

De `msg` parameter [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) is een type dat een verzameling tekenreeksen vertegenwoordigt die als berichten naar een uitvoerbinding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer `outqueue`een opslagwachtrij met de naam . De verbindingstekenreeks voor het opslagaccount `connection` wordt ingesteld door de methode. In plaats van de verbindingstekenreeks zelf, passeert u de toepassingsinstelling die de tekenreeks Opslagaccountverbinding bevat.

De `run` methodedefinitie moet er nu uitzien als het volgende voorbeeld:  

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

U de nieuwe `msg` parameter nu gebruiken om naar de uitvoerbinding van uw functiecode te schrijven. Voeg de volgende coderegel toe voordat de `name` succesrespons `msg` wordt uitgevoerd om de waarde van de uitvoerbinding toe te voegen.

```java
msg.setValue(name);
```

Wanneer u een uitvoerbinding gebruikt, hoeft u de Azure Storage SDK-code niet te gebruiken voor verificatie, het verkrijgen van een wachtrijverwijzing of het schrijven van gegevens. De runtime en queue output binding van functies doen deze taken voor u.

Uw `run` methode moet er nu uitzien als het volgende voorbeeld:

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

Omdat het archetype ook een reeks tests maakt, moet u `msg` deze `run` tests bijwerken om de nieuwe parameter in de methodehandtekening te verwerken.  

Blader naar de locatie van uw testcode onder _src/test/java,_ open het *projectbestand Function.java* en vervang de coderegel onder `//Invoke` door de volgende code.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

U bent nu klaar om de nieuwe uitvoerbinding lokaal uit te proberen.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Net als voorheen gebruikt u de volgende opdracht om het project te bouwen en de runtime Functies lokaal te starten:

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

> [!NOTE]  
> Omdat u extensiebundels inschakelt in host.json, is de [extensie Opslagbinding](functions-bindings-storage-blob.md#add-to-your-functions-app) tijdens het opstarten voor u gedownload en geïnstalleerd, samen met de andere bindende extensies van Microsoft.

Net als voorheen activeert u de functie vanaf de opdrachtregel met behulp van cURL in een nieuw terminalvenster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Deze keer maakt de uitvoerbinding `outqueue` ook een wachtrij met de naam in uw opslagaccount en voegt een bericht met dezelfde tekenreeks toe.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij weer te geven en te controleren of er een bericht is toegevoegd. U uw wachtrij ook bekijken met de [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in de [Azure-portal.](https://portal.azure.com)

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Het project opnieuw implementeren 

Voer de volgende opdracht opnieuw uit om uw gepubliceerde app bij te werken:  

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Nogmaals, u cURL gebruiken om de geïmplementeerde functie te testen. Net als voorheen `AzureFunctions` geeft u de waarde in de hoofdtekst van het POST-verzoek door aan de URL, zoals in dit voorbeeld:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

U [het wachtrijbericht Opslag opnieuw onderzoeken](#query-the-storage-queue) om te controleren of de uitvoerbinding zoals verwacht een nieuw bericht in de wachtrij genereert.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt de functie die http-geactiveerd wordt bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Zie de triggers [en bindingen](functions-triggers-bindings.md)voor [Azure Functions Java voor](functions-reference-java.md) meer informatie over het ontwikkelen van Azure-functies met Java. Zie de voorbeelden van [voorbeelden](/samples/browse/?products=azure-functions&languages=Java)van volledige functieprojecten op Java. 

Vervolgens moet u Application Insights-monitoring inschakelen voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
