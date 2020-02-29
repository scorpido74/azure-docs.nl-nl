---
title: Quick start voor informatie over het gebruik van Azure-app configuratie
description: Een quickstart voor het gebruik van Azure App Configuration met Java Spring-apps.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 2521adfda731c06c879f5cfeb6283567228bf664
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919359"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Snelstartgids: een Java-lente-app maken met Azure-app configuratie

In deze Snelstartgids neemt u Azure-app configuratie op in een Java lente-app om opslag en beheer van toepassings instellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- Een ondersteund [jdk (Java Development Kit)](https://docs.microsoft.com/java/azure/jdk) met versie 8.
- [Apache Maven](https://maven.apache.org/download.cgi) -versie 3,0 of hoger.

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

1. Selecteer **configuratie verkenner** >  **+ maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | /application/config.message | Hello |

    Laat het **Label** en het **inhouds type** nu leeg.

## <a name="create-a-spring-boot-app"></a>Een Spring Boot-app maken

Gebruik de [lente initialisatie functie](https://start.spring.io/) om een nieuw Spring boot-project te maken.

1. Blader naar <https://start.spring.io/>.

1. Geef de volgende opties op:

   - Genereer een **Maven**-project met **Java**.
   - Geef een **Spring boot** -versie op die gelijk is aan of groter is dan 2,0.
   - Geef de namen voor **Groep** en **Artefact** voor uw toepassing op.
   - Voeg de **lente** webafhankelijkheid toe.

1. Nadat u de vorige opties hebt opgegeven, selecteert u **project genereren**. Wanneer u hierom wordt gevraagd, downloadt u het project naar een pad op uw lokale computer.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratie archief

1. Nadat u de bestanden op het lokale systeem hebt uitgepakt, kunt u de toepassing voor het uitvoeren van een eenvoudige Spring boot bewerken. Zoek het bestand *pom.xml* in de hoofdmap van uw app.

1. Open het bestand *pom.xml* in een teksteditor en voeg de starter Spring Cloud Azure Config toe aan de lijst van `<dependencies>`:

    **Lente Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Lente Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Maak een nieuw Java-bestand met de naam *MessageProperties.java* in de pakketmap van uw app. Voeg de volgende regels toe:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Maak een nieuw Java-bestand met de naam *HelloController.java* in de pakketmap van uw app. Voeg de volgende regels toe:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Open het Java-bestand van de hoofdtoepassing en voeg `@EnableConfigurationProperties` toe om deze functie in te schakelen.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Maak een nieuw bestand met de naam `bootstrap.properties` in de map resources van uw app en voeg de volgende regels toe aan het bestand. Vervang de voorbeeld waarden door de juiste eigenschappen voor de app-configuratie opslag.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Stel een omgevings variabele met de naam **APP_CONFIGURATION_CONNECTION_STRING**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Voer op de opdracht regel de volgende opdracht uit en start de opdracht prompt om de wijziging toe te passen:

    ```CLI
        setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
        $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```console
        export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal bouwen en uitvoeren

1. Maak een Spring boot-toepassing met maven en voer deze uit, bijvoorbeeld:

    ```CLI
    mvn clean package
    mvn spring-boot:run
    ```

2. Nadat uw toepassing is uitgevoerd, gebruikt u *krul* om uw toepassing te testen, bijvoorbeeld:

      ```CLI
      curl -X GET http://localhost:8080/
      ```

    U ziet het bericht dat u hebt ingevoerd in de app-configuratie opslag.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een nieuwe app-configuratie opgeslagen gemaakt en gebruikt in een Java lente-app. Zie [lente op Azure](https://docs.microsoft.com/java/azure/spring-framework/)voor meer informatie. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
