---
title: Java-en Maven gebruiken om een functie te publiceren in azure
description: Een door HTTP geactiveerde functie maken en publiceren naar Azure met Java en Maven.
author: rloutlaw
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ef81ff1d3d42e3c9e2ba5d4187f5b5805d35d900
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562031"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Snelstartgids: Java en Maven gebruiken om een functie te maken en te publiceren in azure

In dit artikel leest u hoe u een Java-functie bouwt en publiceert op Azure Functions met het opdracht regel programma maven. Wanneer u klaar bent, wordt de functie code in azure uitgevoerd in een [hosting abonnement](functions-scale.md#consumption-plan) op de server en wordt geactiveerd door een HTTP-aanvraag.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](https://aka.ms/azure-jdks), versie 8
- [Apache Maven](https://maven.apache.org), versie 3,0 of hoger
- [Azure-CLI]
- [Azure functions core tools](./functions-run-local.md#v2) versie 2.6.666 of hoger
- Een Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


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
> Als u problemen ondervindt met het uitvoeren van de opdracht, bekijkt u wat `maven-archetype-plugin`-versie wordt gebruikt. Omdat u de opdracht uitvoert in een lege map zonder `.pom`-bestand, probeert deze mogelijk een invoeg toepassing van de oudere versie te gebruiken van `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` als u uw maven hebt bijgewerkt op basis van een oudere versie. Als dit het geval is, verwijdert u de `maven-archetype-plugin` map en voert u de opdracht opnieuw uit.

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

Maven vraagt u om de waarden die nodig zijn om het project te kunnen genereren tijdens de implementatie. Geef de volgende waarden op wanneer u hierom wordt gevraagd:

| Waarde | Beschrijving |
| ----- | ----------- |
| **Groep** | Een waarde die uw project op unieke wijze identificeert voor alle projecten, volgens de [pakket naamgevings regels](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. In de voor beelden in deze Snelstartgids wordt gebruikgemaakt van `com.fabrikam.functions`. |
| **artifactId** | Een waarde die bestaat uit de naam van het jar, zonder een versie nummer. In de voor beelden in deze Snelstartgids wordt gebruikgemaakt van `fabrikam-functions`. |
| **Versie** | Kies de standaard waarde van `1.0-SNAPSHOT`. |
| **pakket** | Een waarde die het Java-pakket voor de gegenereerde functie code is. Gebruik de standaard. In de voor beelden in deze Snelstartgids wordt gebruikgemaakt van `com.fabrikam.functions`. |
| **Toepassings** | Een wereld wijd unieke naam waarmee uw nieuwe functie-app in azure wordt geïdentificeerd. Gebruik de standaard waarde. Dit is de _artifactId_ die wordt toegevoegd met een wille keurig getal. Noteer deze waarde. u hebt deze later nodig. |
| **appRegion** | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. De standaardwaarde is `westus`. Voer deze [Azure-CLI] -opdracht uit om een lijst met alle regio's op te halen:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | De naam voor de nieuwe [resource groep](../azure-resource-manager/management/overview.md) waarin u de functie-app wilt maken. Gebruik `myResourceGroup`, dat wordt gebruikt door voor beelden in deze Quick Start. Een resource groep moet uniek zijn voor uw Azure-abonnement.|

Typ `Y` of druk op ENTER om te bevestigen.

Maven maakt de project bestanden in een nieuwe map met de naam _artifactId_, die in dit voor beeld is `fabrikam-functions`. 

Open de nieuwe functie. java-bestand van het pad *src/main/Java* in een tekst editor en controleer de gegenereerde code. Deze code is een door [http geactiveerde](functions-bindings-http-webhook.md) functie die de hoofd tekst van de aanvraag echoert. 

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer de volgende opdracht uit, waarbij u de map wijzigt in de zojuist gemaakte projectmap en vervolgens het functie project bouwt en uitvoert:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

U ziet uitvoer als het volgende van Azure Functions Core Tools wanneer u het project lokaal uitvoert:

```Output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Activeer de functie vanaf de opdracht regel met behulp van krul in een nieuw terminal venster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```Output
Hello AzureFunctions!
```
De [functie sleutel](functions-bindings-http-webhook.md#authorization-keys) is niet vereist wanneer deze lokaal wordt uitgevoerd. Gebruik `Ctrl+C` in de terminal om de functiecode te stoppen.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Een functie-app en gerelateerde resources worden in azure gemaakt wanneer u de functie-app voor het eerst implementeert. Voordat u kunt implementeren, gebruikt u de opdracht [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

> [!TIP]
> Als uw account toegang heeft tot meerdere abonnementen, gebruikt u [AZ account set](/cli/azure/account#az-account-set) om het standaard abonnement voor deze sessie in te stellen. 

Gebruik de volgende maven-opdracht om uw project te implementeren in een nieuwe functie-app. 

```azurecli
mvn azure-functions:deploy
```

Met deze `azure-functions:deploy` maven-doel worden de volgende resources in azure gemaakt:

+ Resource groep. Met de naam van de _resourceGroup_ die u hebt opgegeven.
+ Opslag account. Vereist door-functies. De naam wordt wille keurig gegenereerd op basis van de vereisten van het opslag account.
+ App service-plan. Serverloze hosting voor uw functie-app in de opgegeven _appRegion_. De naam wordt wille keurig gegenereerd.
+ Functie-app. Een functie-app is de implementatie-en uitvoerings eenheid voor uw functies. De naam is uw _AppName_, die wordt toegevoegd met een wille keurig gegenereerd nummer. 

De implementatie verpakt de project bestanden ook en implementeert deze in de nieuwe functie-app met behulp van [zip-implementatie](functions-deployment-technologies.md#zip-deploy), waarbij de modus uitvoeren vanaf pakket is ingeschakeld.

Nadat de implementatie is voltooid, ziet u de URL die u kunt gebruiken om toegang te krijgen tot de eind punten van uw functie-apps. Omdat de HTTP-trigger die we hebben gepubliceerd, gebruikmaakt van `authLevel = AuthorizationLevel.FUNCTION`, moet u de functie code ophalen om het eind punt van de functie aan te roepen via HTTP. De eenvoudigste manier om de functie code op te halen, is van de [Azure Portal].

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>De URL van de HTTP-trigger ophalen

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

U kunt de URL ophalen die is vereist om uw functie te activeren, met de functie sleutel van de Azure Portal. 

1. Blader naar de [Azure Portal], Meld u aan, typ de _AppName_ van uw functie-app in **zoeken** boven aan de pagina en druk op ENTER.
 
1. Vouw in uw functie-app **functies (alleen-lezen)** uit, kies uw functie en selecteer vervolgens in de rechter bovenhoek **</> functie-URL ophalen** . 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-java-maven/get-function-url-portal.png)

1. Kies **standaard (functie toets)** en selecteer **kopiëren**. 

U kunt nu de gekopieerde URL gebruiken om toegang te krijgen tot uw functie.

## <a name="verify-the-function-in-azure"></a>De functie in azure controleren

Als u de functie-app die wordt uitgevoerd op Azure met `cURL`wilt controleren, vervangt u de URL uit het voor beeld hieronder door de URL die u hebt gekopieerd uit de portal.

```azurecli
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Hiermee wordt een POST-aanvraag verzonden naar het eind punt van de functie met `AzureFunctions` in de hoofd tekst van de aanvraag. U ziet het volgende antwoord.

```Output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-functies project gemaakt met een door HTTP geactiveerde functie, deze uitvoeren op uw lokale computer en geïmplementeerd in Azure. Breid uw functie nu uit door...

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij-uitvoer binding toevoegen](functions-add-output-binding-storage-queue-java.md)


[Azure-CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
