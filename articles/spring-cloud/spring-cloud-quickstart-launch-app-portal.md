---
title: 'Snelstartgids: een bestaande Azure lente-Cloud toepassing starten met behulp van de Azure Portal'
description: In deze Quick Start implementeert u een lente-Cloud toepassing in de Azure lente-Cloud met behulp van de Azure Portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: 9cd59fdf81e9b5d56872d20c76e8ea177b3c8577
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79470891"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Snelstartgids: een bestaande Azure lente-Cloud toepassing starten met behulp van de Azure Portal

In deze Quick start ziet u hoe u een bestaande lente-Cloud toepassing kunt implementeren in Azure. Met Azure lente Cloud kunt u eenvoudig lente-Cloud toepassingen uitvoeren op Azure. 

De voorbeeld toepassings code die in deze zelf studie wordt gebruikt, is te vinden in onze [github samples-opslag plaats](https://github.com/Azure-Samples/PiggyMetrics). Wanneer u klaar bent, is de beschik bare voorbeeld toepassing online en kan deze worden beheerd via de Azure Portal.

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

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3,0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [De Azure CLI-versie 2.0.67 of hoger installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten op het Azure Portal

1. Open de [Azure Portal](https://ms.portal.azure.com/)op een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar **Azure lente Cloud**.

3. Selecteer **Azure veer Cloud** in de resultaten.

 ![Pictogram ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op de pagina Azure lente-Cloud op **+ toevoegen**.

 ![Pictogram ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de pagina Azure lente-Cloud **maken** .  Houd rekening met de volgende richt lijnen:
    - **Abonnement**: Selecteer het abonnement dat u voor deze resource wilt factureren.  Zorg ervoor dat dit abonnement is toegevoegd aan onze acceptatie lijst voor Azure lente-Cloud.
    - **Resource groep**: het maken van nieuwe resource groepen voor nieuwe resources is een Best Practice.
    - **Service Details/naam**: Geef de naam van uw service-exemplaar op.  De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten.  Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - **Locatie**: Selecteer de locatie voor uw service-exemplaar. Op dit moment worden de volgende locaties ondersteund: VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.

    ![ASC-Portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op het tabblad **Diagnostische instelling** om het volgende dialoog venster te openen.

7. U kunt **Logboeken inschakelen** op *Ja* of *Nee* volgens uw vereisten.

    ![Logboeken inschakelen](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klik op het tabblad **tracering** .

9. U kunt **Tracering inschakelen** op *Ja* of *Nee* volgens uw vereisten.  Als u **Tracering inschakelen** op Ja hebt ingesteld, selecteert u ook een bestaande toepassing inzicht of maakt u een nieuwe. Zonder de **Application Insights** specificatie wordt er een validatie fout weer.


    ![Tracering](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klik op **Controleren en maken**.

11. Controleer uw specificaties en klik op **maken**.

Het duurt ongeveer vijf minuten voordat de service wordt geïmplementeerd.  Zodra de app is geïmplementeerd, wordt de **overzichts** pagina voor het service-exemplaar weer gegeven.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>De configuratie server instellen

1. Ga naar de **overzichts** pagina van de service en selecteer **Configuratie server**.

2. Stel in de sectie **standaard opslagplaats** de **URI** in ophttps://github.com/Azure-Samples/piggymetrics-config' '.

3. Selecteer **Apply** om uw wijzigingen op te slaan.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Micro service-toepassingen bouwen en implementeren

1. Open een [Azure Cloud shell](https://shell.azure.com) en kloon de opslag plaats van de voor beeld-app naar uw lokale computer.  Hier maken we eerst een tijdelijke map met de `source-code` naam voordat ze de app klonen.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Bouw het gekloonde pakket.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Namen toewijzen aan de resource groep en uw service. Zorg ervoor dat u de onderstaande tijdelijke aanduidingen vervangt door de naam van de resource groep en de service naam die u eerder in deze zelf studie hebt ingericht.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Maak de `gateway` toepassing en implementeer het jar-bestand.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Volg hetzelfde patroon en maak de `account-service` `auth-service` toepassingen en implementeer de jar-bestanden.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Het duurt enkele minuten om de implementatie van de toepassingen te volt ooien. Als u wilt bevestigen dat ze zijn geïmplementeerd, gaat u naar de Blade **apps** in het Azure Portal. U ziet een regel voor elk van de drie toepassingen.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eind punt toewijzen aan de gateway

1. Open het tabblad **apps** in het menu aan de linkerkant.

2. Selecteer de `gateway` toepassing om de **overzichts** pagina weer te geven.

3. Selecteer **eind punt toewijzen** om een openbaar eind punt toe te wijzen aan de gateway. Dit kan enkele minuten duren.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Voer het toegewezen open bare eind punt ( **URL**met label) in uw browser in om de actieve toepassing weer te geven.

    ![Scherm opname van ASC-Portal](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server instellen voor een exemplaar
> * Een micro Services-toepassing lokaal bouwen
> * Implementeer elke micro service
> * Openbaar eind punt toewijzen voor uw toepassings gateway

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voor beelden zijn beschikbaar op GitHub: [Azure lente-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
