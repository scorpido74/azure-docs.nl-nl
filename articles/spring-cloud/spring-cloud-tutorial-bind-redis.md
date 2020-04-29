---
title: 'Zelf studie: Azure-cache voor redis aan uw Azure lente-Cloud toepassing binden'
description: Deze zelf studie laat zien hoe u Azure-cache kunt binden voor redis aan uw Azure lente-Cloud toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277508"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure-cache voor redis aan uw Azure lente-Cloud toepassing binden 

In plaats van uw Spring-opstart toepassingen hand matig te configureren, kunt u de geselecteerde Azure-Services automatisch binden aan uw toepassingen met behulp van Azure veer Cloud. In dit artikel wordt uitgelegd hoe u uw toepassing verbindt met Azure cache voor redis.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure-cache voor redis service-exemplaar
* De Azure veer Cloud-extensie voor de Azure CLI

Als u geen geïmplementeerd Azure lente-Cloud exemplaar hebt, volgt u de stappen in de [Snelstartgids voor het implementeren van een Azure lente-Cloud-app](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Azure-cache binden voor redis

1. Voeg de volgende afhankelijkheden toe aan het bestand pom. XML van uw project:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `spring.redis.*` Eigenschappen uit het `application.properties` bestand verwijderen

1. Werk de huidige implementatie bij `az spring-cloud app update` met of maak een nieuwe implementatie `az spring-cloud app deployment create`met.

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal. Ga naar het **dash board** van de toepassing en selecteer de toepassing die u wilt verbinden met Azure cache voor redis. Deze toepassing is hetzelfde als die u in de vorige stap hebt bijgewerkt of geïmplementeerd.

1. Selecteer **service binding** en selecteer **service binding maken**. Vul het formulier in en zorg ervoor dat u de waarde voor het **bindings type** **Azure cache selecteert voor redis**, uw Azure cache voor redis-server en de **primaire** -sleutel optie.

1. De app opnieuw starten. De binding moet nu worden uitgevoerd.

1. Om ervoor te zorgen dat de service binding juist is, selecteert u de naam van de binding en controleert u de gegevens. Het `property` veld moet er als volgt uitzien:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassing koppelt aan Azure cache voor redis. Als u meer wilt weten over bindings Services voor uw toepassing, gaat u verder met de zelf studie over het binden van een toepassing aan een Azure Database for MySQL-exemplaar.

> [!div class="nextstepaction"]
> [Meer informatie over het maken van een binding met een Azure Database for MySQL-exemplaar](spring-cloud-tutorial-bind-mysql.md)
