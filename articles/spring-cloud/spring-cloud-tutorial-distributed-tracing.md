---
title: Zelf studie-gedistribueerde tracering gebruiken met Azure veer Cloud
description: In deze zelf studie wordt uitgelegd hoe u de gedistribueerde tracering van Lente-Clouds via Azure-toepassing Insights gebruikt
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 9c049ecbea3c630e0f7d08e4a42bd441ba3f5cfa
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708761"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Zelf studie: gedistribueerde tracering met Azure lente-Cloud gebruiken

De gedistribueerde traceer hulpprogram ma's van de lente-Cloud bieden eenvoudige fout opsporing en controle van complexe problemen. Azure lente Cloud integreert de toonaangevende [Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met de [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) van Azure om krachtige gedistribueerde tracerings mogelijkheden te bieden vanuit de Azure Portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Gedistribueerde tracering inschakelen in de Azure Portal
> * De lente Cloud Sleuth toevoegen aan uw toepassing
> * Afhankelijkheids kaarten voor uw micro service-toepassingen weer geven
> * Zoeken naar gegevens traceren met verschillende filters

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* Een reeds ingerichte en actieve Azure lente-Cloud service.  Voltooi [deze Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om een Azure lente-Cloud service in te richten en te starten.
    
## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Schakel de zipkin-afzender in om naar het web te verzenden door de volgende regel toe te voegen aan het bestand Application. Properties:

```xml
spring.zipkin.sender.type = web
```

U kunt de volgende stap overs Laan als u onze [hand leiding voor het bereidt van een Azure lente-Cloud toepassing](spring-cloud-tutorial-prepare-app-deployment.md)hebt gevolgd. Als dat niet het geval is, gaat u naar uw lokale ontwikkel omgeving en bewerkt u uw `pom.xml`-bestand om de lente-Sleuth afhankelijkheid voor de Cloud op te

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

* Bouw en implementeer opnieuw voor de Azure lente-Cloud service om deze wijzigingen weer te geven. 

## <a name="modify-the-sample-rate"></a>De sample frequentie wijzigen
U kunt de snelheid waarmee uw telemetrie wordt verzameld, wijzigen door de sample frequentie aan te passen. Als u bijvoorbeeld vaak een steek proef wilt doen, gaat u naar uw `application.properties`-bestand en wijzigt u de volgende regel:

```xml
spring.sleuth.sampler.probability=0.5
```

Als u al een toepassing hebt gemaakt en geïmplementeerd, kunt u de sample frequentie wijzigen door de bovenstaande regel toe te voegen als een omgevings variabele in de Azure CLI of portal. 

## <a name="enable-application-insights"></a>Application Insights inschakelen

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal.
1. Selecteer **gedistribueerde tracering**in de sectie bewaking.
1. Selecteer **instelling bewerken** om een nieuwe instelling te bewerken of toe te voegen.
1. Maak een nieuwe toepassing inzicht query of u kunt een bestaand account selecteren.
1. Kies welke logboek registratie categorie u wilt bewaken en geef de Bewaar tijd op (in dagen).
1. Selecteer **Toep assen** om de nieuwe tracering toe te passen.

## <a name="view-application-map"></a>Toepassings overzicht weer geven

Ga terug naar de pagina gedistribueerde tracering en selecteer **toepassings toewijzing weer geven**. Bekijk de visuele weer gave van uw toepassings-en bewakings instellingen. Zie [dit artikel](https://docs.microsoft.com/azure/azure-monitor/app/app-map)voor meer informatie over het gebruik van de toepassings toewijzing.

## <a name="search"></a>Search

Gebruik de functie Search om andere specifieke telemetrie-items te doorzoeken. Selecteer op de pagina **gedistribueerde tracering** **zoeken**. Zie [dit artikel](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)voor meer informatie over het gebruik van de functie Search.

## <a name="application-insights-page"></a>Application Insights pagina

Application Insights biedt bewakings mogelijkheden naast het toepassings overzicht en zoeken. Zoek in het Azure Portal naar de naam van uw toepassing en start vervolgens een Application Insights pagina voor meer informatie. [Raadpleeg de documentatie](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)voor meer informatie over het gebruik van deze hulpprogram ma's.


## <a name="disable-application-insights"></a>Application Insights uitschakelen

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal.
1. Klik in de sectie bewaking op **gedistribueerde tracering**.
1. Klik op **uitschakelen** om Application Insights uit te scha kelen

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u gedistribueerde tracering in de Azure lente-Cloud kunt inschakelen en begrijpen. Ga verder met de volgende zelf studie als u wilt weten hoe u uw toepassing kunt binden aan een Azure-CosmosDB.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u uw toepassing kunt binden aan een Azure-CosmosDB](spring-cloud-tutorial-bind-cosmos.md).
