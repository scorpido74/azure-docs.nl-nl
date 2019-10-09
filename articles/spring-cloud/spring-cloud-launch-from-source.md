---
title: Start uw lente-Cloud toepassing vanuit de bron code
description: Meer informatie over hoe u uw Azure lente-Cloud toepassing rechtstreeks vanuit uw bron code kunt starten
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: a161b38ab6d23ad86df1ef7e843640276ba486bf
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038806"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Start uw lente-Cloud toepassing vanuit de bron code

Met Azure lente Cloud kunt u uw toepassing rechtstreeks starten vanuit uw Java-bron code of van een vooraf gebouwde JAR. Dit artikel begeleidt u stapsgewijs door de vereiste stappen.

## <a name="initial-requirements"></a>Eerste vereisten

Voordat u begint, moet u ervoor zorgen dat uw Azure-abonnement de vereiste afhankelijkheden heeft:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3,0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

De Azure veer Cloud-extensie voor de Azure CLI installeren met de volgende opdracht

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Een service-exemplaar inrichten met de Azure CLI

Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat White List is voor Azure lente-Cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Open een Azure CLI-venster en voer de volgende opdrachten uit om een exemplaar van de Azure lente-Cloud in te richten. Houd er rekening mee dat we hier ook een openbaar domein aan kunnen toewijzen.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

Het service-exemplaar duurt ongeveer vijf minuten om te implementeren.

Stel de standaard naam van de resource groep en de naam van het cluster in met behulp van de volgende opdrachten:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

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
> Het project mag slechts één JAR-bestand produceren met een `main-class`-vermelding in de `MANIFEST.MF` in `target` (voor maven-implementaties of `build/libs` (voor Gradle-implementaties).  Meerdere JAR-bestanden met `main-class` vermeldingen zorgen ervoor dat de implementatie mislukt.

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

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eind punt toewijzen aan de gateway

1. Open de pagina **toepassings dashboard** .
2. Selecteer de `gateway`-toepassing om de pagina met **toepassings Details** weer te geven.
3. Selecteer **domein toewijzen** om een openbaar eind punt toe te wijzen aan de gateway. Dit kan een paar minuten duren. 
4. Voer het toegewezen open bare IP-adres in uw browser in om de actieve toepassing weer te geven.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Omgevings variabelen voor toepassingen bewerken
> * Openbaar IP-adres voor uw toepassings gateway toewijzen

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
