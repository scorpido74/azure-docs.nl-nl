---
title: Snelstart - Een bestaande Azure Spring Cloud-toepassing starten met de Azure-portal
description: Implementeer in deze quickstart een Spring Cloud-toepassing in de Azure Spring Cloud met behulp van de Azure-portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: 9cd59fdf81e9b5d56872d20c76e8ea177b3c8577
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470891"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Snelstart: een bestaande Azure Spring Cloud-toepassing starten met de Azure-portal

Met deze quickstart ziet u hoe u een bestaande Spring Cloud-toepassing implementeert in Azure. Azure Spring Cloud stelt u in staat om spring cloud-gebaseerde microservicetoepassingen eenvoudig op Azure uit te voeren. 

U vindt de voorbeeldtoepassingscode die wordt gebruikt in deze zelfstudie in onze [GitHub-voorbeeldenopslagplaats.](https://github.com/Azure-Samples/PiggyMetrics) Wanneer u klaar bent, is de meegeleverde voorbeeldtoepassing online toegankelijk en klaar om te worden beheerd via de Azure-portal.

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-instantie inrichten op de Azure-portal

1. Open de [Azure-portal](https://ms.portal.azure.com/)in een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar **Azure Spring Cloud**.

3. Selecteer **Azure Spring Cloud** in de resultaten.

 ![ASC-pictogram](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op de pagina Azure Spring Cloud op **+ Toevoegen**.

 ![ASC-pictogram](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de pagina Azure Spring Cloud **Create.**  Houd rekening met de volgende richtlijnen:
    - **Abonnement:** Selecteer het abonnement dat u voor deze bron wilt factureren.  Controleer of dit abonnement is toegevoegd aan onze lijst met toegestane opties voor Azure Spring Cloud.
    - **Resourcegroep**: Het maken van nieuwe resourcegroepen voor nieuwe resources is een aanbevolen praktijk.
    - **Servicegegevens/naam:** geef de naam van uw service-instantie op.  De naam moet tussen de 4 en 32 tekens lang zijn en kan alleen kleine letters, cijfers en koppeltekens bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een getal zijn.
    - **Locatie:** selecteer de locatie voor uw service-instantie. Momenteel ondersteunde locaties zijn Oost-VS, West US 2, West-Europa en Zuidoost-Azië.

    ![ASC-portal start](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op het tabblad **Diagnostische instelling** om het volgende dialoogvenster te openen.

7. U **Logboeken inschakelen** instellen op *ja* of *nee* op basis van uw vereisten.

    ![Logboeken inschakelen](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klik op het tabblad **Tracering.**

9. U **Tracering inschakelen** instellen op *ja* of *nee* op basis van uw vereisten.  Als u **Tracering inschakelen** op ja instelt, selecteert u ook een bestaand toepassingsinzicht of maakt u een nieuw systeem. Zonder de **Application Insights-specificatie** is er een validatiefout.


    ![Tracering](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klik op **Controleren en maken**.

11. Controleer uw specificaties en klik op **Maken**.

Het duurt ongeveer 5 minuten voordat de service is geïmplementeerd.  Zodra deze is geïmplementeerd, wordt de **pagina Overzicht** voor de service-instantie weergegeven.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Uw configuratieserver instellen

1. Ga naar de pagina **Serviceoverzicht** en selecteer **Config Server**.

2. Stel **URI** in de sectiehttps://github.com/Azure-Samples/piggymetrics-config **Standaardopslagplaats** in op " ".

3. Selecteer **Apply** om uw wijzigingen op te slaan.

    ![Schermafbeelding van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Microservicetoepassingen bouwen en implementeren

1. Open een [Azure Cloud Shell](https://shell.azure.com) en kloon de sample app repository naar uw lokale machine.  Hier maken we eerst een `source-code` tijdelijke map genaamd voor het klonen van de app.

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

3. Geef namen toe aan uw resourcegroep en uw service. Zorg ervoor dat u de tijdelijke aanduidingen hieronder vervangt door de naam en servicenaam van de resourcegroep die u eerder in deze zelfstudie hebt ingericht.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Maak `gateway` de toepassing en implementeer het JAR-bestand.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Volgens hetzelfde patroon `account-service` maakt `auth-service` u de toepassingen en toepassingen en implementeert u hun JAR-bestanden.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Het duurt een paar minuten om de implementatie van de toepassingen te voltooien. Als u wilt bevestigen dat ze zijn geïmplementeerd, gaat u naar het blade **Apps** in de Azure-portal. U ziet een regel elk van de drie toepassingen.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Een openbaar eindpunt toewijzen aan gateway

1. Open het tabblad **Apps** in het menu aan de linkerkant.

2. Selecteer `gateway` de toepassing om de pagina **Overzicht** weer te geven.

3. Selecteer **Eindpunt toewijzen** om een openbaar eindpunt toe te wijzen aan gateway. Dit kan enkele minuten duren.

    ![Schermafbeelding van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Voer het toegewezen openbare eindpunt (met het label **URL)** in uw browser in om de lopende toepassing weer te geven.

    ![Schermafbeelding van ASC-portal](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een service-instantie inrichten
> * Een configuratieserver instellen voor een instantie
> * Lokaal een microservices-toepassing bouwen
> * Elke microservice implementeren
> * Openbaar eindpunt toewijzen voor uw toepassingsgateway

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden op implementatie](spring-cloud-tutorial-prepare-app-deployment.md)

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
