---
title: Zelfstudie - Een Azure-database voor MySQL-instantie binden aan uw Azure Spring Cloud-toepassing
description: In deze zelfstudie ziet u hoe u een Azure Database voor MySQL-instantie binden aan uw Azure Spring Cloud-toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277548"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Zelfstudie: Een Azure-database voor MySQL-instantie binden aan uw Azure Spring Cloud-toepassing 

Met Azure Spring Cloud u bepaalde Azure-services automatisch aan uw toepassingen binden, in plaats van uw Spring Boot-toepassing handmatig te moeten configureren. In deze zelfstudie ziet u hoe u uw toepassing binden aan uw Azure-database voor MySQL-instantie.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerdAzure Spring Cloud-exemplaar
* Een Azure-database voor MySQL-account
* Azure-CLI

Als u geen geïmplementeerdazure springcloud-exemplaar hebt, volgt u de instructies in [Quickstart: Start een Azure Spring Cloud-toepassing met behulp van de Azure-portal](spring-cloud-quickstart-launch-app-portal.md) om uw eerste Spring Cloud-app te implementeren.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Uw app binden aan uw Azure-database voor MySQL-exemplaar

1. Let op de gebruikersnaam en het wachtwoord van uw Azure Database voor MySQL-account. 

1. Maak verbinding met de server, maak een database met de naam **testdb** van een MySQL-client en maak vervolgens een nieuw niet-beheerdersaccount.

1. Voeg in het *pom.xml-bestand* van uw project de volgende afhankelijkheid toe:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Verwijder alle eigenschappen in `spring.datasource.*` het bestand *application.properties.*

1. Werk de huidige `az spring-cloud app update`implementatie bij door deze wijziging uit `az spring-cloud app deployment create`te voeren of maak een nieuwe implementatie voor deze wijziging door .  Deze opdrachten worden bijgewerkt of maken de toepassing met de nieuwe afhankelijkheid.

1. Zoek in de Azure Portal op uw **Azure Spring Cloud-servicepagina** naar het **toepassingsdashboard**en selecteer vervolgens de toepassing die u wilt binden aan uw Azure-database voor MySQL-instantie.  Dit is dezelfde toepassing die u in de vorige stap hebt bijgewerkt of geïmplementeerd. 

1. Selecteer **Servicebinding**en selecteer vervolgens de knop **Servicebinding maken.** 

1. Vul het formulier in, selecteer **Azure MySQL** als **het type Binding,** met dezelfde databasenaam die u eerder hebt gebruikt en met dezelfde gebruikersnaam en hetzelfde wachtwoord dat u in de eerste stap hebt opgemerkt.

1. Start de app opnieuw op en deze binding moet nu werken.

1. Als u wilt controleren of de servicebinding correct is, selecteert u de bindingsnaam en controleert u de details ervan. Het `property` veld moet er als volgt uitzien:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw Azure Spring Cloud-toepassing binden aan een Azure-database voor MySQL-instantie.  Zie het artikel over servicedetectie en -registratie voor meer informatie over het beheren van uw Azure Spring Cloud-service.

> [!div class="nextstepaction"]
> [Servicedetectie en -registratie inschakelen met behulp van het Spring Cloud Service Register](spring-cloud-service-registration.md)
