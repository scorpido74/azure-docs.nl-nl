---
title: 'Zelf studie: een Azure Cosmos DB binden aan uw Azure lente-Cloud toepassing'
description: In deze zelf studie leert u hoe u Azure Cosmos DB bindt aan uw Azure lente-Cloud toepassing
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 7e796c6f8b2ae17ba267a19da1d909087163d99c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708830"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Zelf studie: een Azure Cosmos DB binden aan uw Azure lente-Cloud toepassing

Met Azure lente Cloud kunt u de geselecteerde Azure-Services automatisch koppelen aan uw toepassingen, in plaats van de Spring boot-toepassing hand matig te configureren. In dit artikel wordt beschreven hoe u uw toepassing verbindt met een Azure Cosmos DB.

Vereisten:
* Een geïmplementeerd Azure veer Cloud-instantie.  Volg onze [Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.
* Een Azure Cosmos DB account met het minimale machtigings niveau Inzender.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB binden

Azure Cosmos DB heeft vijf verschillende typen API die ondersteuning bieden voor binding:

1. Hiermee maakt u een Azure Cosmos DB-database. [Raadpleeg dit artikel](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) voor hulp bij het maken van de data base. Noteer de naam van uw data base. Ren heet `testdb`.

1. Voeg op basis van uw API-type een van de volgende afhankelijkheden toe aan de `pom.xml` van de lente-Cloud toepassing.
    
    #### <a name="api-type-core-sql"></a>API-type: core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API-type: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API-type: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API-type: Gremlin (grafiek)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API-type: Azure-tabel

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Werk de huidige implementatie bij met behulp van `az spring-cloud app update` of maak een nieuwe implementatie voor deze wijziging met behulp van `az spring-cloud app deployment create`.  Met deze opdrachten kunt u de toepassing bijwerken of maken met de nieuwe afhankelijkheid.

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal. Dit is de toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd. Zoek het **dash board** van de toepassing en selecteer de toepassing die u wilt verbinden met de Cosmos db. Selecteer vervolgens `Service binding` en selecteer de knop `Create service binding`. Vul het formulier in en selecteer **bindings type** `Azure Cosmos DB`, het API-type, de naam van uw data base en het Azure Cosmos DB-account.

    > [!NOTE]
    > Gebruik een sleutel ruimte voor de database naam als u Cassandra gebruikt.

1. Start de toepassing opnieuw door de knop **opnieuw opstarten** op de pagina toepassing te selecteren.

1. Om ervoor te zorgen dat de service correct is gebonden, selecteert u de naam van de binding en controleert u de details ervan. Het `property` veld moet er als volgt uitzien:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassing verbindt met een CosmosDB.  Ga verder met de volgende zelf studie als u wilt weten hoe u uw toepassing verbindt met een Azure Redis Cache.

> [!div class="nextstepaction"]
> [Een Azure lente-Cloud toepassing binden aan een Azure redis cache](spring-cloud-tutorial-bind-redis.md).
