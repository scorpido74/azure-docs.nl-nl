---
title: Azure-cache binden voor redis aan uw Azure lente-Cloud toepassing | Microsoft Docs
description: Meer informatie over hoe u Azure-cache kunt binden voor redis aan uw Azure lente-Cloud toepassing
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038942"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Zelfstudie: Azure-Services binden aan uw Azure lente-Cloud toepassing: Azure Cache voor Redis

Met Azure lente Cloud kunt u de geselecteerde Azure-Services automatisch koppelen aan uw toepassingen, in plaats van de Spring boot-toepassing hand matig te configureren. In dit artikel wordt beschreven hoe u uw toepassing verbindt met Azure cache voor redis.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure-cache voor redis service-exemplaar
* Azure lente-Cloud extensie voor Azure CLI

Als dat nodig is, installeert u de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

## <a name="bind-azure-cache-for-redis"></a>Azure-cache binden voor redis

1. Voeg de volgende afhankelijkheden toe aan de `pom.xml` van uw project

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. @No__t-0-eigenschappen, indien aanwezig, in het `application.properties`-bestand verwijderen

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