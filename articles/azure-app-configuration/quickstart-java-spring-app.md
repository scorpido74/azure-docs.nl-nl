---
title: Snelstart voor meer informatie over het gebruik van Azure App Configuration
description: Een quickstart voor het gebruik van Azure App Configuration met Java Spring-apps.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: 85b966a9423fa6c8b2dc9e97cab1e24d9756caa4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687340"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Snelstart: een Java Spring-app maken met Azure-app-configuratie

In deze quickstart neemt u Azure App Configuration op in een Java Spring-app om de opslag en het beheer van toepassingsinstellingen te centraliseren, gescheiden van uw code.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- Een ondersteunde [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) met versie 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versie 3.0 of hoger.

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **+ Sleutelwaarde maken** > **Key-value** om de volgende sleutelwaardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | /application/config.message | Hello |

    Laat **label en** **inhoudstype** voorlopig leeg.

7. Selecteer **Toepassen**.

## <a name="create-a-spring-boot-app"></a>Een Spring Boot-app maken

Gebruik de [Spring Initializr](https://start.spring.io/) om een nieuw Spring Boot-project te maken.

1. Blader naar <https://start.spring.io/>.

1. Geef de volgende opties op:

   - Genereer een **Maven**-project met **Java**.
   - Geef een **springbootversie** op die gelijk is aan of groter is dan 2.0.
   - Geef de namen voor **Groep** en **Artefact** voor uw toepassing op.
   - Voeg de afhankelijkheid **van het lenteweb** toe.

1. Nadat u de vorige opties hebt opgegeven, selecteert u **Project genereren**. Wanneer u hierom wordt gevraagd, downloadt u het project naar een pad op uw lokale computer.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratiearchief

1. Nadat u de bestanden op uw lokale systeem hebt uitgepakt, is uw eenvoudige toepassing voor springboot klaar om te worden bewerkt. Zoek het bestand *pom.xml* in de hoofdmap van uw app.

1. Open het bestand *pom.xml* in een teksteditor en voeg de starter Spring Cloud Azure Config toe aan de lijst van `<dependencies>`:

    **Voorjaarswolk 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Voorjaarswolk 1.2.x**

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

1. Maak een nieuw `bootstrap.properties` bestand met de naam onder de bronnenmap van uw app en voeg de volgende regels toe aan het bestand. Vervang de voorbeeldwaarden door de juiste eigenschappen voor uw app-configuratiearchief.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string=${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Stel een omgevingsvariabele met de naam **APP_CONFIGURATION_CONNECTION_STRING**in en stel deze in op de toegangssleutel voor uw App Configuration Store. Voer bij de opdrachtregel de volgende opdracht uit en start de opdrachtprompt opnieuw om de wijziging van kracht te laten worden:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Bouw je Spring Boot applicatie met Maven en voer deze uit, bijvoorbeeld:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Nadat uw toepassing is uitgevoerd, gebruikt u *krul* om uw toepassing te testen, bijvoorbeeld:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    U ziet het bericht dat u hebt ingevoerd in de App Configuration Store.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe App Configuration Store gemaakt en gebruikt met een Java Spring-app. Zie [Lente op Azure](https://docs.microsoft.com/java/azure/spring-framework/)voor meer informatie. Ga verder naar de volgende zelfstudie voor meer informatie over het inschakelen van uw Java Spring-app om de configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-java-spring-app.md)
