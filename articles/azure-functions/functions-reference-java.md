---
title: Naslag informatie voor Java-Ontwikkel aars voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 339615ac99f231fd293a7ea15c853d43da8f998a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057599"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java-ontwikkelaars handleiding

De Azure Functions-runtime ondersteunt [Java SE 8 LTS (Zulu 8.31.0.2-jre 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Deze hand leiding bevat informatie over de complexiteit voor het schrijven van Azure Functions met Java.

Wat er gebeurt met andere talen, kan een functie-app een of meer functies hebben. Een Java-functie is een `public` methode die is gedecoreerd met de aantekening `@FunctionName` . Deze methode definieert de vermelding voor een Java-functie en moet uniek zijn in een bepaald pakket. Een functie-app geschreven in Java kan meerdere klassen hebben met meerdere open bare methoden die aantekeningen hebben `@FunctionName` .

In dit artikel wordt ervan uitgegaan dat u de [Azure functions Naslag informatie voor ontwikkel aars](functions-reference.md)al hebt gelezen. U moet ook een van de volgende Quick starts voor functies volt ooien: [Maak uw eerste Java-functie met Visual Studio code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) of [Maak uw eerste Java-functie vanaf de opdracht regel met behulp van Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Programmeermodel 

De concepten van [Triggers en bindingen](functions-triggers-bindings.md) zijn fundamenteel voor Azure functions. Triggers starten de uitvoering van uw code. Bindingen bieden een manier om gegevens door te geven aan en gegevens van een functie te retour neren zonder dat u aangepaste gegevens toegangs code hoeft te schrijven.

## <a name="create-java-functions"></a>Java-functies maken

Om het maken van Java-functies te vereenvoudigen, zijn er maven hulp middelen en archetypes die gebruikmaken van vooraf gedefinieerde Java-sjablonen die u helpen bij het maken van projecten met een specifieke functie trigger.    

### <a name="maven-based-tooling"></a>Op maven gebaseerd hulp programma

De volgende omgevingen met ontwikkel aars hebben Azure Functions hulp middelen waarmee u Java-functie projecten kunt maken: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

De bovenstaande artikel koppelingen laten zien hoe u uw eerste functies kunt maken met behulp van uw IDE-keuze. 

### <a name="project-scaffolding"></a>Project steigers

Als u de voor keur geeft aan de opdracht regel ontwikkeling van de Terminal, is de eenvoudigste manier om op Java gebaseerde functie projecten op basis van een archetypes te gebruiken `Apache Maven` . De Java maven archetype voor Azure Functions wordt gepubliceerd onder de volgende _groupid_:_artifactId_: [com. micro soft. Azure: Azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Met de volgende opdracht wordt een nieuw Java-functie project gegenereerd met behulp van deze Archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Zie [Java Quick](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)start om aan de slag te gaan met deze archetype. 

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

U kunt een gedeelde [host.jsvoor](functions-host-json.md) het bestand gebruiken om de functie-app te configureren. Elke functie heeft een eigen code bestand (. java) en een bindings configuratie bestand (function.jsaan).

U kunt meer dan één functie in een project opnemen. Vermijd het plaatsen van uw functies in afzonderlijke potten. De `FunctionApp` in de doelmap is wat wordt geïmplementeerd in uw functie-app in Azure.

## <a name="triggers-and-annotations"></a>Triggers en aantekeningen

 Functies worden aangeroepen door een trigger, zoals een HTTP-aanvraag, een timer of een update van gegevens. De functie moet deze trigger en alle andere invoer bewerkingen uitvoeren om een of meer uitvoer te maken.

Gebruik de Java-aantekeningen die zijn opgenomen in het pakket [com. micro soft. Azure. functions. annotatie. *](/java/api/com.microsoft.azure.functions.annotation) om invoer en uitvoer aan uw methoden te binden. Zie [Java Reference docs](/java/api/com.microsoft.azure.functions.annotation)(Engelstalig) voor meer informatie.

> [!IMPORTANT] 
> U moet een Azure Storage-account configureren in uw [local.settings.js](/azure/azure-functions/functions-run-local#local-settings-file) om Azure Blob-opslag, Azure Queue-opslag of Azure-tabel opslag lokaal uit te voeren.

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

Dit is de gegenereerde corresponderende `function.json` met de [Azure-functions-maven-invoeg toepassing](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

[Ondersteuning voor Azure](https://azure.microsoft.com/support/) voor problemen met de JDKs-en functie-apps is beschikbaar in een [ondersteunings abonnement](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>JVM aanpassen

Met functies kunt u de Java Virtual Machine (JVM) aanpassen die wordt gebruikt om uw Java-functies uit te voeren. De [volgende JVM-opties](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) worden standaard gebruikt:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

U kunt aanvullende argumenten opgeven in een app-instelling met de naam `JAVA_OPTS` . U kunt app-instellingen toevoegen aan de functie-app die is geïmplementeerd in Azure in de Azure Portal of de Azure CLI.

> [!IMPORTANT]  
> In het verbruiks plan moet u ook de instelling WEBSITE_USE_PLACEHOLDER toevoegen met de waarde 0 voordat de aanpassing werkt. Met deze instelling worden de koude start tijden voor Java-functies verhoogd.

### <a name="azure-portal"></a>Azure Portal

Gebruik het [tabblad toepassings instellingen](functions-how-to-use-azure-function-app-settings.md#settings) In het [Azure Portal](https://portal.azure.com)om de instelling toe te voegen `JAVA_OPTS` .

### <a name="azure-cli"></a>Azure CLI

U kunt de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) gebruiken om in te stellen `JAVA_OPTS` , zoals in het volgende voor beeld:

#### <a name="consumption-plan"></a>[Verbruiks abonnement](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[Speciaal plan/Premium-abonnement](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

In dit voor beeld wordt de modus headless ingeschakeld. Vervang door `<APP_NAME>` de naam van uw functie-app en `<RESOURCE_GROUP>` met de resource groep. 

## <a name="third-party-libraries"></a>Bibliotheken van derden 

Azure Functions ondersteunt het gebruik van bibliotheken van derden. Standaard worden alle afhankelijkheden die zijn opgegeven in het project `pom.xml` bestand, automatisch gebundeld tijdens het [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) doel. Voor bibliotheken die niet als afhankelijkheden in het `pom.xml` bestand zijn opgegeven, plaatst u deze in een `lib` map in de hoofdmap van de functie. Afhankelijkheden die in de `lib` map worden geplaatst, worden tijdens runtime toegevoegd aan het laad programma systeem klasse.

De `com.microsoft.azure.functions:azure-functions-java-library` afhankelijkheid wordt standaard opgegeven in het klassenpad en hoeft niet te worden opgenomen in de `lib` map. [Azure-functions-Java-worker](https://github.com/Azure/azure-functions-java-worker) voegt ook afhankelijkheden toe die [hier](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) worden vermeld voor het klassenpad.

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

U kunt onbewerkte oude Java-objecten (Pojo's), typen `azure-functions-java-library` die zijn gedefinieerd in, of primitieve gegevens typen, zoals teken reeks en geheel getal, gebruiken om te binden aan invoer-of uitvoer bindingen.

### <a name="pojos"></a>Pojo's

Voor het converteren van invoer gegevens naar POJO maakt [Azure-functions-Java-worker](https://github.com/Azure/azure-functions-java-worker) gebruik van de [gson](https://github.com/google/gson) -bibliotheek. POJO-typen die worden gebruikt als invoer voor functions, moeten zijn `public` .

### <a name="binary-data"></a>Binaire gegevens

BIND binaire invoer of uitvoer naar `byte[]` , door het `dataType` veld in uw function.jsin te stellen op `binary` :

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

Als u null-waarden verwacht, gebruikt u `Optional<T>` .

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
- Payload van HTTP-aanvraag is door gegeven als een `String` voor het argument `inputReq` .
- Er wordt één vermelding opgehaald uit de tabel opslag en wordt door gegeven `TestInputData` aan het argument `inputData` .

Als u een batch invoer wilt ontvangen, kunt u een binding maken met `String[]` ,, `POJO[]` `List<String>` of `List<POJO>` .

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

Deze functie wordt geactiveerd wanneer er nieuwe gegevens in de geconfigureerde Event Hub zijn. Omdat de `cardinality` is ingesteld op `MANY` , ontvangt de functie een batch berichten van de Event hub. `EventData`van Event Hub wordt geconverteerd naar `TestEventData` voor het uitvoeren van de functie.

### <a name="output-binding-example"></a>Voor beeld van uitvoer binding

U kunt een uitvoer binding binden aan de retour waarde met behulp van `$return` . 

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

Als u meerdere uitvoer waarden wilt verzenden, gebruikt u `OutputBinding<T>` gedefinieerd in het `azure-functions-java-library` pakket. 

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

 Deze zijn gedefinieerd in `azure-functions-java-library` . Dit zijn de typen hulp voor het werken met http trigger-functies.

| Gespecialiseerd type      |       Doel        | Typisch gebruik                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-trigger     | Hiermee wordt een methode, koptekst of query opgehaald |
| `HttpResponseMessage` | HTTP-uitvoer binding | Retourneert een andere status dan 200   |

## <a name="metadata"></a>Metagegevens

Met weinig triggers worden [meta gegevens van triggers](/azure/azure-functions/functions-triggers-bindings) samen met invoer gegevens verzonden. U kunt aantekening gebruiken `@BindingName` om de meta gegevens van de trigger te binden.


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
In het vorige voor beeld `queryValue` is de is gebonden aan de query teken reeks parameter `name` in de URL van de HTTP-aanvraag `http://{example.host}/api/metadata?name=test` . Hier volgt nog een voor beeld, waarin wordt weer gegeven hoe u kunt binden aan de `Id` meta gegevens van de wachtrij trigger.

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

`ExecutionContext`, gedefinieerd in de `azure-functions-java-library` , bevat helper-methoden om te communiceren met de functions-runtime. Zie het [ExecutionContext-referentie artikel](/java/api/com.microsoft.azure.functions.executioncontext)voor meer informatie.

### <a name="logger"></a>Logger

Gebruik `getLogger` , gedefinieerd in `ExecutionContext` , om logboeken van de functie code te schrijven.

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
De opdracht [AZ webapp log staart](/cli/azure/webapp/log) bevat opties voor het filteren van uitvoer met behulp van de `--provider` optie. 

Als u de logboek bestanden wilt downloaden als één ZIP-bestand met behulp van de Azure CLI, opent u een nieuwe opdracht prompt, bash of terminal sessie en voert u de volgende opdracht in:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

U moet logboek registratie van het bestands systeem inschakelen in de Azure Portal of Azure CLI voordat u deze opdracht uitvoert.

## <a name="environment-variables"></a>Omgevingsvariabelen

In functions worden [app-instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen met behulp van `System.getenv("AzureWebJobsStorage")` .

In het volgende voor beeld wordt de [toepassings instelling](functions-how-to-use-azure-function-app-settings.md#settings)opgehaald met de sleutel met de naam `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> De waarde van AppSetting FUNCTIONS_EXTENSION_VERSION moet ~ 2 of ~ 3 zijn voor een geoptimaliseerde koud start-ervaring.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het ontwikkelen van Java Azure Functions:

* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
* Lokale ontwikkeling en fout opsporing met [Visual Studio code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)en [eclips](functions-create-maven-eclipse.md)
* [Java-functies voor fout opsporing op afstand met Visual Studio code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-invoeg toepassing voor Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Het maken van functies stroom lijnen met behulp `azure-functions:add` van het doel en voorbereiden van een staging-directory voor de [implementatie van zip-bestanden](deployment-zip-push.md).
