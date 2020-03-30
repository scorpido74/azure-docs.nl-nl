---
title: Snelstart - Start uw Spring Cloud-toepassing vanaf de broncode
description: In deze quickstart leert u hoe u uw Azure Spring Cloud-toepassing rechtstreeks vanuit uw broncode starten
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470791"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Snelstart: start uw Spring Cloud-toepassing vanaf de broncode

Azure Spring Cloud stelt u in staat om spring cloud-gebaseerde microservicetoepassingen eenvoudig op Azure uit te voeren.

Met Azure Spring Cloud u uw toepassing rechtstreeks starten vanuit uw java-broncode of vanuit een vooraf gebouwde JAR. In dit artikel worden de vereiste stappen doorlopen.

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

Controleer voordat u begint of uw Azure-abonnement de vereiste afhankelijkheden heeft:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Er zijn veelvoorkomende Azure-hulpprogramma's vooraf geïnstalleerd, waaronder de nieuwste versies van Git, JDK, Maven en azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com) vanaf shell.azure.com.  U meer informatie over Azure Cloud Shell lezen door [onze documentatie te lezen](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure Spring Cloud-extensie voor de Azure CLI met de volgende opdracht

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Een serviceinstantie inrichten met de Azure CLI

Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat op de witte lijst staat voor Azure Spring Cloud

```azurecli
az login
az account list -o table
az account set --subscription
```

Maak een resourcegroep met uw Azure Spring Cloud-service. Meer informatie over [Azure Resource Groups](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Voer de volgende opdrachten uit om een exemplaar van Azure Spring Cloud in te richten. Bereid een naam voor voor uw Azure Spring Cloud-service. De naam moet tussen de 4 en 32 tekens lang zijn en kan alleen kleine letters, cijfers en koppeltekens bevatten. Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een getal zijn.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Het implementeren van de service-instantie duurt ongeveer vijf minuten.

Stel de naam en clusternaam van uw standaardbrongroep in met de volgende opdrachten:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>De Spring Cloud-toepassing maken

Met de volgende opdracht wordt een Spring Cloud-toepassing in uw abonnement gemaakt.  Dit creëert een lege Spring Cloud service waarop we onze applicatie kunnen uploaden.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Uw Spring Cloud-toepassing implementeren

U uw toepassing implementeren vanuit een vooraf gebouwde JAR of vanuit een Gradle- of Maven-repository.  Hieronder vindt u instructies voor elk geval.

### <a name="deploy-a-built-jar"></a>Een gebouwde JAR implementeren

Om te implementeren van een JAR gebouwd op uw lokale machine, ervoor te zorgen dat uw build produceert een [vet-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

De fat-JAR implementeren voor een actieve implementatie

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

De fat-JAR implementeren voor een specifieke implementatie

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Implementeren vanuit broncode

Azure Spring Cloud gebruikt [kpack](https://github.com/pivotal/kpack) om uw project te bouwen.  U Azure CLI gebruiken om uw broncode te uploaden, uw project te bouwen met kpack en deze te implementeren in de doeltoepassing.

> [!WARNING]
> Het project mag slechts één `main-class` JAR-bestand `MANIFEST.MF` `target` produceren met een vermelding `build/libs` in de in (voor Maven-implementaties of (voor Gradle-implementaties).  Meerdere JAR-bestanden met `main-class` vermeldingen zorgen ervoor dat de implementatie mislukt.

Voor enkele module Maven / Gradle projecten:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Voor Maven / Gradle projecten met meerdere modules, herhaal voor elke module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Implementatielogboeken weergeven

Bekijk de kpack-buildlogboeken met de volgende opdracht:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> De kpack-logboeken tonen alleen de nieuwste implementatie als die implementatie is opgebouwd uit de bron met kpack.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan gateway

1. Open de pagina **Toepassingsdashboard.**
2. Selecteer `gateway` de toepassing om de pagina Toepassingsgegevens weer te **geven.**
3. Selecteer **Domein toewijzen** om een openbaar eindpunt toe te wijzen aan gateway. Dit kan een paar minuten. 
4. Voer het toegewezen openbare IP-adres in uw browser in om uw hardlooptoepassing te bekijken.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-instantie inrichten
> * Een configuratieserver instellen voor een instantie
> * Lokaal een microservices-toepassing bouwen
> * Elke microservice implementeren
> * Omgevingsvariabelen voor toepassingen bewerken
> * Openbare IP toewijzen voor uw toepassingsgateway

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden op implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
