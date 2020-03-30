---
title: Snelstart - Een toepassing starten met Maven met Azure Spring Cloud
description: Start in deze quickstart een voorbeeldtoepassing met Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: d1e6d6db1465b20f1f32a8ffb2f978d0a04a1033
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470840"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Snelstart: een Azure Spring Cloud-app starten met de Maven-plug-in

Met de Azure Spring Cloud Maven-plug-in u uw Azure Spring Cloud-toepassingen eenvoudig maken en bijwerken. Door een configuratie vooraf te definiëren, u toepassingen implementeren voor uw bestaande Azure Spring Cloud-service. In dit artikel gebruikt u een voorbeeldtoepassing genaamd PiggyMetrics om deze functie aan te tonen.

Na deze snelle start leert u hoe u:

> [!div class="checklist"]
> * Een service-instantie inrichten
> * Een configuratieserver instellen voor een instantie
> * Lokaal kloon- en bouwtoepassing voor microservices
> * Elke microservice implementeren
> * Een openbaar eindpunt toewijzen aan uw toepassing

>[!Note]
> Azure Spring Cloud wordt momenteel aangeboden als een openbare preview. Openbare preview-aanbiedingen stellen klanten in staat om te experimenteren met nieuwe functies voordat ze officieel worden uitgebracht.  Openbare preview-functies en services zijn niet bedoeld voor productiegebruik.  Bekijk onze veelgestelde vragen over onze [veelgestelde vragen](https://azure.microsoft.com/support/faq/) voor meer informatie over ondersteuning tijdens previews of dien een [ondersteuningsverzoek](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) in voor meer informatie.


>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u gebruiken om de opdrachten in dit artikel uit te voeren. Het heeft gemeenschappelijke Azure-hulpprogramma's vooraf geïnstalleerd, waaronder de nieuwste versies van Git, de Java Development Kit (JDK), Maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u [Azure Cloud Shell](https://shell.azure.com). Zie [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md)voor meer informatie.

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Installeer Git](https://git-scm.com/).
2. [Installeer JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installeer Maven 3.0 of hoger](https://maven.apache.org/download.cgi).
4. [Meld u aan voor een gratis Azure-abonnement.](https://azure.microsoft.com/free/)

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-instantie inrichten op de Azure-portal

1. Open deze koppeling naar Azure Spring Cloud in een webbrowser [in de Azure-portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)en meld u aan bij uw account.

1. Geef de **projectgegevens** voor de voorbeeldaanvraag als volgt op:

    1. Selecteer het **abonnement** waaraan de toepassing wordt gekoppeld.
    1. Selecteer of maak een resourcegroep voor de toepassing. We raden u aan een nieuwe brongroep te maken.  In het onderstaande voorbeeld `myspringservice`ziet u een nieuwe resourcegroep met de naam .
    1. Geef een naam op voor de nieuwe Azure Spring Cloud-service.  De naam moet tussen de 4 en 32 tekens lang zijn en kan alleen kleine letters, cijfers en koppeltekens bevatten. Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een getal zijn.  De service in het onderstaande voorbeeld heeft de naam `contosospringcloud`.
    1. Selecteer een locatie voor uw toepassing in de opgegeven opties.  In dit voorbeeld `East US`selecteren we .
    1. Selecteer **Controleren + maken** om een overzicht van uw nieuwe service te bekijken.  Als alles er goed uitziet, selecteert u **Maken**.

    > [!div class="mx-imgBorder"]
    > ![Selecteer Controleren + maken](media/maven-qs-review-create.jpg)

Het duurt ongeveer 5 minuten voordat de service is geïmplementeerd. Nadat de service is geïmplementeerd, selecteert u **Ga naar resource** en wordt de pagina **Overzicht** voor de serviceinstantie weergegeven.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Uw configuratieserver instellen

1. Selecteer **Config Server**op de pagina **ServiceOverzicht** .
1. Stel **uri** in de sectie **https://github.com/Azure-Samples/piggymetrics-config** **Standaardopslagplaats** in op , en selecteer **Toepassen** om de wijzigingen op te slaan.

    > [!div class="mx-imgBorder"]
    > ![Config-instellingen definiëren en toepassen](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>De opslagplaats voor voorbeeldtoepassingen klonen en bouwen

1. Start de [Azure Cloud Shell](https://shell.azure.com).

1. Kloon de Git-opslagplaats door de volgende opdracht uit te voeren:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Configuraties genereren en implementeren in de Azure Spring Cloud

1. Genereer configuraties door de volgende opdracht uit te voeren in de hoofdmap van PiggyMetrics met de bovenliggende POM:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. Selecteer de `gateway``auth-service`modules `account-service`, en .

    b. Selecteer uw abonnement en Azure Spring Cloud-servicecluster.

    c. Vermeld in de lijst van verstrekte projecten `gateway` het nummer waarmee het overeenkomt om het publiek toegang te geven.
    
    d. Bevestig de configuratie.

1. De POM bevat nu de afhankelijkheden en configuraties van de plug-in. Implementeer de apps met de volgende opdracht:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Nadat de implementatie is voltooid, u PiggyMetrics openen via de URL in de uitvoer van de vorige opdracht.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Spring Cloud-toepassing geïmplementeerd vanuit een Maven-repository. Ga verder met de zelfstudie over het voorbereiden van uw app op implementatie voor meer informatie over Azure Spring Cloud.

> [!div class="nextstepaction"]
> [Uw Azure Spring Cloud-toepassing voorbereiden op implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
> [Meer informatie over Maven-plug-ins voor Azure](https://github.com/microsoft/azure-maven-plugin)

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
