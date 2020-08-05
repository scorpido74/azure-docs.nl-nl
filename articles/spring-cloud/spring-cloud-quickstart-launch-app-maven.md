---
title: 'Quickstart: een toepassing starten met behulp van Maven met Azure Spring Cloud'
description: In deze quickstart start u een voorbeeldtoepassing met behulp van Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 9ae6524dc1d53580d0dc7de1179ccf9db8cebc61
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086129"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Quickstart: een Azure Spring Cloud-toepassing starten met behulp van de Maven-invoegtoepassing

Met de Maven-invoegtoepassing van Azure Spring Cloud kunt u eenvoudig uw Azure Spring Cloud-toepassingen maken en bijwerken. Door vooraf een configuratie te definiëren, kunt u toepassingen implementeren in uw bestaande Azure Spring Cloud-service. In dit artikel gebruikt u een voorbeeldtoepassing met de naam PiggyMetrics om deze functie te demonstreren.

In deze quickstart leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratieserver instellen voor een exemplaar
> * Een microservicetoepassing lokaal klonen en bouwen
> * Alle microservices implementeren
> * Een openbaar eindpunt voor uw toepassing toewijzen

>[!Note]
> Azure Spring Cloud wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunnen klanten voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Voor meer informatie over ondersteuning tijdens previews kunt u onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) raadplegen of een [Ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) indienen.


>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de opdrachten in dit artikel kunt uitvoeren. Deze bevat vooraf geïnstalleerde algemene Azure-hulpprogramma's, met inbegrip van de nieuwste versies van Git, de Java Development Kit (JDK), Maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u [Azure Cloud Shell](https://shell.azure.com). Zie [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie.

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Installeer Git](https://git-scm.com/).
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi).
4. [Aanmelden aan voor een gratis proefversie van Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten in Azure Portal

1. Open in een webbrowser [deze koppeling naar Azure Spring Cloud in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform) en meld u aan bij uw account.

1. Geef de **projectdetails** voor de voorbeeldtoepassing als volgt op:

    1. Selecteer het **abonnement** waaraan de toepassing wordt gekoppeld.
    1. Selecteer of maak een resourcegroep voor de toepassing. We raden u aan om een nieuwe resourcegroep te maken.  In het onderstaande voorbeeld ziet u een nieuwe resourcegroep met de naam `myspringservice`.
    1. Geef een naam op voor de nieuwe Azure Spring Cloud-service.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten. Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.  De service in het onderstaande voorbeeld heeft de naam `contosospringcloud`.
    1. Selecteer een locatie voor uw toepassing uit de beschikbare opties.  In dit voorbeeld selecteren we `East US`.
    1. Selecteer **Beoordelen en maken** om een overzicht van de nieuwe service te bekijken.  Als alles er goed uitziet, selecteert u **Maken**.

    > [!div class="mx-imgBorder"]
    > ![Beoordelen en maken](media/maven-qs-review-create.jpg) selecteren

Het implementeren van de service duurt ongeveer vijf minuten. Nadat de service is geïmplementeerd, selecteert u **Ga naar resource**. De pagina **Overzicht** voor het service-exemplaar wordt dan weergegeven.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Uw configuratieserver instellen

1. Selecteer op de pagina **Overzicht** van de service de optie **Config Server**.
1. Stel in de sectie **Standaardopslagplaats** de **URI** in op **https://github.com/Azure-Samples/piggymetrics-config** en selecteer vervolgens **Toepassen** om uw wijzigingen op te slaan.

    > [!div class="mx-imgBorder"]
    > ![Configuratie-instellingen definiëren en toepassen](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>De voorbeeldtoepassingsopslagplaats klonen en bouwen

1. Start de [Azure Cloud Shell](https://shell.azure.com).

1. Voer de volgende opdracht uit om de Git-opslagplaats te klonen:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Configuraties genereren en implementeren in de Azure Spring Cloud

1. Genereer configuraties door de volgende opdracht uit te voeren in de hoofdmap van PiggyMetrics die de bovenliggende POM bevat:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Selecteer de modules `gateway`, `auth-service` en `account-service`.

    b. Selecteer uw abonnement en Azure Spring Cloud-servicecluster.

    c. Voer in de lijst met opgegeven projecten het getal in dat overeenkomt met `gateway` om het openbare toegang te verlenen.
    
    d. Bevestig de configuratie.

1. De POM bevat nu de afhankelijkheden en configuraties van de invoegtoepassing. Implementeer de toepassingen met behulp van de volgende opdracht:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Nadat de implementatie is voltooid, kunt u toegang krijgen tot PiggyMetrics met behulp van de URL die wordt vermeld in de uitvoer van de voorgaande opdracht.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Spring Cloud-toepassing geïmplementeerd vanuit een Maven-opslagplaats. Ga verder met de zelfstudie over het voorbereiden van uw app voor implementatie voor meer informatie over Azure Spring Cloud.

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
> [Meer informatie over Maven-invoegtoepassingen voor Azure](https://github.com/microsoft/azure-maven-plugins)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
