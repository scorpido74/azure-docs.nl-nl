---
title: Zelfstudie - Gedistribueerde tracering gebruiken met Azure Spring Cloud
description: In deze zelfstudie wordt uitgelegd hoe u De Gedistribueerde Tracering van Spring Cloud gebruiken via Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273211"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Gedistribueerde tracering gebruiken met Azure Spring Cloud

Met de gedistribueerde traceringstools in Azure Spring Cloud u complexe problemen eenvoudig opsporen en bewaken. Azure Spring Cloud integreert [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met Azure's [Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) Deze integratie biedt krachtige gedistribueerde traceringsmogelijkheden vanuit de Azure-portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Gedistribueerde tracering inschakelen in de Azure-portal.
> * Voeg Spring Cloud Sleuth toe aan uw toepassing.
> * Bekijk afhankelijkheidskaarten voor uw microservicetoepassingen.
> * Zoek met verschillende filters naar traceringsgegevens.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u een Azure Spring Cloud-service nodig die al is ingericht en uitgevoerd. Voltooi de [quickstart bij het implementeren van een app via de Azure CLI](spring-cloud-quickstart-launch-app-cli.md) voor het inrichten en uitvoeren van een Azure Spring Cloud-service.
    
## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Voeg de volgende regel toe aan het bestand application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Na deze wijziging kan de zipkin-afzender naar het web worden verzonden.

1. Sla deze stap over als u onze [handleiding voor het voorbereiden van een Azure Spring Cloud-toepassing hebt](spring-cloud-tutorial-prepare-app-deployment.md)gevolgd. Ga anders naar uw lokale ontwikkelomgeving en bewerk uw pom.xml-bestand om de volgende afhankelijkheid van Spring Cloud Sleuth op te nemen:

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

1. Opnieuw bouwen en implementeren voor uw Azure Spring Cloud-service om deze wijzigingen weer te geven.

## <a name="modify-the-sample-rate"></a>De steekproefsnelheid wijzigen

U de snelheid waarmee uw telemetrie wordt verzameld wijzigen door de steekproefsnelheid te wijzigen. Als u bijvoorbeeld de helft zo vaak wilt samplen, opent u het bestand application.properties en wijzigt u de volgende regel:

```xml
spring.sleuth.sampler.probability=0.5
```

Als u een toepassing al hebt gebouwd en geïmplementeerd, u de voorbeeldsnelheid wijzigen. Dit doe je door de vorige regel toe te voegen als omgevingsvariabele in de Azure CLI of de Azure-portal.

## <a name="enable-application-insights"></a>Application Insights inschakelen

1. Ga naar uw Azure Spring Cloud-servicepagina in de Azure-portal.
1. Selecteer **op** de pagina Controle de optie **Gedistribueerde tracering**.
1. Selecteer **Instelling bewerken** om een nieuwe instelling te bewerken of toe te voegen.
1. Maak een nieuwe query Application Insights of selecteer een bestaande query.
1. Kies welke logboekcategorie u wilt controleren en geef de bewaartijd in dagen op.
1. Selecteer **Toepassen** om de nieuwe tracering toe te passen.

## <a name="view-the-application-map"></a>De toepassingskaart weergeven

Ga terug naar de pagina **Gedistribueerde tracering** en selecteer **Toepassingskaart weergeven**. Bekijk de visuele weergave van uw toepassings- en bewakingsinstellingen. Zie [Toepassingskaart: Triage gedistribueerde toepassingen](https://docs.microsoft.com/azure/azure-monitor/app/app-map)voor meer informatie over het gebruik van de toepassingskaart.

## <a name="use-search"></a>Zoeken gebruiken

Gebruik de zoekfunctie om te zoeken naar andere specifieke telemetrie-items. Selecteer op de pagina **Gedistribueerde tracering** de optie **Zoeken**. Zie [Zoeken gebruiken in toepassingsinzichten](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)voor meer informatie over het gebruik van de zoekfunctie.

## <a name="use-application-insights"></a>Toepassingsinzichten gebruiken

Application Insights biedt naast de toepassingskaart en zoekfunctie ook bewakingsmogelijkheden. Zoek in de Azure-portal naar de naam van uw toepassing en open vervolgens een pagina Toepassingsstatistieken om bewakingsgegevens te vinden. Bekijk [Azure Monitor-logboekquery's](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)voor meer informatie over het gebruik van deze hulpprogramma's.

## <a name="disable-application-insights"></a>Toepassingsinzichten uitschakelen

1. Ga naar uw Azure Spring Cloud-servicepagina in de Azure-portal.
1. Selecteer **Gedistribueerde tracering** **bij Controle**.
1. Selecteer **Uitschakelen** om Toepassingsinzichten uit te schakelen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u gedistribueerde tracering in Azure Spring Cloud inschakelen en begrijpen. Ga door naar de volgende zelfstudie voor meer informatie over het binden van uw toepassing aan een Azure Cosmos DB-database.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u binden aan een Azure Cosmos DB-database](spring-cloud-tutorial-bind-cosmos.md)
