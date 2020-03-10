---
title: Quick start voor het toevoegen van functie vlaggen voor een Spring boot met Azure-app configuratie
description: Functie vlaggen toevoegen aan veer boot-apps en deze beheren met Azure-app configuratie
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/21/2020
ms.author: lcozzens
ms.openlocfilehash: 489bc0234580e8df8dcc85c1d3cc0add547818b1
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944343"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Snelstartgids: functie vlaggen toevoegen aan een Spring boot-app

In deze Snelstartgids neemt u Azure-app configuratie op in een Spring boot-web-app om een end-to-end-implementatie van functie beheer te maken. U kunt de app Configuration-service gebruiken om al uw functie vlaggen centraal op te slaan en hun status te bepalen.

De bron voor het beheer van Spring boot-onderdelen breidt het Framework uit met uitgebreide ondersteuning voor functie vlaggen. Deze bibliotheken hebben **geen** afhankelijkheid van Azure-bibliotheken. Ze kunnen naadloos worden geïntegreerd met de configuratie van apps via de configuratie provider voor veer boot.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Een ondersteunde [SDK voor Java Development Kit](https://docs.microsoft.com/java/azure/jdk) met versie 8.
* [Apache Maven](https://maven.apache.org/download.cgi) -versie 3,0 of hoger.

## <a name="create-an-app-configuration-instance"></a>Een app-configuratie-exemplaar maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **functie beheer** >  **+ toevoegen** om een functie vlag met de naam `Beta`toe te voegen.

    > [!div class="mx-imgBorder"]
    > functie vlag ![met de naam bèta](media/add-beta-feature-flag.png) inschakelen

    Houd `label` nu niet gedefinieerd.

## <a name="create-a-spring-boot-app"></a>Een Spring Boot-app maken

Gebruik de [lente initialisatie functie](https://start.spring.io/) om een nieuw Spring boot-project te maken.

1. Blader naar <https://start.spring.io/>.

1. Geef de volgende opties op:

   * Genereer een **Maven**-project met **Java**.
   * Geef een **Spring boot** -versie op die gelijk is aan of groter is dan 2,0.
   * Geef de namen voor **Groep** en **Artefact** voor uw toepassing op.  In dit artikel wordt gebruikgemaakt van `com.example` en `demo`.
   * Voeg de **lente** webafhankelijkheid toe.

1. Nadat u de vorige opties hebt opgegeven, selecteert u **project genereren**. Als u hierom wordt gevraagd, moet u het project downloaden naar uw lokale computer.

## <a name="add-feature-management"></a>Functie beheer toevoegen

1. Nadat u de bestanden op het lokale systeem hebt uitgepakt, kunt u de Spring boot-toepassing bewerken. Zoek *pom. XML* in de hoofdmap van uw app.

1. Open het bestand *pom. XML* in een tekst editor en voeg het volgende toe aan de lijst met `<dependencies>`.:

### <a name="spring-cloud-11x"></a>Lente Cloud 1.1. x

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

### <a name="spring-cloud-12x"></a>Lente Cloud 1.2. x

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Er is een beheer bibliotheek zonder webonderdelen die geen afhankelijkheid van een web heeft. Raadpleeg de [documentatie](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) van github voor verschillen.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratie archief

1. Ga naar de map `resources` van uw app en open `bootstrap.properties`.  Als het bestand niet bestaat, maakt u het. Voeg de volgende regel toe aan het bestand.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Selecteer in de app-configuratie portal voor uw configuratie archief `Access keys` in de zijbalk. Selecteer het tabblad alleen-lezen sleutels. Kopieer de waarde van de primaire connection string.

1. Voeg de primaire connection string als een omgevings variabele toe met behulp van de naam van de variabele `APP_CONFIGURATION_CONNECTION_STRING`.

1. Open het Java-bestand van de hoofdtoepassing en voeg `@EnableConfigurationProperties` toe om deze functie in te schakelen.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Maak een nieuw Java-bestand met de naam *MessageProperties.java* in de pakketmap van uw app.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. Maak een nieuw Java-bestand met de naam *HelloController.java* in de pakketmap van uw app.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("Beta").block());
            return "welcome";
        }
    }
    ```

1. Maak een nieuw HTML-bestand met de naam *Welcome. html* in de map Sjablonen van uw app.

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Maak een nieuwe map met de naam CSS onder `static` en in een nieuw CSS-bestand met de naam *Main. CSS*.

    ```css
    html {
     position: relative;
     min-height: 100%;
    }
    body {
     margin-bottom: 60px;
    }
    .footer {
     position: absolute;
     bottom: 0;
     width: 100%;
     height: 60px;
     line-height: 60px;
     background-color: #f5f5f5;
    }

    body > .container {
     padding: 60px 15px 0;
    }

    .footer > .container {
     padding-right: 15px;
     padding-left: 15px;
    }

    code {
     font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal bouwen en uitvoeren

1. Maak uw Spring Boot-app met Maven en voer deze uit.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Open een browser venster en ga naar de standaard-URL voor een lokaal gehoste web-app: `https://localhost:8080`.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. Selecteer in de portal voor app-configuratie **functie beheer**en wijzig de status van de **bèta** sleutel in **op**:

    | Sleutel | Status |
    |---|---|
    | Bèta | Aan |

1. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een nieuwe app-configuratie opgeslagen gemaakt en gebruikt om de functies in een Spring boot-web-app te beheren via de [beheer bibliotheken voor onderdelen](https://go.microsoft.com/fwlink/?linkid=2074664).

* Meer informatie over [functie beheer](./concept-feature-management.md).
* [Functie vlaggen beheren](./manage-feature-flags.md).
* [Gebruik functie vlaggen in een lente boot core-app](./use-feature-flags-spring-boot.md).
