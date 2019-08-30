---
title: Naslag informatie voor Java-Ontwikkel aars voor Azure Functions | Microsoft Docs
description: Meer informatie over het ontwikkelen van functies met Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, functies, gebeurtenis verwerking, webhooks, dynamische compute, serverloze architectuur, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: aea1434acdbfd97bcc9096dddd497ef031a74b94
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170548"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java-ontwikkelaars handleiding

De Azure Functions-runtime ondersteunt [Java SE 8 LTS (Zulu 8.31.0.2-jre 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Deze hand leiding bevat informatie over de complexiteit voor het schrijven van Azure Functions met Java.

Een Java-functie is `public` een methode die is gedecoreerd `@FunctionName`met de aantekening. Deze methode definieert de vermelding voor een Java-functie en moet uniek zijn in een bepaald pakket. 

In dit artikel wordt ervan uitgegaan dat u de [Azure functions Naslag informatie voor ontwikkel aars](functions-reference.md)al hebt gelezen. U moet ook de Snelstartgids van functions volt ooien om uw eerste functie te maken met behulp van [Visual Studio code](functions-create-first-function-vs-code.md) of [maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Programmeermodel 

De concepten van [Triggers en bindingen](functions-triggers-bindings.md) zijn fundamenteel voor Azure functions. Triggers starten de uitvoering van uw code. Bindingen bieden een manier om gegevens door te geven aan en gegevens van een functie te retour neren zonder dat u aangepaste gegevens toegangs code hoeft te schrijven.

## <a name="folder-structure"></a>Mapstructuur

Hier volgt de mapstructuur van een Azure Functions Java-project:

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

U kunt een gedeeld [host. json](functions-host-json.md) -bestand gebruiken om de functie-app te configureren. Elke functie heeft een eigen code bestand (. java) en een bindings configuratie bestand (function. json).

U kunt meer dan één functie in een project opnemen. Vermijd het plaatsen van uw functies in afzonderlijke potten. De `FunctionApp` in de doelmap is wat wordt geïmplementeerd in uw functie-app in Azure.

## <a name="triggers-and-annotations"></a>Triggers en aantekeningen

 Functies worden aangeroepen door een trigger, zoals een HTTP-aanvraag, een timer of een update van gegevens. De functie moet deze trigger en alle andere invoer bewerkingen uitvoeren om een of meer uitvoer te maken.

Gebruik de Java-aantekeningen die zijn opgenomen in het pakket [com. micro soft. Azure. functions. annotatie. *](/java/api/com.microsoft.azure.functions.annotation) om invoer en uitvoer aan uw methoden te binden. Zie [Java Reference docs](/java/api/com.microsoft.azure.functions.annotation)(Engelstalig) voor meer informatie.

> [!IMPORTANT] 
> U moet een Azure Storage-account configureren in uw [lokale. settings. json](/azure/azure-functions/functions-run-local#local-settings-file) om Azure Blob-opslag, Azure Queue-opslag of Azure Table Storage lokaal uit te voeren.

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

Dit is de gegenereerde `function.json` corresponderende met de [Azure-functions-maven-invoeg toepassing](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="jdk-runtime-availability-and-support"></a>Beschik baarheid en ondersteuning van JDK-runtime 

Voor lokale ontwikkeling van Java-functie-apps downloadt en gebruikt u de [Azul Zulu Enter prise voor Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs van [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions maakt gebruik van de Azul Java 8 JDK wanneer u uw functie-apps in de Cloud implementeert.

[Ondersteuning voor Azure](https://azure.microsoft.com/support/) voor problemen met de JDKs-en functie-apps is beschikbaar in een ondersteunings [abonnement](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>JVM aanpassen

Met functies kunt u de Java Virtual Machine (JVM) aanpassen die wordt gebruikt om uw Java-functies uit te voeren. De [volgende JVM-opties](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) worden standaard gebruikt:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

U kunt aanvullende argumenten opgeven in een app-instelling `JAVA_OPTS`met de naam. U kunt app-instellingen toevoegen aan de functie-app die is geïmplementeerd in Azure in de Azure Portal of de Azure CLI.

### <a name="azure-portal"></a>Azure Portal

Gebruik het [tabblad toepassings instellingen](functions-how-to-use-azure-function-app-settings.md#settings) in het `JAVA_OPTS` [Azure Portal](https://portal.azure.com)om de instelling toe te voegen.

### <a name="azure-cli"></a>Azure-CLI

U kunt de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) gebruiken om in `JAVA_OPTS`te stellen, zoals in het volgende voor beeld:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
In dit voor beeld wordt de modus headless ingeschakeld. Vervang `<APP_NAME>` door de naam van uw functie-app en `<RESOURCE_GROUP>` met de resource groep.

> [!WARNING]  
> In het [verbruiks plan](functions-scale.md#consumption-plan)moet u de instelling `WEBSITE_USE_PLACEHOLDER` toevoegen `0`met de waarde.  
Met deze instelling worden de koude start tijden voor Java-functies verhoogd.

## <a name="third-party-libraries"></a>Bibliotheken van derden 

Azure Functions ondersteunt het gebruik van bibliotheken van derden. Standaard worden alle afhankelijkheden die zijn opgegeven in `pom.xml` het project bestand, automatisch gebundeld tijdens het [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) doel. Voor bibliotheken die niet als afhankelijkheden in `pom.xml` het bestand zijn opgegeven, plaatst `lib` u deze in een map in de hoofdmap van de functie. Afhankelijkheden die in `lib` de map worden geplaatst, worden tijdens runtime toegevoegd aan het laad programma systeem klasse.

De `com.microsoft.azure.functions:azure-functions-java-library` afhankelijkheid wordt standaard opgegeven in het klassenpad en hoeft niet te worden opgenomen in de `lib` map. [Azure-functions-Java-worker](https://github.com/Azure/azure-functions-java-worker) voegt ook afhankelijkheden toe die [hier](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) worden vermeld voor het klassenpad.

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

U kunt onbewerkte oude Java-objecten (pojo's), typen `azure-functions-java-library`die zijn gedefinieerd in, of primitieve gegevens typen, zoals teken reeks en geheel getal, gebruiken om te binden aan invoer-of uitvoer bindingen.

### <a name="pojos"></a>Pojo's

Voor het converteren van invoer gegevens naar POJO maakt [Azure-functions-Java-worker](https://github.com/Azure/azure-functions-java-worker) gebruik van de [gson](https://github.com/google/gson) -bibliotheek. POJO-typen die worden gebruikt als invoer voor `public`functions, moeten zijn.

### <a name="binary-data"></a>Binaire gegevens

BIND binaire invoer of uitvoer naar `byte[]`, door het `dataType` veld in uw function. json in te stellen `binary`op:

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

Als u null-waarden verwacht, `Optional<T>`gebruikt u.

## <a name="bindings"></a>Bindingen

Invoer-en uitvoer bindingen bieden een declaratieve manier om verbinding te maken met gegevens vanuit uw code. Een functie kan meerdere invoer-en uitvoer bindingen hebben.

### <a name="input-binding-example"></a>Voor beeld van invoer binding

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

U kunt deze functie aanroepen met een HTTP-aanvraag. 
- Payload van HTTP-aanvraag is door `String` gegeven als een `inputReq`voor het argument.
- Er wordt één vermelding opgehaald uit de tabel opslag en wordt door gegeven `TestInputData` aan het argument `inputData`.

Als u een batch invoer wilt ontvangen, kunt u een `String[]`binding `POJO[]`maken `List<String>`met, `List<POJO>`, of.

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

Deze functie wordt geactiveerd wanneer er nieuwe gegevens in de geconfigureerde Event Hub zijn. Omdat de `cardinality` is ingesteld op `MANY`, ontvangt de functie een batch berichten van de Event hub. `EventData`van Event hub wordt geconverteerd naar `TestEventData` voor het uitvoeren van de functie.

### <a name="output-binding-example"></a>Voor beeld van uitvoer binding

U kunt een uitvoer binding binden aan de retour waarde met behulp `$return`van. 

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

Als er meerdere uitvoer bindingen zijn, gebruikt u de retour waarde voor slechts één van beide.

Als u meerdere uitvoer waarden wilt verzenden `OutputBinding<T>` , gebruikt u `azure-functions-java-library` gedefinieerd in het pakket. 

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

U kunt deze functie aanroepen op een HttpRequest. Er worden meerdere waarden naar de wachtrij opslag geschreven.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage en HttpResponseMessage

 Deze zijn gedefinieerd in `azure-functions-java-library`. Dit zijn de typen hulp voor het werken met http trigger-functies.

| Gespecialiseerd type      |       Doel        | Typisch gebruik                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Hiermee wordt een methode, koptekst of query opgehaald |
| `HttpResponseMessage` | HTTP-uitvoer binding | Retourneert een andere status dan 200   |

## <a name="metadata"></a>Metagegevens

Met weinig triggers worden [meta gegevens van triggers](/azure/azure-functions/functions-triggers-bindings) samen met invoer gegevens verzonden. U kunt aantekening `@BindingName` gebruiken om de meta gegevens van de trigger te binden.


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
In het vorige voor beeld is `queryValue` de is gebonden aan de query teken `name` reeks parameter in `http://{example.host}/api/metadata?name=test`de URL van de HTTP-aanvraag. Hier volgt nog een voor beeld, waarin wordt weer `Id` gegeven hoe u kunt binden aan de meta gegevens van de wachtrij trigger.

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
> De naam die in de aantekening wordt opgegeven, moet overeenkomen met de meta gegevens eigenschap.

## <a name="execution-context"></a>Context voor uitvoering

`ExecutionContext`, gedefinieerd in de `azure-functions-java-library`, bevat helper-methoden om te communiceren met de functions-runtime.

### <a name="logger"></a>Logger

Gebruik `getLogger`, gedefinieerd in `ExecutionContext`, om logboeken van de functie code te schrijven.

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

## <a name="view-logs-and-trace"></a>Logboeken en traceringen weer geven

U kunt de Azure CLI gebruiken voor het streamen van Java stdout-en stderr-logboek registratie en andere toepassings Logboeken. 

U kunt als volgt uw functie-app configureren om toepassings logboeken te schrijven met behulp van Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Als u de uitvoer van logboek registratie voor uw functie-app wilt streamen met behulp van de Azure CLI, opent u een nieuwe opdracht prompt, bash of terminal sessie en voert u de volgende opdracht in:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
De opdracht [AZ webapp log staart](/cli/azure/webapp/log) bevat opties voor het filteren van uitvoer met `--provider` behulp van de optie. 

Als u de logboek bestanden wilt downloaden als één ZIP-bestand met behulp van de Azure CLI, opent u een nieuwe opdracht prompt, bash of terminal sessie en voert u de volgende opdracht in:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

U moet logboek registratie van het bestands systeem inschakelen in de Azure Portal of Azure CLI voordat u deze opdracht uitvoert.

## <a name="environment-variables"></a>Omgevingsvariabelen

In functions worden [app-instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen met behulp `System.getenv("AzureWebJobsStorage")`van.

In het volgende voor beeld wordt de [toepassings instelling](functions-how-to-use-azure-function-app-settings.md#settings)opgehaald met de `myAppSetting`sleutel met de naam:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het ontwikkelen van Java Azure Functions:

* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* Lokale ontwikkeling en fout opsporing met [Visual Studio code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)en [eclips](functions-create-maven-eclipse.md)
* [Java-Azure Functions voor fout opsporing op afstand met Visual Studio code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-invoeg toepassing voor Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Het maken van functies stroom lijnen `azure-functions:add` met behulp van het doel en voorbereiden van een staging-directory voor de [implementatie van zip-bestanden](deployment-zip-push.md).
