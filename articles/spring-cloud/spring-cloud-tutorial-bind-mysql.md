---
title: Zelf studie-een Azure Database for MySQL-exemplaar koppelen aan uw Azure lente-Cloud toepassing
description: In deze zelf studie wordt uitgelegd hoe u een Azure Database for MySQL exemplaar verbindt met uw Azure lente-Cloud toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277548"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Zelf studie: een Azure Database for MySQL-exemplaar binden aan uw Azure lente-Cloud toepassing 

Met Azure lente-Cloud kunt u de selectie van Azure-Services automatisch aan uw toepassingen binden, in plaats van de veer boot-toepassing hand matig te configureren. In deze zelf studie ziet u hoe u uw toepassing kunt binden aan uw Azure Database for MySQL-exemplaar.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure veer Cloud-exemplaar
* Een Azure Database for MySQL-account
* Azure-CLI

Als u geen geïmplementeerd Azure lente-Cloud exemplaar hebt, volgt u de instructies in [Quick Start: een Azure lente-Cloud toepassing starten met behulp van de Azure Portal](spring-cloud-quickstart-launch-app-portal.md) om uw eerste lente-Cloud-app te implementeren.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Uw app binden aan uw Azure Database for MySQL-exemplaar

1. Noteer de gebruikers naam en het wacht woord van de beheerder van uw Azure Database for MySQL-account. 

1. Verbinding maken met de server, een Data Base met de naam **testdb** op een mysql-client, en vervolgens een nieuw niet-beheerders account maken.

1. Voeg in het bestand *pom. XML* van het project de volgende afhankelijkheden toe:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Verwijder alle `spring.datasource.*` eigenschappen in het bestand *Application. Properties* .

1. Werk de huidige implementatie bij door `az spring-cloud app update`uit te voeren of maak een nieuwe implementatie voor deze wijziging door `az spring-cloud app deployment create`uit te voeren.  Met deze opdrachten kunt u de toepassing bijwerken of maken met de nieuwe afhankelijkheid.

1. Zoek in de Azure Portal op de pagina **Azure lente-Cloud** service naar het **dash board**van de toepassing en selecteer vervolgens de toepassing die u wilt koppelen aan uw Azure database for MySQL exemplaar.  Dit is de toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd. 

1. Selecteer **service binding**en selecteer vervolgens de knop **service binding maken** . 

1. Vul het formulier in en selecteer **Azure mysql** als het **bindings type**met dezelfde database naam die u eerder hebt gebruikt en gebruik dezelfde gebruikers naam en hetzelfde wacht woord als in de eerste stap.

1. Start de app opnieuw en deze binding zou nu moeten werken.

1. Om ervoor te zorgen dat de service binding juist is, selecteert u de naam van de binding en verifieert u de details ervan. Het `property` veld moet er als volgt uitzien:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Azure lente-Cloud toepassing verbindt met een Azure Database for MySQL-exemplaar.  Zie het artikel over service detectie en registratie voor meer informatie over het beheren van uw Azure lente-Cloud service.

> [!div class="nextstepaction"]
> [Service detectie en registratie inschakelen met behulp van het lente-REGI ster van de Cloud service](spring-cloud-service-registration.md)
