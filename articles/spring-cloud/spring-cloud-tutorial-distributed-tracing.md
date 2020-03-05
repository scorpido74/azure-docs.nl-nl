---
title: Zelf studie-gedistribueerde tracering gebruiken met Azure veer Cloud
description: In deze zelf studie wordt uitgelegd hoe u de gedistribueerde tracering van Lente-Clouds via Azure-toepassing Insights gebruikt
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273211"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Gedistribueerde tracering gebruiken met Azure veer Cloud

Met de gedistribueerde hulpprogram ma's voor tracering in azure lente-Cloud kunt u eenvoudig complexe problemen opsporen en bewaken. Azure lente Cloud integreert [lente Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met de [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)van Azure. Deze integratie biedt krachtige functies voor gedistribueerde tracering van de Azure Portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Schakel gedistribueerde tracering in de Azure Portal in.
> * Een lente Cloud Sleuth toevoegen aan uw toepassing.
> * Afhankelijkheids kaarten voor uw micro service-toepassingen weer geven.
> * Zoek gegevens traceren met verschillende filters.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u een Azure lente-Cloud service nodig die al is ingericht en actief is. Voltooi de [Snelstartgids voor het implementeren van een app via de Azure cli](spring-cloud-quickstart-launch-app-cli.md) om een Azure lente-Cloud service in te richten en uit te voeren.
    
## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Voeg de volgende regel toe aan het bestand Application. Properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Na deze wijziging kan de Zipkin-afzender naar het web worden verzonden.

1. Sla deze stap over als u onze [hand leiding voor het voorbereiden van een Azure lente-Cloud toepassing](spring-cloud-tutorial-prepare-app-deployment.md)hebt gevolgd. Als dat niet het geval is, gaat u naar uw lokale ontwikkel omgeving en bewerkt u het bestand pom. XML om de volgende afhankelijkheids-Sleuth-afhankelijkheid toe te voegen:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Bouw en implementeer opnieuw voor de Azure lente-Cloud service om deze wijzigingen weer te geven.

## <a name="modify-the-sample-rate"></a>De sample frequentie wijzigen

U kunt de snelheid waarmee uw telemetrie wordt verzameld, wijzigen door de sample frequentie aan te passen. Als u bijvoorbeeld de helft wilt bemonsteren, opent u het bestand Application. Properties en wijzigt u de volgende regel:

```xml
spring.sleuth.sampler.probability=0.5
```

Als u een toepassing al hebt gemaakt en geïmplementeerd, kunt u de sample frequentie wijzigen. Dit doet u door de vorige regel als een omgevings variabele in de Azure CLI of de Azure Portal toe te voegen.

## <a name="enable-application-insights"></a>Application Insights inschakelen

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal.
1. Selecteer op de pagina **controle** de optie **gedistribueerde tracering**.
1. Selecteer **instelling bewerken** om een nieuwe instelling te bewerken of toe te voegen.
1. Maak een nieuwe Application Insights query of selecteer een bestaande.
1. Kies welke logboek registratie categorie u wilt bewaken en geef de Bewaar tijd op in dagen.
1. Selecteer **Toep assen** om de nieuwe tracering toe te passen.

## <a name="view-the-application-map"></a>De toepassings toewijzing weer geven

Ga terug naar de pagina **gedistribueerde tracering** en selecteer **toepassings toewijzing weer geven**. Bekijk de visuele weer gave van uw toepassings-en bewakings instellingen. Zie [Application map: sorteren Distributed Applications](https://docs.microsoft.com/azure/azure-monitor/app/app-map)(Engelstalig) voor meer informatie over het gebruik van de toepassings toewijzing.

## <a name="use-search"></a>Zoek opdracht gebruiken

Gebruik de zoek functie om te zoeken naar andere specifieke telemetrie-items. Selecteer op de pagina **gedistribueerde tracering** **zoeken**. Zie [zoeken gebruiken in Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)voor meer informatie over het gebruik van de zoek functie.

## <a name="use-application-insights"></a>Application Insights gebruiken

Application Insights biedt bewakings mogelijkheden naast het toepassings overzicht en de zoek functie. Zoek in het Azure Portal naar de naam van uw toepassing en open vervolgens een Application Insights pagina om controle-informatie te vinden. Raadpleeg [Azure monitor-logboek query's](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)voor meer informatie over het gebruik van deze hulpprogram ma's.

## <a name="disable-application-insights"></a>Application Insights uitschakelen

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal.
1. Selecteer bij **bewaking** **gedistribueerde tracering**.
1. Selecteer **uitschakelen** om Application Insights uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u gedistribueerde tracering in azure lente-Cloud kunt inschakelen en begrijpen. Ga verder met de volgende zelf studie als u wilt weten hoe u uw toepassing verbindt met een Azure Cosmos DB-Data Base.

> [!div class="nextstepaction"]
> [Meer informatie over het verbinden met een Azure Cosmos DB-Data Base](spring-cloud-tutorial-bind-cosmos.md)
