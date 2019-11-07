---
title: Azure-cache binden voor redis aan uw Azure lente-Cloud toepassing | Microsoft Docs
description: Meer informatie over hoe u Azure-cache kunt binden voor redis aan uw Azure lente-Cloud toepassing
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607579"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Zelf studie: Azure-Services binden aan uw Azure veer Cloud-toepassing: Azure cache voor redis

Met Azure lente Cloud kunt u de geselecteerde Azure-Services automatisch koppelen aan uw toepassingen, in plaats van de Spring boot-toepassing hand matig te configureren. In dit artikel wordt beschreven hoe u uw toepassing verbindt met Azure cache voor redis.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure-cache voor redis service-exemplaar
* Azure lente-Cloud extensie voor Azure CLI

Als u geen geïmplementeerd Azure veer Cloud-exemplaar hebt, volgt u de stappen in deze [Snelstartgids](spring-cloud-quickstart-launch-app-portal.md) om uw eerste lente-Cloud-app te implementeren.

## <a name="bind-azure-cache-for-redis"></a>Azure-cache binden voor redis

1. Voeg de volgende afhankelijkheden toe aan de `pom.xml` van uw project

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Verwijder `spring.redis.*` eigenschappen, indien aanwezig, in het `application.properties` bestand

1. Werk de huidige implementatie bij met `az spring-cloud app update` of maak een nieuwe implementatie met behulp van `az spring-cloud app deployment create`.

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal. Zoek het **dash board** van de toepassing en selecteer de toepassing die u wilt verbinden met de Azure-cache voor redis.  Dit is de toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd. Kies vervolgens `Service binding` en selecteer de knop `Create service binding`. Vul het formulier in en zorg ervoor dat u het **bindings type** `Azure Cache for Redis`, uw redis-server en de primaire-sleutel optie selecteert. 

1. Start de app opnieuw en deze binding zou nu moeten werken.

1. Om ervoor te zorgen dat de service binding juist is, selecteert u de naam van de binding en controleert u de gegevens. Het veld `property` moet er als volgt uitzien:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassing kunt binden aan een Azure redis-cache.  Ga verder met de zelf studie voor het binden van een toepassing aan een MySQL-data base voor meer informatie over bindings Services voor uw toepassing.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u een Azure mysql-service verbindt met uw Azure lente-Cloud service](spring-cloud-tutorial-bind-mysql.md).