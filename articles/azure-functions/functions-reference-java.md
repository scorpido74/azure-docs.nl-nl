---
title: Java-ontwikkelaarsreferentie voor Azure-functies
description: Begrijpen hoe u functies ontwikkelen met Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673009"
---
# <a name="azure-functions-java-developer-guide"></a>Java-ontwikkelaarshandleiding voor Azure Functions

De runtime van Azure Functions ondersteunt [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Deze handleiding bevat informatie over de fijne kneepjes van het schrijven van Azure-functies met Java.

Aangezien het andere talen overkomt, kan een functie-app een of meer functies hebben. Een Java-functie `public` is een methode, versierd `@FunctionName`met de annotatie. Deze methode definieert de vermelding voor een Java-functie en moet uniek zijn in een bepaald pakket. Een functie-app geschreven in Java kan meerdere klassen met meerdere `@FunctionName`openbare methoden geannoteerd met .

In dit artikel wordt ervan uitgegaan dat u de verwijzing naar de [ontwikkelaar van Azure-functies](functions-reference.md)al hebt gelezen. U moet ook de functies snel starten om uw eerste functie te maken, met behulp van [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) of [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Programmeermodel 

De concepten van [triggers en bindingen](functions-triggers-bindings.md) zijn van fundamenteel belang voor Azure-functies. Triggers starten de uitvoering van uw code. Bindingen bieden u een manier om gegevens door te geven aan en gegevens van een functie te retourneren, zonder dat u aangepaste toegangscode voor gegevens hoeft te schrijven.

## <a name="create-java-functions"></a>Java-functies maken

Om het gemakkelijker te maken om Java-functies te maken, zijn er Maven-gebaseerde tooling en archetypen die vooraf gedefinieerde Java-sjablonen gebruiken om u te helpen projecten te maken met een specifieke functietrigger.    

### <a name="maven-based-tooling"></a>Maven-gebaseerde tooling

De volgende ontwikkelaarsomgevingen hebben Azure Functions-hulpprogramma's waarmee u Java-functieprojecten maken: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Het artikel links hierboven laten zien hoe je je eerste functies te maken met behulp van uw IDE van keuze. 

### <a name="project-scaffolding"></a>Project Steigers

Als u liever commando lijn ontwikkeling van de Terminal, de eenvoudigste manier `Apache Maven` om java-gebaseerde functie projecten steiger is het gebruik van archetypen. Het Archetype Java Maven voor Azure-functies wordt gepubliceerd onder de volgende _groupId:__artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

De volgende opdracht genereert een nieuw Java-functieproject met behulp van dit archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Om aan de slag te gaan met dit archetype, zie de [Java quickstart](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java). 

## <a name="create-kotlin-functions-preview"></a>Kotlin-functies maken (voorbeeld)

Er is ook een Maven archetype om Kotlin functies te genereren. Dit archetype, momenteel in preview, wordt gepubliceerd onder de volgende _groupId:__artifactId_: [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

De volgende opdracht genereert een nieuw Java-functieproject met behulp van dit archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Om aan de slag te gaan met dit archetype, zie de [Kotlin quickstart.](functions-create-first-kotlin-maven.md)

## <a name="folder-structure"></a>Mapstructuur

Hier is de mapstructuur van een Java-project voor Azure Functions:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* Het Kotlin project lijkt erg op elkaar, omdat het nog steeds Maven_

U een bestand gedeelde [host.json](functions-host-json.md) gebruiken om de functie-app te configureren. Elke functie heeft zijn eigen codebestand (.java) en bindend configuratiebestand (function.json).

U meer dan één functie in een project plaatsen. Vermijd het zetten van uw functies in aparte potten. De `FunctionApp` in de doelmap is wat wordt geïmplementeerd in uw functie-app in Azure.

## <a name="triggers-and-annotations"></a>Triggers en annotaties

 Functies worden aangeroepen door een trigger, zoals een HTTP-aanvraag, een timer of een update van gegevens. Uw functie moet die trigger en andere ingangen verwerken om een of meer uitgangen te produceren.

Gebruik de Java-annotaties die zijn opgenomen in het pakket [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) om invoer en uitvoer aan uw methoden te binden. Zie de [Java-referentiedocumenten voor](/java/api/com.microsoft.azure.functions.annotation)meer informatie .

> [!IMPORTANT] 
> U moet een Azure Storage-account configureren in uw [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) om azure blob-opslag, Azure Queue-opslag of Azure Table-opslaglokaal uit te voeren.

Voorbeeld:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Hier is de `function.json` gegenereerde corresponderendoor de [azure-functions-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Beschikbaarheid en ondersteuning voor JDK runtime 

Download en gebruik de [Azul Zulu Enterprise voor Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs van [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)voor lokale ontwikkeling van Java-functie-apps. Azure Functions gebruikt de Azul Java 8 JDK-runtime wanneer u uw functie-apps implementeert in de cloud.

[Azure-ondersteuning](https://azure.microsoft.com/support/) voor problemen met de JDK's en functie-apps is beschikbaar met een [gekwalificeerd ondersteuningsplan.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>JVM aanpassen

Met functions u de Virtuele Java-machine (JVM) aanpassen die wordt gebruikt om uw Java-functies uit te voeren. De [volgende JVM-opties](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) worden standaard gebruikt:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

U aanvullende argumenten geven `JAVA_OPTS`in een app-instelling met de naam. U app-instellingen toevoegen aan uw functie-app die is geïmplementeerd in Azure in de Azure-portal of de Azure CLI.

### <a name="azure-portal"></a>Azure Portal

Gebruik in de [Azure-portal](https://portal.azure.com)het tabblad `JAVA_OPTS` [Toepassingsinstellingen](functions-how-to-use-azure-function-app-settings.md#settings) om de instelling toe te voegen.

### <a name="azure-cli"></a>Azure CLI

U de [opdracht az-functieapp config appsettings instellen](/cli/azure/functionapp/config/appsettings) om in te stellen, `JAVA_OPTS`zoals in het volgende voorbeeld:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Dit voorbeeld maakt headless mode mogelijk. Vervang `<APP_NAME>` de naam van uw `<RESOURCE_GROUP>` functie-app en door de resourcegroep.

> [!WARNING]  
> In [het verbruiksplan](functions-scale.md#consumption-plan)moet `WEBSITE_USE_PLACEHOLDER` u de `0`instelling toevoegen met een waarde van .  
Deze instelling verhoogt de koude starttijden voor Java-functies.

## <a name="third-party-libraries"></a>Bibliotheken van derden 

Azure Functions ondersteunt het gebruik van bibliotheken van derden. Standaard worden alle afhankelijkheden die `pom.xml` in uw projectbestand [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) zijn opgegeven, automatisch gebundeld tijdens het doel. Voor bibliotheken die niet zijn `pom.xml` opgegeven als afhankelijkheden `lib` in het bestand, plaatst u deze in een map in de hoofdmap van de functie. Afhankelijkheden die `lib` in de map worden geplaatst, worden toegevoegd aan de systeemklasselader tijdens runtime.

De `com.microsoft.azure.functions:azure-functions-java-library` afhankelijkheid wordt standaard op het klassenpad geleverd en hoeft niet `lib` in de map te worden opgenomen. [Azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) voegt ook afhankelijkheden toe die [hier](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) worden vermeld aan het klassenpad.

## <a name="data-type-support"></a>Ondersteuning voor gegevenstype

U Plain old Java-objecten (POBO's), typen gedefinieerd in `azure-functions-java-library`of primitieve gegevenstypen zoals String en Integer gebruiken om te binden aan invoer- of uitvoerbindingen.

### <a name="pojos"></a>POJOs POJOs

Voor het converteren van invoergegevens naar POJO maakt [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) gebruik van de [gson-bibliotheek.](https://github.com/google/gson) POJO-typen die worden gebruikt `public`als ingangen van functies moeten zijn .

### <a name="binary-data"></a>Binaire gegevens

Bind binaire ingangen `byte[]`of uitgangen `dataType` aan , door het `binary`veld in uw function.json in te stellen op:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Als u null-waarden `Optional<T>`verwacht, gebruikt u .

## <a name="bindings"></a>Bindingen

Invoer- en uitvoerbindingen bieden een declaratieve manier om verbinding te maken met gegevens vanuit uw code. Een functie kan meerdere invoer- en uitvoerbindingen hebben.

### <a name="input-binding-example"></a>Voorbeeld van invoerbinding

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

U beroept zich op deze functie met een HTTP-aanvraag. 
- HTTP-aanvraag payload wordt `String` doorgegeven `inputReq`als een voor het argument .
- Eén vermelding wordt opgehaald uit tabelopslag `TestInputData` en wordt `inputData`doorgegeven aan het argument .

Als u een batch invoer wilt `String[]` `POJO[]`ontvangen, u binden aan, , `List<String>`of `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Deze functie wordt geactiveerd wanneer er nieuwe gegevens in de geconfigureerde gebeurtenishub zijn. Omdat `cardinality` de functie `MANY`is ingesteld op , ontvangt de functie een batch berichten van de gebeurtenishub. `EventData`van gebeurtenishub wordt `TestEventData` omgezet in voor de functieuitvoering.

### <a name="output-binding-example"></a>Voorbeeld van uitvoerbinding

U een uitvoerbinding binden aan `$return`de retourwaarde met behulp van. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Als er meerdere uitvoerbindingen zijn, gebruikt u de retourwaarde voor slechts één van deze bindingen.

Als u meerdere uitvoerwaarden wilt verzenden, gebruikt u `OutputBinding<T>` gedefinieerd in het `azure-functions-java-library` pakket. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

U beroept zich op deze functie op een HttpRequest. Het schrijft meerdere waarden aan Queue opslag.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage en HttpResponseMessage

 Deze zijn gedefinieerd `azure-functions-java-library`in . Het zijn helpertypen om met httptrigger-functies te werken.

| Gespecialiseerd type      |       Doel        | Normaal gebruik                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Hiermee krijgt u methode, kopteksten of query's |
| `HttpResponseMessage` | HTTP-uitvoerbinding | Retourneert andere status dan 200   |

## <a name="metadata"></a>Metagegevens

Weinig triggers verzenden [trigger metadata](/azure/azure-functions/functions-triggers-bindings) samen met invoergegevens. U annotatie `@BindingName` gebruiken om te binden om metagegevens te activeren.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
In het voorgaande `queryValue` voorbeeld is de parameter `name` gebonden aan de `http://{example.host}/api/metadata?name=test`querytekenreeks parameter in de HTTP-aanvraag-URL. Hier is nog een voorbeeld, `Id` waarin wordt weergegeven hoe u binden aan metagegevens van wachtrijtrigger.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> De naam in de annotatie moet overeenkomen met de eigenschap metadata.

## <a name="execution-context"></a>Context voor uitvoering

`ExecutionContext`, gedefinieerd in `azure-functions-java-library`de , bevat helper methoden om te communiceren met de functies runtime.

### <a name="logger"></a>Logger

Gebruik `getLogger`, gedefinieerd `ExecutionContext`in , om logboeken te schrijven van functiecode.

Voorbeeld:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Logboeken en traceweeren weergeven

U de Azure CLI gebruiken om Java-stdout- en stderr-logboekregistratie en andere logboekregistratie van toepassingen te streamen. 

U als u uw functie-app zo configureren dat u toepassingslogboekregistraties schrijft met azure cli:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Als u logboekregistratieuitvoer voor uw functie-app wilt streamen met de Azure CLI, opent u een nieuwe opdrachtprompt, Bash of Terminal-sessie en voert u de volgende opdracht in:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
De [az webapp log tail](/cli/azure/webapp/log) opdracht heeft `--provider` opties om de uitvoer te filteren met behulp van de optie. 

Als u de logboekbestanden als één ZIP-bestand wilt downloaden met de Azure CLI, opent u een nieuwe opdrachtprompt, Bash of Terminal-sessie en voert u de volgende opdracht in:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

U moet de logboekregistratie van het bestandssysteem hebben ingeschakeld in de Azure-portal of de Azure CLI voordat deze opdracht wordt uitgevoerd.

## <a name="environment-variables"></a>Omgevingsvariabelen

In Functies worden [app-instellingen](functions-app-settings.md), zoals serviceverbindingstekenreeksen, tijdens de uitvoering weergegeven als omgevingsvariabelen. U deze instellingen `System.getenv("AzureWebJobsStorage")`openen via.

In het volgende voorbeeld wordt de `myAppSetting` [toepassingsinstelling](functions-how-to-use-azure-function-app-settings.md#settings)met de sleutel met de naam :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over de ontwikkeling van Azure Functions Java:

* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* Lokale ontwikkeling en debug genfout met [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), en [Eclipse](functions-create-maven-eclipse.md)
* [Externe foutopsporing Java Azure-functies met Visual Studio-code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-plug-in voor Azure-functies](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Stroomlijn het maken `azure-functions:add` van functies via het doel en bereid een map voor met fasering voor [de implementatie van ZIP-bestanden.](deployment-zip-push.md)
