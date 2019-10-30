---
title: Een Java-lente toepassing starten met behulp van de Azure CLI
description: In deze Quick Start implementeert u een voorbeeld toepassing in azure lente-Cloud op de Azure CLI.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 82e59d90135d6bdeb2d4ee1ad0e9a64ef0489cbc
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101271"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Snelstartgids: een Java-lente toepassing starten met Azure CLI

Met Azure veer Cloud kunt u eenvoudig een micro service toepassing op basis van een Spring boot uitvoeren op Azure.

In deze Quick start ziet u hoe u een bestaande Java-toepassing in azure kunt implementeren. Wanneer u klaar bent, kunt u door gaan met het beheren van de toepassing via de Azure CLI of overschakelen naar het gebruik van de Azure Portal.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Openbaar eind punt voor uw toepassing toewijzen

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Zorg ervoor dat uw Azure-abonnement toegang heeft tot de Azure lente-Cloud voordat u begint met deze Snelstartgids.  Als preview-service wordt u gevraagd om contact met ons op te nemen zodat we uw abonnement kunnen toevoegen aan onze acceptatie lijst.  [Vul dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
)in als u de mogelijkheden van Azure lente-Cloud wilt verkennen.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3,0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [De Azure CLI-versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Een service-exemplaar inrichten in de Azure CLI

1. Meld u aan bij de Azure CLI en kies uw actieve abonnement. Zorg ervoor dat u het actieve abonnement kiest dat White List is voor Azure lente-Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Bereid een naam voor de Azure lente-Cloud service voor.  De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten.  Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

3. Maak een resource groep die uw Azure lente-Cloud service bevat.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/resource-group-overview.md).

4. Open een Azure CLI-venster en voer de volgende opdrachten uit om een exemplaar van de Azure lente-Cloud in te richten.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    De implementatie van het service-exemplaar duurt ongeveer vijf minuten.

5. Stel de standaard naam van de resource groep en de naam van het cluster in met behulp van de volgende opdrachten:

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

## <a name="setup-your-configuration-server"></a>De configuratie server instellen

Werk uw config-server bij met de locatie van de Git-opslag plaats voor het project:

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

## <a name="build-the-microservices-applications-locally"></a>De micro Services-toepassingen lokaal bouwen

1. Maak een nieuwe map en kloon de opslag plaats van de voor beeld-app naar uw Azure Cloud-account.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Wijzig de map en bouw het project.

    ```azurecli
        cd PiggyMetrics
        mvn clean package -D skipTests
    ```

Het compileren van het project duurt ongeveer vijf minuten.  Als u klaar bent, moet u afzonderlijke JAR-bestanden voor elke service in hun respectieve mappen hebben.

## <a name="create-the-microservices"></a>De micro Services maken

Maak lente-Cloud micro Services met behulp van de JAR-bestanden die zijn gemaakt in de vorige stap. U maakt drie micro Services: **Gateway**, **auth-service**en **account service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

>[!NOTE]
> De namen van de toepassingen moeten exact overeenkomen met de namen van de POTTen zodat de opgegeven configuratie server goed werkt.

## <a name="deploy-applications-and-set-environment-variables"></a>Toepassingen implementeren en omgevings variabelen instellen

We moeten onze toepassingen echt implementeren in Azure. Gebruik de volgende opdrachten om alle drie de toepassingen te implementeren:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

## <a name="assign-public-endpoint-to-gateway"></a>Openbaar eind punt toewijzen aan gateway

We hebben een manier nodig om toegang te krijgen tot de toepassing via een webbrowser. Onze gateway toepassing heeft een openbaar gericht eind punt nodig dat kan worden toegewezen met de volgende opdracht:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Voer ten slotte een query uit op de **Gateway** toepassing voor het open bare IP-adres, zodat u kunt controleren of de toepassing wordt uitgevoerd:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Ga naar de URL die u hebt gekregen van de vorige opdracht om te zien hoe de PiggyMetrics-toepassing wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lente-Cloud toepassing geïmplementeerd vanuit de Azure CLI.  Ga verder met de zelf studie over het voorbereiden van uw app voor implementatie voor meer informatie over Azure veer Cloud.

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
