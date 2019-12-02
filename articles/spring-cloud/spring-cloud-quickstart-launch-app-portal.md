---
title: 'Snelstartgids: een Azure lente-Cloud toepassing starten met behulp van de Azure Portal'
description: Implementeer een voorbeeld toepassing in de Azure lente-Cloud met behulp van de Azure Portal.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 0d8bc302434ea973dd4d94156c65ee6965dd1dc1
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666241"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Snelstartgids: een Azure lente-Cloud toepassing starten met behulp van de Azure Portal

Met Azure lente Cloud kunt u eenvoudig lente-Cloud toepassingen uitvoeren op Azure.

In deze Quick start ziet u hoe u een bestaande lente-Cloud toepassing kunt implementeren in Azure.  De voorbeeld toepassings code die in deze zelf studie wordt gebruikt, is te vinden in onze [github samples-opslag plaats](https://github.com/Azure-Samples/PiggyMetrics). Wanneer u klaar bent, is de beschik bare voorbeeld toepassing online en kan deze worden beheerd via de Azure Portal.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Openbaar eind punt voor uw toepassing toewijzen

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Azure lente Cloud wordt momenteel aangeboden als een open bare preview. Met open bare preview-aanbiedingen kunnen klanten experimenteren met nieuwe functies vóór hun officiële release.  Open bare preview-functies en-services zijn niet bedoeld voor gebruik in productie omgevingen.  Raadpleeg voor meer informatie over ondersteuning tijdens previews onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of bestand a [ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie.

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

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten op het Azure Portal

1. Open in een webbrowser [deze koppeling naar Azure lente Cloud in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform).

1. Vul het formulier in op de pagina Azure lente-Cloud **maken** .  Houd rekening met de volgende richt lijnen:
    - Service naam: Geef de naam van uw service-exemplaar op.  De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten.  Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - Abonnement: Selecteer het abonnement dat u voor deze resource wilt factureren.  Zorg ervoor dat dit abonnement is toegevoegd aan onze acceptatie lijst voor Azure lente-Cloud.
    - Resource groep: het maken van nieuwe resource groepen voor nieuwe resources is een best practice.
    - Locatie: Selecteer de locatie voor uw service-exemplaar. Op dit moment worden de volgende locaties ondersteund: VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.

1. Klik op **controleren en maken**.

1. Controleer uw specificaties en klik op **maken**.

Het duurt ongeveer vijf minuten voordat de service wordt geïmplementeerd.  Zodra de app is geïmplementeerd, wordt de **overzichts** pagina voor het service-exemplaar weer gegeven.

## <a name="set-up-your-configuration-server"></a>De configuratie server instellen

1. Ga naar de **overzichts** pagina van de service en selecteer **Configuratie server**.

1. Stel in het gedeelte **standaard opslagplaats** de **URI** in op https\://github.com/azure-samples/piggymetrics.

1. Stel het **Label** in op config.

1. Selecteer **Toep assen** om uw wijzigingen op te slaan.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

## <a name="build-and-deploy-microservice-applications"></a>Micro service-toepassingen bouwen en implementeren

1. Open een [Azure Cloud shell](https://shell.azure.com) en kloon de opslag plaats van de voor beeld-app naar uw lokale computer.  Hier maken we eerst een tijdelijke map met de naam `source-code` voordat ze de app klonen.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Bouw het gekloonde pakket.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```
1. Namen toewijzen aan de resource groep en uw service. Zorg ervoor dat u de onderstaande tijdelijke aanduidingen vervangt door de naam van de resource groep en de service naam die u eerder in deze zelf studie hebt ingericht.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Maak de `gateway`-toepassing en implementeer het JAR-bestand.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Maak na hetzelfde patroon een `account-service`-en `auth-service`-toepassing en implementeer de JAR-bestanden.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Het duurt enkele minuten om de implementatie van de toepassingen te volt ooien. Als u wilt bevestigen dat ze zijn geïmplementeerd, gaat u naar de Blade **apps** in het Azure Portal. U ziet een regel voor elk van de drie toepassingen.

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eind punt toewijzen aan de gateway

1. Open het tabblad **apps** in het menu aan de linkerkant.

1. Selecteer de `gateway` toepassing om de **overzichts** pagina weer te geven.

1. Selecteer **domein toewijzen** om een openbaar eind punt toe te wijzen aan de gateway. Dit kan enkele minuten duren.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. Voer het toegewezen open bare eind punt ( **URL**met label) in uw browser in om de actieve toepassing weer te geven.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)


## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Openbaar eind punt toewijzen voor uw toepassings gateway

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
