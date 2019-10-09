---
title: Azure Database for MySQL binden aan uw Azure lente-Cloud toepassing | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Azure MySQL verbindt met uw Azure lente-Cloud toepassing
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039019"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Zelfstudie: Azure-Services binden aan uw Azure lente-Cloud toepassing: Azure Database for MySQL

Met Azure lente Cloud kunt u de geselecteerde Azure-Services automatisch koppelen aan uw toepassingen, in plaats van de Spring boot-toepassing hand matig te configureren. In deze zelf studie wordt uitgelegd hoe u uw toepassing verbindt met Azure MySQL.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure Database for MySQL-account
* Azure-CLI

Als dat nodig is, installeert u de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

## <a name="bind-azure-database-for-mysql"></a>Azure Database for MySQL binden

1. Noteer de gebruikers naam en het wacht woord voor de beheerder van uw Azure MySQL-account. Verbinding maken met de server en een Data Base met de naam `testdb` maken op basis van een MySQL-client. Maak een nieuw niet-beheerders account.

1. Voeg de volgende afhankelijkheden toe aan de `pom.xml` van uw project

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Verwijder `spring.datasource.*` eigenschappen, indien aanwezig, in het bestand `application.properties`.

1. Werk de huidige implementatie bij met `az spring-cloud app update` of maak een nieuwe implementatie voor deze wijziging met behulp van `az spring-cloud app deployment create`.  Met deze opdrachten kunt u de toepassing bijwerken of maken met de nieuwe afhankelijkheid.

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal. Zoek het **dash board** van de toepassing en selecteer de toepassing die u wilt verbinden met Azure mysql.  Dit is de toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd. Selecteer vervolgens `Service binding` en selecteer de knop `Create service binding`. Vul het formulier in en zorg ervoor dat het selectie vakje **bindings type** `Azure MySQL`, dezelfde database naam die u eerder hebt gebruikt en dezelfde gebruikers naam en hetzelfde wacht woord die u in de eerste stap hebt genoteerd.

1. Start de app opnieuw en deze binding zou nu moeten werken.

1. Om ervoor te zorgen dat de service binding juist is, selecteert u de naam van de binding en controleert u de details ervan. Het veld `property` moet er als volgt uitzien:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassing verbindt met een MySQL-data base.  Lees voor meer informatie over het beheren van uw Azure lente-Cloud service voor meer informatie over service detectie en registratie.

> [!div class="nextstepaction"]
> [Meer informatie over het inschakelen van service detectie en registraties met behulp van het lente-REGI ster van de Cloud service](spring-cloud-service-registration.md).

