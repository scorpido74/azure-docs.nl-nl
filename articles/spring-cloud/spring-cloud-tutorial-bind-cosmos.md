---
title: Zelfstudie - Een Azure Cosmos DB binden aan uw Azure Spring Cloud-toepassing
description: In deze zelfstudie leert u hoe u Azure Cosmos DB binden aan uw Azure Spring Cloud-toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277567"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Een Azure Cosmos DB-database binden aan uw Azure Spring Cloud-toepassing

In plaats van uw Spring Boot-toepassingen handmatig te configureren, u bepaalde Azure-services automatisch aan uw toepassingen binden met Azure Spring Cloud. In dit artikel wordt uitgelegd hoe u uw toepassing binden aan een Azure Cosmos DB-database.

Vereisten:

* Een geïmplementeerde Azure Spring Cloud-instantie. Volg onze [quickstart bij het implementeren via de Azure CLI](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Een Azure Cosmos DB-account met een minimummachtigingsniveau van inzender.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB binden

Azure Cosmos DB heeft vijf verschillende API-typen die binding ondersteunen. De volgende procedure laat zien hoe ze te gebruiken:

1. Hiermee maakt u een Azure Cosmos DB-database. Raadpleeg de snelstart bij [het maken van een database](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) voor hulp. 

1. Neem de naam van uw database op. Voor deze procedure is de databasenaam **testdb**.

1. Voeg een van de volgende afhankelijkheden toe aan het pom.xml-bestand van uw Azure Spring Cloud-toepassing. Kies de afhankelijkheid die geschikt is voor uw API-type.

    * API-type: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API-type: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API-type: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API-type: Gremlin (grafiek)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API-type: Azure-tabel

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Met `az spring-cloud app update` deze functie u `az spring-cloud app deployment create` de huidige implementatie bijwerken of gebruiken om een nieuwe implementatie te maken. Deze opdrachten worden bijgewerkt of maken de toepassing met de nieuwe afhankelijkheid.

1. Ga naar uw Azure Spring Cloud-servicepagina in de Azure-portal. Ga naar **Toepassingsdashboard** en selecteer de toepassing die u wilt binden aan Azure Cosmos DB. Deze toepassing is dezelfde toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd.

1. Selecteer **Servicebinding**en selecteer **Servicebinding maken**. Als u het formulier wilt invullen, selecteert u:
   * De **waarde van het type Binding** Azure Cosmos **DB**.
   * Het API-type.
   * Je databasenaam.
   * Het Azure Cosmos DB-account.

    > [!NOTE]
    > Als u Cassandra gebruikt, gebruikt u een sleutelruimte voor de databasenaam.

1. Start de toepassing opnieuw door **Opnieuw starten** te selecteren op de toepassingspagina.

1. Als u wilt weten of de service correct is gebonden, selecteert u de bindende naam en controleert u de details ervan. Het `property` veld moet vergelijkbaar zijn met dit voorbeeld:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw Azure Spring Cloud-toepassing binden aan een Azure Cosmos DB-database. Ga door naar de volgende zelfstudie voor meer informatie over het binden van uw toepassing aan een Azure-cache voor Redis-cache.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u binden aan een Azure-cache voor Redis-cache](spring-cloud-tutorial-bind-redis.md)
