---
title: Java en Maven/Gradle gebruiken om een functie naar Azure te publiceren
description: Een HTTP-geactiveerde functie maken en publiceren naar Azure met Java en Maven of Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d0f30272a4e605449c946ca402db4f3ba00735bc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520544"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Snelstart: Gebruik Java en Maven/Gradle om een functie te maken en te publiceren naar Azure

In dit artikel ziet u hoe u een Java-functie bouwen en publiceren naar Azure Functions met het opdrachtregelhulpprogramma Maven/Gradle. Wanneer u klaar bent, wordt uw functiecode uitgevoerd in Azure in een [serverloos hostingplan](functions-scale.md#consumption-plan) en wordt geactiveerd door een HTTP-aanvraag.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](https://aka.ms/azure-jdks), versie 8
- [Azure-CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versie 2.6.666 of hoger
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org), versie 3.0 of hoger
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), versie 4.10 en hoger
::: zone-end 

U hebt ook een actief Azure-abonnement nodig. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.

## <a name="prepare-a-functions-project"></a>Een functieproject voorbereiden

::: zone pivot="java-build-tools-maven" 
Voer in een lege map de volgende opdracht uit om het Functions-project te genereren op basis van een [Maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Als u Powershell gebruikt, vergeet dan niet om "" toe te voegen rond parameters.

> [!NOTE]
> Als u problemen ondervindt bij het uitvoeren van `maven-archetype-plugin` de opdracht, bekijkt u welke versie wordt gebruikt. Omdat u de opdracht uitvoert in `.pom` een lege map zonder bestand, probeert u `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` mogelijk een plug-in van de oudere versie te gebruiken vanaf het punt als u uw Maven hebt geüpgraded van een oudere versie. Als dat het zo `maven-archetype-plugin` is, probeert u de map te verwijderen en de opdracht opnieuw uit te voeren.

Maven vraagt u om waarden die nodig zijn om het genereren van het project op implementatie te voltooien. Geef de volgende waarden op wanneer daarom wordt gevraagd:

| Waarde | Beschrijving |
| ----- | ----------- |
| **groupId** | Een waarde die uw project op unieke wijze identificeert voor alle projecten, volgens de regels voor [pakketnaamgeving](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. De voorbeelden in deze `com.fabrikam.functions`quickstart gebruiken . |
| **artefactId** | Een waarde die de naam van de pot is, zonder versienummer. De voorbeelden in deze `fabrikam-functions`quickstart gebruiken . |
| **Versie** | Kies de standaardwaarde van `1.0-SNAPSHOT`. |
| **Pakket** | Een waarde die het Java-pakket is voor de gegenereerde functiecode. Gebruik de standaard. De voorbeelden in deze `com.fabrikam.functions`quickstart gebruiken . |

Typ `Y` of druk op Enter om dit te bevestigen.

Maven maakt de projectbestanden in een nieuwe map met een `fabrikam-functions`naam van **artefactId**, die in dit voorbeeld is . Voer de volgende opdracht uit om de map te wijzigen in de gemaakte projectmap.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
Gebruik de volgende opdracht om het voorbeeldproject te klonen:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Open `build.gradle` en `appName` wijzig de volgende sectie in een unieke naam om conflicten met domeinnamen te voorkomen bij het implementeren naar Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```
::: zone-end

Open het nieuwe Function.java-bestand vanaf het *src/main/java-pad* in een teksteditor en bekijk de gegenereerde code. Deze code is een [HTTP-geactiveerde](functions-bindings-http-webhook.md) functie die de hoofdtekst van het verzoek weerkaatst. 

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer de volgende opdracht uit om te bouwen en voer het functieproject uit:

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

U ziet uitvoer zoals de volgende van Azure Functions Core Tools wanneer u het project lokaal uitvoert:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Activeer de functie vanaf de opdrachtregel met behulp van cURL in een nieuw terminalvenster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
De [functiesleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) is niet vereist wanneer u lokaal wordt uitgevoerd. Gebruik `Ctrl+C` in de terminal om de functiecode te stoppen.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Een functie-app en gerelateerde resources worden gemaakt in Azure wanneer u uw functie-app voor het eerst implementeert. Voordat u implementeren, gebruikt u de opdracht Azure CLI voor [AZ-aanmelding](/cli/azure/authenticate-azure-cli) om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

> [!TIP]
> Als uw account toegang heeft tot meerdere abonnementen, gebruikt u [het AZ-account dat is ingesteld](/cli/azure/account#az-account-set) om het standaardabonnement voor deze sessie in te stellen. 

Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app. 


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

Hiermee worden de volgende bronnen in Azure gemaakt:

+ Resourcegroep. Benoemd met de _resourceGroup die_ u hebt geleverd.
+ Opslagaccount. Vereist door Functies. De naam wordt willekeurig gegenereerd op basis van de vereisten voor de naam van het opslagaccount.
+ App-serviceplan. Serverloze hosting voor uw functie-app in de opgegeven _appRegio_. De naam wordt willekeurig gegenereerd.
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam is uw _appName_, toegevoegd met een willekeurig gegenereerd nummer. 

De implementatie verpakt ook de projectbestanden en implementeert deze naar de nieuwe functie-app met [zip-implementatie](functions-deployment-technologies.md#zip-deploy), met run-from-package-modus ingeschakeld.

Nadat de implementatie is voltooid, ziet u de URL die u gebruiken om toegang te krijgen tot de eindpunten van uw functie-app. Omdat de HTTP-trigger `authLevel = AuthorizationLevel.FUNCTION`die we hebben gepubliceerd, wordt gebruikt, moet u de functiesleutel krijgen om het functieeindpunt over HTTP aan te roepen. De eenvoudigste manier om de functiesleutel te krijgen, is via de [Azure-portal.]

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>De HTTP-trigger-URL ophalen

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

U de URL die nodig is om uw functie te activeren, met de functiesleutel, ophalen via de Azure-portal. 

1. Blader naar de [Azure-portal,]meld u aan, typ de _appName_ van uw functie-app in **Zoeken** boven aan de pagina en druk op Enter.
 
1. Vouw in uw functie-app Functies uit **(Alleen lezen)** uit, kies uw functie en selecteer **vervolgens</> Functie-URL** ophalen rechtsboven. 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-java-maven/get-function-url-portal.png)

1. Kies **standaard (functiesleutel)** en selecteer **Kopiëren**. 

U nu de gekopieerde URL gebruiken om toegang te krijgen tot uw functie.

## <a name="verify-the-function-in-azure"></a>De functie in Azure verifiëren

Als u de functie-app `cURL`die op Azure wordt uitgevoerd met behulp van , wilt controleren, vervangt u de URL uit het onderstaande voorbeeld door de URL die u van de portal hebt gekopieerd.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Hiermee wordt een POST-verzoek naar `AzureFunctions` het functieeindpunt met in de hoofdtekst van de aanvraag. U ziet het volgende antwoord.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-functieproject gemaakt met een HTTP-geactiveerde functie, het uitvoeren op uw lokale machine en geïmplementeerd in Azure. Nu, verleng uw functie door ...

> [!div class="nextstepaction"]
> [Een azure storage-wachtrijuitvoerbinding toevoegen](functions-add-output-binding-storage-queue-java.md)


[Azure-CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
