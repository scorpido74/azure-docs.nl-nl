---
title: Azure-cache voor redis aan uw Azure lente-Cloud toepassing binden
description: Meer informatie over hoe u Azure-cache kunt binden voor redis aan uw Azure lente-Cloud toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: ddf239fc6415b3dbee6051d4199cd29e5244e4d2
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142132"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure-cache voor redis aan uw Azure lente-Cloud toepassing binden 

In plaats van uw Spring-opstart toepassingen hand matig te configureren, kunt u de geselecteerde Azure-Services automatisch binden aan uw toepassingen met behulp van Azure veer Cloud. In dit artikel wordt uitgelegd hoe u uw toepassing verbindt met Azure cache voor redis.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure-cache voor redis service-exemplaar
* De Azure veer Cloud-extensie voor de Azure CLI

Als u geen geïmplementeerd Azure lente-Cloud exemplaar hebt, volgt u de stappen in de [Snelstartgids voor het implementeren van een Azure lente-Cloud-app](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Azure-cache binden voor redis

1. Voeg de volgende afhankelijkheden toe aan het pom.xml-bestand van uw project:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `spring.redis.*`Eigenschappen uit het bestand verwijderen `application.properties`

1. Werk de huidige implementatie bij met `az spring-cloud app update` of maak een nieuwe implementatie met `az spring-cloud app deployment create` .

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

In dit artikel hebt u geleerd hoe u uw Azure lente-Cloud toepassing kunt binden aan Azure cache voor redis. Zie [verbinding maken met een Azure database for MySQL-exemplaar](spring-cloud-tutorial-bind-mysql.md)voor meer informatie over bindings Services voor uw toepassing.
