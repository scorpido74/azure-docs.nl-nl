---
title: 'Snelstart: een Java Spring-toepassing starten met de Azure CLI'
description: In deze quickstart implementeert u een voorbeeldtoepassing naar Azure Spring Cloud op azure CLI.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: c05e53bd8ad8ade8c1e42729f46c99a0059c4dce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470857"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Snelstart: een Java Spring-toepassing starten met de Azure CLI

Met Azure Spring Cloud u eenvoudig een op Spring Boot gebaseerde microservices-toepassing op Azure uitvoeren.

Met deze quickstart u zien hoe u een bestaande Java Spring Cloud-toepassing implementeert in Azure. Wanneer u klaar bent, u de toepassing blijven beheren via de Azure CLI of overschakelen naar het gebruik van de Azure-portal.

Na deze snelle start leert u hoe u:

> [!div class="checklist"]
> * Een service-instantie inrichten
> * Een configuratieserver instellen voor een instantie
> * Lokaal een microservices-toepassing bouwen
> * Elke microservice implementeren
> * Openbaar eindpunt toewijzen voor uw toepassing

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Azure Spring Cloud wordt momenteel aangeboden als een openbare preview. Openbare preview-aanbiedingen stellen klanten in staat om te experimenteren met nieuwe functies voordat ze officieel worden uitgebracht.  Openbare preview-functies en services zijn niet bedoeld voor productiegebruik.  Bekijk onze veelgestelde vragen over onze [veelgestelde vragen](https://azure.microsoft.com/support/faq/) voor meer informatie over ondersteuning tijdens previews of dien een [ondersteuningsverzoek](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) in voor meer informatie.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Er zijn veelvoorkomende Azure-hulpprogramma's vooraf geïnstalleerd, waaronder de nieuwste versies van Git, JDK, Maven en azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanaf shell.azure.com.  U meer informatie over Azure Cloud Shell lezen door [onze documentatie te lezen](../cloud-shell/overview.md)

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [De Azure CLI-versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

De Azure Spring Cloud-extensie voor de Azure CLI installeren met de volgende opdracht

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Een service-instantie inrichten op de Azure CLI

1. Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat op de witte lijst staat voor Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Bereid een naam voor voor uw Azure Spring Cloud-service.  De naam moet tussen de 4 en 32 tekens lang zijn en kan alleen kleine letters, cijfers en koppeltekens bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een getal zijn.

3. Maak een resourcegroep met uw Azure Spring Cloud-service.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Meer informatie over [Azure Resource Groups](../azure-resource-manager/management/overview.md).

4. Open een Azure CLI-venster en voer de volgende opdrachten uit om een exemplaar van Azure Spring Cloud in te richten.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Het implementeren van de service-instantie duurt ongeveer vijf minuten.

5. Stel de naam en clusternaam van uw standaardbrongroep in met de volgende opdrachten:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Uw configuratieserver instellen

Werk uw config-server bij met de locatie van de git repository voor ons project:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Bouw de microservices-toepassingen lokaal

1. Maak een nieuwe map en kloon de voorbeeld-app-opslagplaats naar uw Azure Cloud-account.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Wijzig de directory en bouw het project.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Het samenstellen van het project duurt ongeveer 5 minuten.  Eenmaal voltooid, moet u afzonderlijke JAR-bestanden voor elke service in hun respectievelijke mappen.

## <a name="create-the-microservices"></a>De microservices maken

Maak Spring Cloud-microservices met behulp van de JAR-bestanden die in de vorige stap zijn gebouwd. U maakt drie microservices: **gateway,** **auth-service**en **accountservice.**

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Toepassingen implementeren en omgevingsvariabelen instellen

We moeten onze toepassingen daadwerkelijk implementeren in Azure. Gebruik de volgende opdrachten om alle drie de toepassingen te implementeren:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Openbaar eindpunt toewijzen aan gateway

We hebben een manier nodig om toegang te krijgen tot de applicatie via een webbrowser. Onze gateway-toepassing heeft een openbaar eindpunt nodig.

1. Wijs het eindpunt toe met de volgende opdracht:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Vraag de **gatewaytoepassing** op voor het openbare IP-adres, zodat u controleren of de toepassing wordt uitgevoerd:

Linux:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Windows:

```azurecli
az spring-cloud app show -s <service name> -g <resource group> -n gateway -o table
```

3. Navigeer naar de URL die door de vorige opdracht wordt verstrekt om de toepassing PiggyMetrics uit te voeren.
    ![Schermafbeelding van PiggyMetrics die wordt uitgevoerd](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

U ook door de Azure-portal navigeren om de URL te vinden. 
1. Navigeren naar de service
2. **Apps selecteren**
3. Gateway **selecteren**

    ![Schermafbeelding van PiggyMetrics die wordt uitgevoerd](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. De URL zoeken **gateway Overview** op ![de pagina Overzicht van de gateway Schermafbeelding van PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Spring Cloud-toepassing van de Azure CLI geïmplementeerd.  Ga verder met de zelfstudie over het voorbereiden van uw app op implementatie voor meer informatie over Azure Spring Cloud.

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden op implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
