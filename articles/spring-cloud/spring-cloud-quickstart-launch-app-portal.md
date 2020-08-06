---
title: Quickstart - Een bestaande Azure Spring Cloud-toepassing starten met behulp van Azure Portal
description: In deze quickstart implementeert u een Spring Cloud-toepassing in de Azure Spring Cloud met behulp van Azure Portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 09820da3fcebe3614852f36180a29f35505039b8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496845"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Quickstart: Een bestaande Azure Spring Cloud-toepassing starten met behulp van Azure Portal

In deze quickstart ziet u hoe u een bestaande Spring Cloud-toepassing implementeert in Azure. Met Azure Spring Cloud kunt u eenvoudig op Spring Cloud gebaseerde microservicetoepassingen uitvoeren in Azure. 

De voorbeeldtoepassingscode die in deze zelfstudie wordt gebruikt, is te vinden in onze [opslagplaats voor GitHub-voorbeelden](https://github.com/Azure-Samples/PiggyMetrics). Wanneer u klaar bent, is de gegeven voorbeeldtoepassing online toegankelijk en kan deze worden beheerd via Azure Portal.

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten in Azure Portal

1. Open [Azure Portal](https://ms.portal.azure.com/) op een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar **Azure Spring Cloud**.

3. Selecteer **Azure Spring Cloud** in de resultaten.

 ![ASC-pictogram](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op **+ Toevoegen** op de Azure Spring Cloud-pagina.

 ![ASC-pictogram](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.  Houd rekening met de volgende richtlijnen:
    - **Abonnement**: Selecteer het abonnement waarvoor u voor deze resource gefactureerd wilt worden.  Controleer of dit abonnement is toegevoegd aan onze acceptatielijst voor Azure Spring Cloud.
    - **Resourcegroep**: Het is een best practice om nieuwe resourcegroepen te maken voor nieuwe resources.
    - **Servicedetails/naam**: Geef de naam op van uw service-exemplaar.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - **Locatie**: Selecteer de locatie voor uw service-exemplaar. Op dit moment worden de volgende locaties ondersteund: US - oost, US - west 2, Europa - west en Azië - zuidoost.

    ![ASC-portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op het tabblad **Diagnostische instelling** om het volgende dialoogvenster te openen.

7. U kunt **Logboeken inschakelen** instellen op *Ja* of op *Nee* volgens uw vereisten.

    ![Logboeken inschakelen](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klik op het tabblad **Tracering**.

9. U kunt **Tracering inschakelen** instellen op *Ja* of op *Nee* volgens uw vereisten.  Als u **Tracering inschakelen** instelt op Ja, selecteert u ook een bestaand toepassingsinzicht of maakt u een nieuwe. Zonder de specificatie **Application Insights** treedt er een validatiefout op.


    ![Tracering](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klik op **Controleren en maken**.

11. Controleer uw specificaties en klik op **Maken**.

Het duurt ongeveer vijf minuten om de service te implementeren.  Wanneer de app is geïmplementeerd, wordt de pagina **Overzicht** voor het service-exemplaar weergegeven.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Uw configuratieserver instellen

1. Ga naar de pagina **Overzicht** van de service en selecteer **Config Server**.

2. Stel in de sectie **Standaardopslagplaats** **URI** in op https://github.com/Azure-Samples/piggymetrics-config.

3. Selecteer **Toepassen** om uw wijzigingen op te slaan.

    ![Schermopname van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Microservicetoepassingen bouwen en implementeren

1. Open een [Azure Cloud Shell](https://shell.azure.com) of uw lokale shell waarop Azure CLI is geïnstalleerd. Hier maken we eerst een tijdelijke map met de naam `source-code` voordat we de voorbeeldapp klonen.

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

3. Voer de volgende opdracht uit om de Azure Spring Cloud-extensie voor de Azure CLI te installeren

    ```azurecli
    az extension add --name spring-cloud
    ```

4. Wijs namen toe aan uw resourcegroep en uw service. Vergeet niet de onderstaande tijdelijke aanduidingen te vervangen door de naam van de resourcegroep en de servicenaam die u eerder in deze zelfstudie hebt ingericht.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

5. Maak de toepassing `gateway` en implementeer het JAR-bestand.

    Maak de app met behulp van de Spring Cloud-extensie:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

6. Volg hetzelfde patroon en maak de toepassingen`account-service` en `auth-service`, en implementeer de JAR-bestanden.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

7. Het duurt enkele minuten om de implementatie van de toepassingen te voltooien. Als u wilt bevestigen dat ze zijn geïmplementeerd, gaat u naar het deelvenster **Apps** in Azure Portal. U ziet een regel voor elk van de drie toepassingen.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan de gateway

1. Open het tabblad **Apps** in het menu aan de linkerkant.

2. Selecteer de toepassing `gateway` om de pagina **Overzicht** weer te geven.

3. Selecteer **Eindpunt toewijzen** om een openbaar eindpunt toe te wijzen aan de gateway. Dit kan een paar minuten duren.

    ![Schermopname van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Voer het toegewezen openbare eindpunt (met het label **URL**) in uw browser in om de actieve toepassing weer te geven.

    ![Schermopname van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratieserver instellen voor een exemplaar
> * Lokaal een microservicetoepassing bouwen
> * Alle microservices implementeren
> * Een openbaar eindpunt voor uw toepassingsgateway toewijzen

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
