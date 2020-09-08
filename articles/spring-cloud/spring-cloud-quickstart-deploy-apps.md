---
title: 'Quickstart: apps bouwen en implementeren in Azure Spring Cloud'
description: Beschrijft de implementatie van apps in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050825"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Quickstart: Apps bouwen en implementeren in Azure Spring Cloud

In dit document wordt uitgelegd hoe u microservicetoepassingen bouwt en implementeert in Azure Spring Cloud met behulp van:
* Azure CLI
* Maven-invoegtoepassing
* Intellij

Voordat u implementeert met behulp van Azure CLI of Maven, voltooit u eerst de voorbeelden van [inrichting van Azure Spring Cloud-exemplaar](spring-cloud-quickstart-provision-service-instance.md) en [het instellen van de configuratieserver](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Implementatieprocedures

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Lokaal microservicetoepassingen bouwen

1. Kloon de opslagplaats van de voorbeeld-app in uw Azure Cloud-account.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Wijzig de map en bouw het project.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Het compileren van het project duurt ongeveer vijf minuten. Als u klaar bent, moet u voor elke service afzonderlijke JAR-bestanden in de corresponderende mappen hebben.

### <a name="create-and-deploy-the-apps"></a>De apps maken en implementeren

1. Stel de standaardnaam van de resourcegroep en van het cluster in met behulp van de volgende opdrachten:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Maak Azure Spring Cloud-microservices met behulp van de JAR-bestanden die in de vorige stap zijn gemaakt. U maakt drie apps: **gateway**, **auth-service** en **account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Toepassingen die in de vorige stap zijn gemaakt, moeten in Azure worden geïmplementeerd. Gebruik de volgende opdrachten om alle drie de toepassingen te implementeren:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan de gateway

We hebben een manier nodig om via een webbrowser toegang te krijgen tot de toepassing. Voor onze gatewaytoepassing is een openbaar eindpunt vereist.

1. Wijs het eindpunt toe met behulp van de volgende opdracht:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Voer een query uit op de **gateway**-toepassing om het openbare IP-adres ervan op te halen, zodat u kunt controleren of de toepassing wordt uitgevoerd:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>De voorbeeldtoepassingsopslagplaats klonen en bouwen

1. Voer de volgende opdracht uit om de Git-opslagplaats te klonen:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Configuraties genereren en implementeren in de Azure Spring Cloud

1. Genereer configuraties door de volgende opdracht uit te voeren in de hoofdmap van Piggy Metrics die de bovenliggende POM bevat. Als u zich al hebt aangemeld bij Azure CLI, worden de referenties automatisch opgehaald met de opdracht. Als u dit niet doet, wordt u aangemeld via promptinstructies. Bekijk onze [wiki-pagina](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) voor meer informatie.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    U wordt gevraagd om het volgende te selecteren:
    * **Modules:** Selecteer `gateway`, `auth-service` en `account-service`.
    * **Abonnement:** Dit is het abonnement dat wordt gebruikt om een Azure Spring Cloud-exemplaar te maken.
    * **Service-exemplaar:** Dit is de naam van het Azure Spring Cloud-exemplaar.
    * **Openbaar eindpunt:** Voer in de lijst met opgegeven projecten het getal in dat overeenkomt met `gateway`.  Dit verleent openbare toegang.

1. De POM bevat nu de afhankelijkheden en configuraties van de invoegtoepassing. Implementeer de apps met behulp van de volgende opdracht. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Voorbeeldproject importeren in IntelliJ

1. Download de bronopslagplaats voor deze zelfstudie en pak deze uit, of kloon deze met Git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Open het dialoogvenster **Welkom** van IntelliJ en selecteer **Project importeren** om de wizard Importeren te openen.

1. Selecteer de map `piggymetric`.

    ![Project importeren](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Gateway-app implementeren in Azure Spring Cloud
Als u wilt implementeren in Azure, moet u zich aanmelden met uw Azure-toolkit voor IntelliJ, en uw abonnement kiezen. Zie [Installatie en aanmelding](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) voor meer informatie over aanmelden.

1. Klik met de rechtermuisknop op uw project in de projectverkenner van IntelliJ en selecteer **Azure** -> **Implementeren in Azure Spring Cloud**.

    ![Implementeren in Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. Voeg in het veld **Naam** het achtervoegsel *:gateway* toe aan de bestaande **Naam** die verwijst naar de configuratie.
1. Selecteer in het tekstvak **Artefact** de optie *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. Controleer uw abonnement in het tekstvak **Abonnement**.
1. Selecteer in het tekstvak **Spring Cloud** het exemplaar van Azure Spring Cloud dat u hebt gemaakt in [Azure Spring Cloud-exemplaar inrichten](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Stel **Openbaar eindpunt** in op *Inschakelen*.
1. Selecteer in het tekstvak **App:** de optie **App maken...** .
1. Voer *gateway* in en klik vervolgens op **OK**.

    ![Implementeren naar Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. Dubbelklik in de sectie **Vóór het starten** van het dialoogvenster op *Maven-doel uitvoeren*.
1. Ga in het tekstvak **Werkmap** naar de map *piggymetrics/gateway*.
1. Voer *package-DskipTests* in het tekstvak **Opdrachtregel** in. Klik op **OK**.
1. Start de implementatie door onder in het dialoogvenster **Azure Spring Cloud-app implementeren** op de knop **Uitvoeren** te klikken. De invoegtoepassing voert de opdracht `mvn package` uit in de app `gateway` en implementeert het JAR-bestand dat is gegenereerd met de opdracht `package`.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Apps voor auth-service en account-service implementeren in Azure Spring Cloud
U kunt de bovenstaande stappen herhalen om de apps `auth-service` en `account-service` te implementeren in Azure Spring Cloud:

1. Wijzig **Naam** en **Artefact** om de app `auth-service` te identificeren.
1. Selecteer in het tekstvak **App:** de optie **App maken...** om de apps `auth-service` te maken.
1. Controleer of de optie **Openbaar eindpunt** is ingesteld op *Uitgeschakeld*.
1. Schakel in de sectie **Vóór het starten** van het dialoogvenster de **Werkmap** naar de map *piggymetrics/auth-service*.
1. Start de implementatie door onder in het dialoogvenster **Azure Spring Cloud-app implementeren** op de knop **Uitvoeren** te klikken. 
1. Herhaal deze procedures om `account-service` te configureren en te implementeren.
---

Ga naar de URL die in de uitvoer van de vorige stappen is opgegeven, voor toegang tot de Piggy Metrics-toepassing. bijvoorbeeld `https://<service instance name>-gateway.azuremicroservices.io`

![Toegang tot Piggy Metrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

U kunt ook door Azure Portal bladeren om de URL te vinden. 
1. Navigeer naar de service
2. Selecteer **Apps**
3. Selecteer **gateway**

    ![Navigeren in de app](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Zoek de URL op de pagina **Gateway | Overzicht**

    ![Navigeren in de app, twee](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Resources opschonen
In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep uit de portal of door de volgende opdracht in Cloud Shell uit te voeren:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
In de voorgaande stappen hebt u ook de standaardnaam van de resourcegroep ingesteld. Voer de volgende opdracht uit in Cloud Shell om de standaardinstelling te wissen:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Logboeken, metrische gegevens en tracering](spring-cloud-quickstart-logs-metrics-tracing.md)
