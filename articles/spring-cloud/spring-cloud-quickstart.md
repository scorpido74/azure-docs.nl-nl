---
title: Snelstart - Uw eerste Azure Spring Cloud-toepassing implementeren
description: In deze quickstart implementeren we een Spring Cloud-toepassing in Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260547"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Quickstart: Uw eerste Azure Spring Cloud-toepassing implementeren

In deze quickstart wordt uitgelegd hoe u een eenvoudige Azure Spring Cloud-microservicetoepassing kunt implementeren om te worden uitgevoerd in Azure. 

De toepassingscode die in deze zelfstudie wordt gebruikt, is een eenvoudige app die is gebouwd met Spring Initializr. Wanneer u dit voorbeeld hebt voltooid, is de toepassing online toegankelijk en kan deze worden beheerd via de Azure-portal.

In deze quickstart wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Een eenvoudig Spring Cloud-project genereren
> * Een service-exemplaar inrichten
> * De app bouwen en implementeren met een openbaar eindpunt
> * Logboeken in real-time streamen

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI-versie 2.0.67 of hoger](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Een Spring Cloud-project genereren

Begin met [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) om een voorbeeldproject te genereren met aanbevolen afhankelijkheden voor Azure Spring Cloud. In de volgende afbeelding ziet u de Initializr-configuratie voor dit voorbeeldproject.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Initializr-pagina](media/spring-cloud-quickstart-java/initializr-page.png)

1. Klik op **Genereren** wanneer alle afhankelijkheden zijn ingesteld. Download het pakket en pak het uit. Maak vervolgens een webcontroller voor een eenvoudige webtoepassing door `src/main/java/com/example/hellospring/HelloController.java` als volgt toe te voegen:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Een exemplaar van Azure Spring Cloud inrichten

Met de volgende procedure maakt u een exemplaar van Azure Spring Cloud met behulp van de Azure-portal.

1. Open [Azure Portal](https://ms.portal.azure.com/) op een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar *Azure Spring Cloud*.

3. Selecteer *Azure Spring Cloud* in de resultaten.

    ![ASC-pictogram: starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op **+ Toevoegen** op de Azure Spring Cloud-pagina.

    ![ASC-pictogram: toevoegen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.  Houd rekening met de volgende richtlijnen:
    - **Abonnement**: Selecteer het abonnement waarvoor u voor deze resource gefactureerd wilt worden.
    - **Resourcegroep**: Het is een best practice om nieuwe resourcegroepen te maken voor nieuwe resources. Dit wordt in latere stappen gebruikt als **\<resource group name\>** .
    - **Servicedetails/naam**: Geef de **\<service instance name\>** op.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - **Locatie**: Selecteer de regio voor uw service-exemplaar.

    ![ASC-portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op **Controleren en maken**.

## <a name="build-and-deploy-the-app"></a>De app compileren en implementeren
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Met de volgende procedure wordt de toepassing gebouwd en geïmplementeerd met behulp van de Azure CLI. Voer de volgende opdracht uit in de hoofdmap van het project.

1. Bouw het project met behulp van Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. Installeer de Azure Spring Cloud-extensie voor de Azure CLI (als u dit nog niet hebt gedaan):

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Maak de app met een toegewezen openbaar eindpunt:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Implementeer het JAR-bestand voor de app:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Het duurt enkele minuten om de implementatie van de toepassing te voltooien. Als u wilt controleren of de implementatie is geslaagd, gaat u naar de blade **Apps** in de Azure-portal. U ziet hier de status van de toepassing.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

In de volgende procedure wordt gebruikgemaakt van de IntelliJ-invoegtoepassing voor Azure Spring Cloud om de voorbeeld-app te implementeren in de IntelliJ IDEA.  

### <a name="import-project"></a>Project importeren

1. Open het dialoogvenster **Welkom** van IntelliJ, en selecteer **Project importeren** om de wizard Importeren te openen.
1. Selecteer de map `hellospring`.

    ![Project importeren](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>De app implementeren
Als u wilt implementeren in Azure, moet u zich aanmelden met uw Azure-account en uw abonnement kiezen.  Zie [Installatie en aanmelding](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) voor meer informatie over aanmelden.

1. Klik met de rechtermuisknop op uw project in de projectverkenner van IntelliJ en selecteer **Azure** -> **Implementeren in Azure Spring Cloud**.

    [ ![Implementeren in Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Accepteer de naam voor de app in het veld **Naam**. **Naam** verwijst naar de configuratie, niet naar de naam van de app. Gebruikers hoeven deze doorgaans niet te wijzigen.
1. Selecteer in het tekstvak **Artefact** de optie *hellospring-0.0.1-SNAPSHOT.jar*.
1. Controleer uw abonnement in het tekstvak **Abonnement**.
1. Selecteer in het tekstvak **Spring Cloud** het exemplaar van Azure Spring Cloud dat u hebt gemaakt in [Azure Spring Cloud-exemplaar inrichten](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Stel **Openbaar eindpunt** in op *Inschakelen*.
1. Selecteer in het tekstvak **App:** de optie **App maken...** .
1. Voer *hellospring* in en klik vervolgens op **OK**.

    [ ![Implementeren in Azure OK](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Start de implementatie door onder in het dialoogvenster **Azure Spring Cloud-app implementeren** op de knop **Uitvoeren** te klikken. De invoegtoepassing voert de opdracht `mvn package` uit in de app `hellospring` en implementeert het JAR-bestand dat is gegenereerd met de opdracht `package`.
---

Zodra de implementatie is voltooid, hebt u toegang tot de app op `https://<service instance name>-hellospring.azuremicroservices.io/`.

  [ ![Toegang tot de app vanuit de browser](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Streaminglogboeken in real-time

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Gebruik de volgende opdracht om real-time logboeken op te halen in de app.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Logboeken worden weergegeven in de resultaten:

[ ![Streaminglogboeken](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Gebruik `az spring-cloud app logs -h` om meer parameters en functionaliteit van logboekstreaming te verkennen.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Selecteer **Azure Explorer**  en **Spring Cloud**.
1. Klik met de rechtermuisknop op de app die wordt uitgevoerd.
1. Selecteer **Streaminglogboeken** in de vervolgkeuzelijst.
1. Selecteer het exemplaar.

    [ ![Streaminglogboeken selecteren](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Het streaminglogboek wordt weergegeven in het uitvoervenster.

    [ ![Uitvoer van streaminglogboeken](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Ga naar het tabblad **Logboeken** in het menu van de [Azure-portal](https://portal.azure.com/) voor geavanceerde functies voor logboekanalyse. Logboeken hebben hier een latentie van enkele minuten.

[ ![Logboekanalyse](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen
In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep uit de portal of door de volgende opdracht in Cloud Shell uit te voeren:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een eenvoudig Azure Spring Cloud-project genereren
> * Een service-exemplaar inrichten
> * De app bouwen en implementeren met een openbaar eindpunt
> * Streaminglogboeken in real-time
## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Microservices bouwen en uitvoeren](spring-cloud-quickstart-sample-app-introduction.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
