---
title: 'Zelf studie: een Azure Cosmos DB binden aan uw Azure lente-Cloud toepassing'
description: In deze zelf studie leert u hoe u Azure Cosmos DB bindt aan uw Azure lente-Cloud toepassing
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 8eeb67419d2da90ff1e2d2beb8cb59a85c3bbacb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461616"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Een Azure Cosmos DB-Data Base koppelen aan uw Azure lente-Cloud toepassing

In plaats van uw Spring-opstart toepassingen hand matig te configureren, kunt u de geselecteerde Azure-Services automatisch binden aan uw toepassingen met behulp van Azure veer Cloud. In dit artikel wordt beschreven hoe u uw toepassing verbindt met een Azure Cosmos DB-Data Base.

Vereisten:

* Een geïmplementeerd Azure veer Cloud-instantie. Volg onze [Snelstartgids voor de implementatie via de Azure cli](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Een Azure Cosmos DB account met een mini maal machtigings niveau voor Inzender.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB binden

Azure Cosmos DB heeft vijf verschillende typen API die ondersteuning bieden voor binding. De volgende procedure laat zien hoe u deze gebruikt:

1. Hiermee maakt u een Azure Cosmos DB-database. Raadpleeg de Snelstartgids over het [maken van een Data Base](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) voor meer informatie. 

1. Noteer de naam van uw data base. Voor deze procedure is de naam van de Data Base **testdb**.

1. Voeg een van de volgende afhankelijkheden toe aan het pom. XML-bestand van uw Azure veer Cloud-toepassing. Kies de afhankelijkheid die geschikt is voor uw API-type.

    * API-type: core (SQL)

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

1. Gebruik `az spring-cloud app update` om de huidige implementatie bij te werken of gebruik `az spring-cloud app deployment create` om een nieuwe implementatie te maken. Met deze opdrachten kunt u de toepassing bijwerken of maken met de nieuwe afhankelijkheid.

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal. Ga naar het **toepassings dashboard** en selecteer de toepassing die u wilt verbinden met Azure Cosmos db. Deze toepassing is hetzelfde als die u in de vorige stap hebt bijgewerkt of geïmplementeerd.

1. Selecteer **service binding**en selecteer **service binding maken**. Selecteer om het formulier in te vullen:
   * De waarde van het **bindings type** **Azure Cosmos DB**.
   * Het API-type.
   * De naam van uw data base.
   * Het Azure Cosmos DB-account.

    > [!NOTE]
    > Als u Cassandra gebruikt, gebruikt u een sleutel ruimte voor de naam van de data base.

1. Start de toepassing opnieuw door **opnieuw opstarten** te selecteren op de pagina van de toepassing.

1. Om ervoor te zorgen dat de service correct is gebonden, selecteert u de naam van de binding en controleert u de gegevens. Het `property` veld moet vergelijkbaar zijn met dit voor beeld:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassing verbindt met een Azure Cosmos DB-Data Base. Ga verder met de volgende zelf studie als u wilt weten hoe u uw toepassing kunt binden aan een Azure-cache voor redis-cache.

> [!div class="nextstepaction"]
> [Meer informatie over het binden van een Azure-cache voor redis-cache](spring-cloud-tutorial-bind-redis.md)
