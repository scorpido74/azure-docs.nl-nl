---
title: Een Azure lente-Cloud toepassing starten met behulp van de Azure Portal
description: Implementeer een voorbeeld toepassing in de Azure lente-Cloud met behulp van de Azure Portal.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 0f463291a0405178c5bdfbb890a6f954e13b204d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023949"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Snelstartgids: een Azure lente-Cloud toepassing starten met behulp van de Azure Portal

Met Azure lente Cloud kunt u eenvoudig lente-Cloud toepassingen uitvoeren op Azure.

In deze Quick start ziet u hoe u een bestaande lente-Cloud toepassing kunt implementeren in Azure. [Hier volgt een koppeling](https://github.com/Azure-Samples/PiggyMetrics) naar de voorbeeld toepassings code die in deze zelf studie wordt gebruikt. Wanneer u klaar bent, is de beschik bare voorbeeld toepassing online en kan deze worden beheerd via de Azure Portal.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Openbaar eind punt voor uw toepassing toewijzen

## <a name="prerequisites"></a>Vereisten

>[!Note]
> Zorg ervoor dat uw Azure-abonnement toegang heeft tot de Azure lente-Cloud voordat u begint met deze Snelstartgids.  Als preview-service vragen we klanten om aan ons te bellen zodat we uw abonnement kunnen toevoegen aan onze acceptatie lijst.  Als u de mogelijkheden van Azure lente Cloud wilt verkennen, kunt u contact met ons opnemen via e-mail: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3,0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten op het Azure Portal

1. Open in een webbrowser [deze koppeling naar Azure lente Cloud in de Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/Azure%20Spring%20Cloud).

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/goto-portal.png)

1. Selecteer **Azure veer Cloud** om naar de overzichts pagina te gaan. Selecteer vervolgens de knop **maken** om aan de slag te gaan.

1. Vul het formulier in, waarbij u rekening moet houden met de volgende richt lijnen:
    - Service naam: Geef de naam van uw service-exemplaar op.  De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten.  Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - Abonnement: Selecteer het abonnement dat u voor deze resource wilt factureren.  Zorg ervoor dat dit abonnement is toegevoegd aan onze acceptatie lijst voor Azure lente-Cloud.
    - Resource groep: het maken van nieuwe resource groepen voor nieuwe resources is een best practice.
    - Locatie: Selecteer de locatie voor uw service-exemplaar. Op dit moment worden de volgende locaties ondersteund: VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.
    
Het duurt ongeveer vijf minuten voordat de service wordt geïmplementeerd.  Zodra de app is geïmplementeerd, wordt de **overzichts** pagina voor het service-exemplaar weer gegeven.

## <a name="set-up-your-configuration-server"></a>De configuratie server instellen

1. Ga naar de **overzichts** pagina van de service en selecteer **Configuratie server**.

1. In de sectie **standaard opslagplaats** stelt u de **URI** in op ' https\://github.com/azure-samples/piggymetrics ', stelt u een **Label** in op ' config ' en selecteert u **Toep assen** om uw wijzigingen op te slaan.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

## <a name="build-and-deploy-microservice-applications"></a>Micro service-toepassingen bouwen en implementeren

1. Open een opdracht venster en voer de volgende opdracht uit om de opslag plaats van de voor beeld-app te klonen op uw lokale computer.

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Bouw het project door de onderstaande opdracht uit te voeren.

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Meld u aan bij Azure CLI en stel uw actieve abonnement in.

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
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

    ```cli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Het duurt enkele minuten om de implementatie van de toepassingen te volt ooien. Als u wilt bevestigen dat ze zijn geïmplementeerd, gaat u naar de Blade **apps** in het Azure Portal. U ziet een regel voor elk van de drie toepassingen.

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eind punt toewijzen aan de gateway

1. Open het tabblad **apps** in het menu aan de linkerkant.
2. Selecteer de `gateway`-toepassing om de pagina **overzicht** weer te geven.
3. Selecteer **domein toewijzen** om een openbaar eind punt toe te wijzen aan de gateway. Dit kan een paar minuten duren.

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
