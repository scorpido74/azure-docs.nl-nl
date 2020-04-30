---
title: 'Snelstartgids: uw lente-Cloud toepassing vanuit de bron code starten'
description: In deze Quick Start leert u hoe u uw Azure lente-Cloud toepassing rechtstreeks vanuit uw bron code kunt starten
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79470791"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Snelstartgids: uw lente-Cloud toepassing vanuit de bron code starten

Met Azure lente Cloud kunt u eenvoudig lente-Cloud toepassingen uitvoeren op Azure.

Met Azure lente Cloud kunt u uw toepassing rechtstreeks starten vanuit uw Java-bron code of van een vooraf gebouwde JAR. Dit artikel begeleidt u stapsgewijs door de vereiste stappen.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Openbaar eind punt voor uw toepassing toewijzen

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Azure lente Cloud wordt momenteel aangeboden als een open bare preview. Met open bare preview-aanbiedingen kunnen klanten experimenteren met nieuwe functies vóór hun officiële release.  Open bare preview-functies en-services zijn niet bedoeld voor gebruik in productie omgevingen.  Raadpleeg voor meer informatie over ondersteuning tijdens previews onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of bestand a [ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) voor meer informatie.

Voordat u begint, moet u ervoor zorgen dat uw Azure-abonnement de vereiste afhankelijkheden heeft:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3,0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

De Azure veer Cloud-extensie voor de Azure CLI installeren met de volgende opdracht

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Een service-exemplaar inrichten met de Azure CLI

Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat White List is voor Azure lente-Cloud

```azurecli
az login
az account list -o table
az account set --subscription
```

Maak een resource groep die uw Azure lente-Cloud service bevat. U kunt meer te weten komen over [Azure-resource groepen](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Voer de volgende opdrachten uit om een exemplaar van de Azure lente-Cloud in te richten. Bereid een naam voor de Azure lente-Cloud service voor. De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten. Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Het service-exemplaar duurt ongeveer vijf minuten om te implementeren.

Stel de standaard naam van de resource groep en de naam van het cluster in met behulp van de volgende opdrachten:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>De lente-Cloud toepassing maken

Met de volgende opdracht maakt u een lente-Cloud toepassing in uw abonnement.  Hiermee maakt u een lege lente-Cloud service waarnaar we onze toepassing kunnen uploaden.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Uw lente-Cloud toepassing implementeren

U kunt uw toepassing implementeren op basis van een vooraf gebouwde JAR of vanuit een Gradle-of maven-opslag plaats.  Zoek de instructies voor elk van de onderstaande cases.

### <a name="deploy-a-built-jar"></a>Een ingebouwd JAR implementeren

Als u wilt implementeren vanuit een JAR dat is gebouwd op uw lokale computer, moet u ervoor zorgen dat uw Build een [FAT-jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)produceert.

Het FAT-JAR implementeren voor een actieve implementatie

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Het FAT-JAR implementeren voor een specifieke implementatie

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Implementeren vanuit bron code

Azure veer Cloud maakt gebruik van [kpack](https://github.com/pivotal/kpack) om uw project te bouwen.  U kunt Azure CLI gebruiken om uw bron code te uploaden, uw project te bouwen met kpack en het te implementeren in de doel toepassing.

> [!WARNING]
> Het project moet slechts één JAR-bestand produceren met `main-class` een vermelding in `MANIFEST.MF` de `target` in (voor maven-implementaties of `build/libs` (voor Gradle-implementaties).  Als er meerdere JAR `main-class` -bestanden met vermeldingen zijn, mislukt de implementatie.

Voor maven/Gradle-projecten met één module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Voor maven/Gradle-projecten met meerdere modules herhaalt u voor elke module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Implementatie logboeken weer geven

Bekijk de kpack-build-logboeken met behulp van de volgende opdracht:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> In de kpack-Logboeken wordt alleen de meest recente implementatie weer gegeven als die implementatie is gemaakt op basis van de bron met behulp van kpack.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eind punt toewijzen aan de gateway

1. Open de pagina **toepassings dashboard** .
2. Selecteer de `gateway` toepassing om de pagina met **toepassings Details** weer te geven.
3. Selecteer **domein toewijzen** om een openbaar eind punt toe te wijzen aan de gateway. Dit kan een paar minuten duren. 
4. Voer het toegewezen open bare IP-adres in uw browser in om de actieve toepassing weer te geven.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Omgevings variabelen voor toepassingen bewerken
> * Openbaar IP-adres voor uw toepassings gateway toewijzen

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voor beelden zijn beschikbaar op GitHub: [Azure lente-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
