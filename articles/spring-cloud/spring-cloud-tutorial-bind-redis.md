---
title: Zelfstudie - Azure Cache voor Redis binden aan uw Azure Spring Cloud-toepassing
description: In deze zelfstudie ziet u hoe u Azure Cache voor Redis binden aan uw Azure Spring Cloud-toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277508"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure-cache voor Redis binden aan uw Azure Spring Cloud-toepassing 

In plaats van uw Spring Boot-toepassingen handmatig te configureren, u bepaalde Azure-services automatisch aan uw toepassingen binden met Azure Spring Cloud. In dit artikel ziet u hoe u uw toepassing binden aan Azure Cache voor Redis.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerdAzure Spring Cloud-exemplaar
* Een Azure-cache voor redis-serviceinstantie
* De Azure Spring Cloud-extensie voor de Azure CLI

Als u geen geïmplementeerdazure springcloud-exemplaar hebt, volgt u de stappen in de [quickstart bij het implementeren van een Azure Spring Cloud-app.](spring-cloud-quickstart-launch-app-portal.md)

## <a name="bind-azure-cache-for-redis"></a>Azure-cache voor Redis binden

1. Voeg de volgende afhankelijkheid toe aan het pom.xml-bestand van uw project:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Alle `spring.redis.*` eigenschappen uit `application.properties` het bestand verwijderen

1. Werk de huidige `az spring-cloud app update` implementatie bij met `az spring-cloud app deployment create`behulp van of maak een nieuwe implementatie met behulp van .

1. Ga naar uw Azure Spring Cloud-servicepagina in de Azure-portal. Ga naar **Toepassingsdashboard** en selecteer de toepassing die u wilt binden aan Azure Cache voor Redis. Deze toepassing is dezelfde toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd.

1. Selecteer **Servicebinding** en selecteer **Servicebinding maken**. Vul het formulier in en selecteer de **Azure-waarde binding** **voor Redis,** uw Azure-cache voor Redis-server en de optie **Primaire** sleutel.

1. De app opnieuw starten. De binding zou nu moeten werken.

1. Als u wilt weten of de servicebinding correct is, selecteert u de bindende naam en controleert u de details ervan. Het `property` veld moet er als volgt uitzien:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw Azure Spring Cloud-toepassing binden aan Azure Cache voor Redis. Ga voor meer informatie over het binden van services aan uw toepassing verder naar de zelfstudie over het binden van een toepassing aan een Azure Database voor MySQL-instantie.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u zich binden aan een Azure-database voor MySQL-instantie](spring-cloud-tutorial-bind-mysql.md)
