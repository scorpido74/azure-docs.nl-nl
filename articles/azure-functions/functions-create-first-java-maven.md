---
title: Java en Maven gebruiken voor het publiceren van een functie-Azure Functions
description: Lees hier hoe u een eenvoudige, door HTTP getriggerde functie maakt en publiceert naar Azure met Java en Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure-functies, functies, gebeurtenisverwerking, berekenen, architectuur zonder server
ms.service: azure-functions
ms.topic: quickstart
ms.devlang: java
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5447fdcfa86c35b7c5cf079ae8446c30785e893f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299402"
---
# <a name="quickstart-use-java-to-create-and-publish-a-function-to-azure-functions"></a>Quickstart: Java gebruiken om een functie te maken en te publiceren in Azure Functions

In dit artikel wordt beschreven hoe u een Java-functie bouwt en publiceert op Azure functions met het opdracht regel programma maven. Wanneer u klaar bent, wordt uw functiecode uitgevoerd in het [Verbruiksabonnement](functions-scale.md#consumption-plan) in Azure en kan deze worden geactiveerd met behulp van een HTTP-aanvraag.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](https://aka.ms/azure-jdks), versie 8
- [Apache Maven](https://maven.apache.org), versie 3,0 of hoger
- [Azure-CLI](https://docs.microsoft.com/cli/azure)
- [Azure functions core tools](./functions-run-local.md#v2) versie 2.6.666 of hoger

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.

## <a name="generate-a-new-functions-project"></a>Een nieuw Functions-project genereren

Voer in een lege map de volgende opdracht uit om het Functions-project te genereren op basis van een [Maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Als u problemen ondervindt met het uitvoeren van de opdracht, bekijkt `maven-archetype-plugin` u de versie die wordt gebruikt. Omdat u de opdracht uitvoert in een lege map zonder `.pom` bestand, probeert deze mogelijk een invoeg toepassing van de oudere `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` versie te gebruiken als u een upgrade hebt uitgevoerd van uw maven van een oudere versie. Als dit het geval is, `maven-archetype-plugin` verwijdert u de map en voert u de opdracht opnieuw uit.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

U wordt door Maven gevraagd om de waarden die nodig zijn om het project te kunnen genereren. Informatie over de waarden voor _groupId_, _artifactId_ en _version_ kunt u vinden in de Engelstalige [naslag van Maven over naamconventies](https://maven.apache.org/guides/mini/guide-naming-conventions.html). De waarde voor _appName_ moet uniek zijn binnen Azure. Om die reden genereert Maven standaard een app-naam op basis van de eerder opgegeven waarde voor _artifactId_. De waarde voor _packageName_ bepaalt het Java-pakket voor de gegenereerde functiecode.

De id's `com.fabrikam.functions` en `fabrikam-functions` hieronder worden gebruikt als een voorbeeld en om latere stappen in deze snelstart makkelijker te kunnen lezen. In deze stap wordt u aangeraden om Maven van uw eigen waarden te voorzien.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

Maven maakt de projectbestanden in een nieuwe map met de naam _artifactId_; in dit voorbeeld `fabrikam-functions`. De gereed voor uitvoering van gegenereerde code in het project is een door [http geactiveerde](/azure/azure-functions/functions-bindings-http-webhook) functie die de hoofd tekst van de aanvraag echoert. Vervang *src/main/Java/com/fabrikam/functions. java* door de volgende code: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java
     * 2. curl {your host}/api/HttpTrigger-Java?name=HTTP%20Query
     */
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Wijzig de map naar de zojuist gemaakte projectmap (de map met uw host. json-en Pom. XML-bestanden) en bouw en voer de functie uit met maven:

```CMD
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Als u deze uitzondering krijgt: `javax.xml.bind.JAXBException` met Java 9, bekijkt u de tijdelijke oplossing op [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

U ziet deze uitvoer als de functie lokaal op uw systeem wordt uitgevoerd en klaar is om op HTTP-aanvragen te reageren:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/HttpTrigger-Java
```

Activeer de functie vanaf de opdrachtregel met de opdracht curl in een nieuw terminalvenster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d LocalFunction
```

```Output
Hello LocalFunction!
```

Gebruik `Ctrl-C` in de terminal om de functiecode te stoppen.

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Bij het implementeren naar Azure Functions worden accountreferenties uit de Azure CLI gebruikt. [Meld u aan met de Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voordat u doorgaat.

```azurecli
az login
```

Implementeer de code in een nieuwe functie-app met behulp van de Maven-target `azure-functions:deploy`. Hiermee wordt een [zip-implementatie uitgevoerd met uitvoeren vanuit pakket](functions-deployment-technologies.md#zip-deploy) modus ingeschakeld.

> [!NOTE]
> Wanneer u Visual Studio code gebruikt om uw functie-app te implementeren, moet u een niet-gratis abonnement kiezen of wordt er een fout melding weer gegeven. U kunt uw abonnement aan de linkerkant van de IDE bekijken.

```azurecli
mvn azure-functions:deploy
```

Als het implementeren is voltooid, ziet u de URL die u kunt gebruiken voor toegang tot de Azure-functie-app:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Test de functie-app in Azure met behulp van `cURL`. Wijzig de URL in onderstaand voorbeeld om deze overeen te laten komen met de geïmplementeerde URL voor uw eigen functie-app uit de vorige stap.

> [!NOTE]
> Zorg ervoor dat u de **toegangs rechten** instelt `Anonymous`op. Wanneer u het standaard niveau van `Function`kiest, moet u de [functie sleutel](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) in aanvragen voor toegang tot uw functie-eind punt presen teren.

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Wijzigingen maken en opnieuw implementeren

Bewerk het bronbestand `src/main.../Function.java` in het gegenereerde project om de tekst te wijzigen die is geretourneerd met de functie-app. Wijzig deze regel:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
```

In het volgende:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hi, " + name).build();
```

Sla de wijzigingen op. Voer het schone pakket MVN uit en implementeer het `azure-functions:deploy` opnieuw door vanaf de terminal als voorheen uit te voeren. De functie-app wordt bijgewerkt, en deze aanvraag:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Heeft deze bijgewerkte uitvoer:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-functie-app gemaakt met een eenvoudige HTTP-trigger en deze geïmplementeerd naar Azure Functions.

- Neem de [Azure Functions Java-handleiding voor ontwikkelaars](functions-reference-java.md) door voor meer informatie over het ontwikkelen van Java-functies. Deze handleiding is vooralsnog door een vertaalmachine vertaald.
- U kunt extra functies met verschillende triggers toevoegen aan uw project met behulp van de Maven-target `azure-functions:add`.
- Gebruik [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) en [Eclipse](functions-create-maven-eclipse.md) om functies lokaal te schrijven en fouten op te sporen. 
- Gebruik Visual Studio Code om fouten op te sporen in functies die zijn geïmplementeerd in Azure. Raadpleeg de Visual Studio Code-documentatie over [serverloze Java-toepassingen](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) voor instructies.

> [!NOTE] 
> U kunt ook een Azure Functions project op basis van Kotlin maken met behulp van Azure-functions-Kotlin-Archetype in plaats daarvan. Ga naar de [github-opslag plaats](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) voor meer informatie.
