---
title: Azure Database for MySQL binden aan uw Azure lente-Cloud toepassing | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Azure MySQL verbindt met uw Azure lente-Cloud toepassing
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607594"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Zelf studie: Azure-Services binden aan uw Azure veer Cloud-toepassing: Azure Database for MySQL

Met Azure lente Cloud kunt u de geselecteerde Azure-Services automatisch koppelen aan uw toepassingen, in plaats van de Spring boot-toepassing hand matig te configureren. In deze zelf studie wordt uitgelegd hoe u uw toepassing verbindt met Azure MySQL.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure Database for MySQL-account
* Azure CLI

Als u geen geïmplementeerd Azure veer Cloud-exemplaar hebt, volgt u de stappen in deze [Snelstartgids](spring-cloud-quickstart-launch-app-portal.md) om uw eerste lente-Cloud-app te implementeren.

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

1. Ga naar de pagina Azure lente-Cloud service in de Azure Portal. Zoek het **dash board** van de toepassing en selecteer de toepassing die u wilt verbinden met Azure mysql.  Dit is de toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd. Selecteer vervolgens `Service binding` en selecteer de knop `Create service binding`. Vul het formulier in en zorg ervoor dat het selectie vakje **binding type** `Azure MySQL`, dezelfde database naam die u eerder hebt gebruikt en dezelfde gebruikers naam en hetzelfde wacht woord die u in de eerste stap hebt genoteerd.

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

