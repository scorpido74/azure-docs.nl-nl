---
title: Java en Gradle gebruiken om een functie naar Azure te publiceren
description: Een HTTP-geactiveerde functie maken en publiceren naar Azure met Java en Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 5e18e035bd237fd489b715986e58d7ede726348d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886599"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Java en Gradle gebruiken om een functie te maken en te publiceren naar Azure

In dit artikel ziet u hoe u een Java-functieproject bouwen en publiceren naar Azure-functies met het opdrachtregelhulpprogramma Gradle. Wanneer u klaar bent, wordt uw functiecode uitgevoerd in Azure in een [serverloos hostingplan](functions-scale.md#consumption-plan) en wordt geactiveerd door een HTTP-aanvraag. 

> [!NOTE]
> Als Gradle is niet uw voorkeur ontwikkeling tool, check out onze soortgelijke tutorials voor Java devlopers met behulp van [Maven,](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) en [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](https://aka.ms/azure-jdks), versie 8
- [Azure-CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versie 2.6.666 of hoger
- [Gradle](https://gradle.org/), versie 4.10 en hoger

U hebt ook een actief Azure-abonnement nodig. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.

## <a name="prepare-a-functions-project"></a>Een functieproject voorbereiden

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

Open het nieuwe Function.java-bestand vanaf het *src/main/java-pad* in een teksteditor en bekijk de gegenereerde code. Deze code is een [HTTP-geactiveerde](functions-bindings-http-webhook.md) functie die de hoofdtekst van het verzoek weerkaatst. 

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer de volgende opdracht uit om te bouwen en voer het functieproject uit:

```bash
gradle jar --info
gradle azureFunctionsRun
```
U ziet uitvoer zoals het volgende van Azure Functions Core Tools wanneer u het project lokaal uitvoert:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Activeer de functie vanaf de opdrachtregel met de volgende cURL-opdracht in een nieuw terminalvenster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

De verwachte output is de volgende:

<pre>
Hello AzureFunctions!
</pre>

De [functiesleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) is niet vereist wanneer u lokaal wordt uitgevoerd.  
Gebruik `Ctrl+C` in de terminal om de functiecode te stoppen.

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

```bash
gradle azureFunctionsDeploy
```

Hiermee worden de volgende resources in Azure gemaakt op basis van de waarden in het build.gradle-bestand:

+ Resourcegroep. Benoemd met de _resourceGroup die_ u hebt geleverd.
+ Opslagaccount. Vereist door Functies. De naam wordt willekeurig gegenereerd op basis van de vereisten voor de naam van het opslagaccount.
+ App Service-abonnement. Serverless Consumption plan hosting voor uw functie-app in de opgegeven _appRegio_. De naam wordt willekeurig gegenereerd.
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam is uw _appName_, toegevoegd met een willekeurig gegenereerd nummer. 

De implementatie verpakt ook de projectbestanden en implementeert deze naar de nieuwe functie-app met [zip-implementatie](functions-deployment-technologies.md#zip-deploy), met run-from-package-modus ingeschakeld.

Omdat de HTTP-trigger `authLevel = AuthorizationLevel.FUNCTION`die we hebben gepubliceerd, wordt gebruikt, moet u de functiesleutel krijgen om het functieeindpunt over HTTP aan te roepen. De eenvoudigste manier om de functiesleutel te krijgen, is via de [Azure-portal.]

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>De HTTP-trigger-URL ophalen

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

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-functieproject gemaakt met een HTTP-geactiveerde functie, het uitvoeren op uw lokale machine en geïmplementeerd in Azure. Nu, verleng uw functie door ...

> [!div class="nextstepaction"]
> [Een azure storage-wachtrijuitvoerbinding toevoegen](functions-add-output-binding-storage-queue-java.md)


[Azure-CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
