---
title: 'Quickstart: Een Java Spring-toepassing starten met behulp van de Azure CLI'
description: In deze quickstart implementeert u een voorbeeldtoepassing in Azure Spring Cloud vanuit de Azure CLI.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: ab6eb9b516ca30d92437bdc358e3f1f56cfe8e71
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457215"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Quickstart: Een Java Spring-toepassing starten met behulp van de Azure CLI

Met Azure Spring Cloud kunt u eenvoudig een op Spring Boot gebaseerde microservicetoepassing uitvoeren in Azure.

In deze quickstart ziet u hoe u een bestaande Java Spring Cloud-toepassing implementeert in Azure. Als u klaar bent, kunt u doorgaan met het beheren van de toepassing via de Azure CLI of overstappen op het gebruik van Azure Portal.

In deze quickstart leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratieserver instellen voor een exemplaar
> * Lokaal een microservicetoepassing bouwen
> * Alle microservices implementeren
> * Een openbaar eindpunt voor uw toepassing toewijzen

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Azure Spring Cloud wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunnen klanten voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Voor meer informatie over ondersteuning tijdens previews kunt u onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) raadplegen of een [Ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) indienen.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Deze bevat vooraf geïnstalleerde algemene Azure-hulpprogramma's, met inbegrip van de nieuwste versies van Git, JDK, Maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanuit shell.azure.com.  Meer informatie over Azure Cloud Shell vindt u door [onze documentatie te lezen](../cloud-shell/overview.md)

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [Azure CLI-versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Voer de volgende opdracht uit om de Azure Spring Cloud-extensie voor de Azure CLI te installeren

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Een service-exemplaar inrichten in de Azure CLI

1. Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat is goedgekeurd voor Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Bedenk een naam voor uw Azure Spring Cloud-service.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

3. Maak een resourcegroep die uw Azure Spring Cloud-service bevat.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).

4. Open een Azure CLI-venster en voer de volgende opdrachten uit om een exemplaar van Azure Spring Cloud in te richten.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    De implementatie van de service-instantie duurt ongeveer vijf minuten.

5. Stel de standaardnaam van de resourcegroep en van het cluster in met behulp van de volgende opdrachten:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>De configuratieserver instellen

Werk uw configuratieserver bij met de locatie van de Git-opslagplaats voor het project:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Lokaal microservicetoepassingen bouwen

1. Maak een nieuwe map en kloon de opslagplaats van de voorbeeld-app in uw Azure Cloud-account.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Wijzig de map en bouw het project.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Het compileren van het project duurt ongeveer vijf minuten.  Als u klaar bent, moet u voor elke service afzonderlijke JAR-bestanden in de corresponderende mappen hebben.

## <a name="create-the-microservices"></a>De microservices maken

Maak Spring Cloud-microservices met behulp van de JAR-bestanden die in de vorige stap zijn gemaakt. U maakt drie microservices: **gateway**, **auth-service**en **account-service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Toepassingen implementeren en omgevingsvariabelen instellen

We moeten onze toepassingen implementeren in Azure. Gebruik de volgende opdrachten om alle drie de toepassingen te implementeren:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan de gateway

We hebben een manier nodig om via een webbrowser toegang te krijgen tot de toepassing. Voor onze gatewaytoepassing is een openbaar eindpunt vereist.

1. Wijs het eindpunt toe met behulp van de volgende opdracht:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Voer een query uit op de **gateway**-toepassing om het openbare IP-adres ervan op te halen, zodat u kunt controleren of de toepassing wordt uitgevoerd:

```azurecli
az spring-cloud app show --name gateway --query properties.url
```

3. Ga naar de URL die met de vorige opdracht is opgehaald om de PiggyMetrics-toepassing uit te voeren.
    ![Schermopname van het uitvoeren van PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

U kunt ook door Azure Portal bladeren om de URL te vinden. 
1. Navigeer naar de service
2. Selecteer **Apps**
3. Selecteer **gateway**

    ![Schermopname van het uitvoeren van PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Zoek de URL op de pagina **gatewayoverzicht** ![Schermopname van het uitvoeren van PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Spring Cloud-toepassing geïmplementeerd vanuit de Azure CLI.  Ga verder met de zelfstudie over het voorbereiden van uw app voor implementatie voor meer informatie over Azure Spring Cloud.

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
