---
title: Quick start voor het toevoegen van functie vlaggen aan de lente-Azure-app configuratie | Microsoft Docs
description: Een Snelstartgids voor het toevoegen van functie vlaggen voor veer boot-apps en het beheren van deze in Azure-app configuratie
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Spring Boot
ms.workload: tbd
ms.date: 09/26/2019
ms.author: mametcal
ms.openlocfilehash: fe943913f9d33923438efb91630c5a85c4d1b748
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687183"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Quickstart: Functie vlaggen toevoegen aan een Spring boot-app

In deze Snelstartgids neemt u Azure-app configuratie op in een Spring boot-web-app om een end-to-end-implementatie van functie beheer te maken. U kunt de app Configuration-service gebruiken om al uw functie vlaggen centraal op te slaan en hun status te bepalen.

De bron voor het beheer van Spring boot-onderdelen breidt het Framework uit met uitgebreide ondersteuning voor functie vlaggen. Deze bibliotheken hebben **geen** afhankelijkheid van Azure libries. Ze kunnen naadloos worden geïntegreerd met de configuratie van apps via de configuratie provider voor veer boot.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- Een ondersteunde [SDK voor Java Development Kit](https://docs.microsoft.com/java/azure/jdk) met versie 8.
- [Apache Maven](https://maven.apache.org/download.cgi) -versie 3,0 of hoger.

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **feature Manager** >  **+ maken** om de volgende functie vlaggen toe te voegen:

    | Sleutel | State |
    |---|---|
    | Bèta | Uit |

## <a name="create-a-spring-boot-app"></a>Een Spring Boot-app maken

U gebruikt de [lente initialisatie functie](https://start.spring.io/) om een nieuw Spring boot-project te maken.

1. Blader naar <https://start.spring.io/>.

2. Geef de volgende opties op:

   - Genereer een **Maven**-project met **Java**.
   - Geef een **Spring boot** -versie op die gelijk is aan of groter is dan 2,0.
   - Geef de namen voor **Groep** en **Artefact** voor uw toepassing op.
   - Voeg de afhankelijkheid **Web** toe.

3. Nadat u de vorige opties hebt opgegeven, selecteert u **project genereren**. Wanneer u hierom wordt gevraagd, downloadt u het project naar een pad op uw lokale computer.

## <a name="add-feature-management"></a>Functie beheer toevoegen

1. Nadat u de bestanden op het lokale systeem hebt uitgepakt, kunt u de toepassing voor het uitvoeren van een eenvoudige Spring boot bewerken. Zoek het bestand *pom.xml* in de hoofdmap van uw app.

2. Open het bestand *pom. XML* in een tekst editor en voeg de lente Cloud Azure config starter en het onderdeel beheer toe aan de lijst met `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Er is een beheer bibliotheek zonder webonderdelen die geen afhankelijkheid van een web heeft. Bekijk de aanvullende [documenten](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) voor verschillen. Wanneer u app-configuratie niet gebruikt, raadpleegt u ook de [declaratie van functie vlaggen](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management#feature-flag-declaration).

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratie archief

1. Open `bootstrap.properties`, dat zich in de map resources van uw app bevindt, en voeg de volgende regels toe aan het bestand. Voeg de informatie over de app-configuratie toe.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

2. Ga in de app-configuratie portal voor uw configuratie archief naar toegangs sleutels. Selecteer het tabblad alleen-lezen sleutels. Kopieer op dit tabblad de waarde van een van de verbindings reeksen en voeg deze toe als een nieuwe omgevings variabele met variabele naam `APP_CONFIGURATION_CONNECTION_STRING`.

3. Open het Java-bestand van de hoofdtoepassing en voeg `@EnableConfigurationProperties` toe om deze functie in te schakelen.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

4. Maak een nieuw Java-bestand met de naam *HelloController.java* in de pakketmap van uw app. Voeg de volgende regels toe:

    ```java
    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabled("Beta"));
            return "welcome";
        }
    }
    ```

5. Maak een nieuw HTML-bestand met de naam *Welcome. html* in de map Sjablonen van uw app. Voeg de volgende regels toe:

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

6. Maak een nieuwe map met de naam CSS onder statisch en daarin een nieuw CSS-bestand met de naam *Main. CSS*. Voeg de volgende regels toe:

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

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Maak een Spring boot-toepassing met maven en voer deze uit, bijvoorbeeld:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

2. Open een browser venster en ga naar `https://localhost:8080`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/spring-boot-feature-flag-local-before.png)

3. Selecteer in de portal voor app-configuratie **functie beheer**en wijzig de status van de **bèta** sleutel in **op**:

    | Sleutel | State |
    |---|---|
    | Bèta | Aan |

4. Vernieuw de browserpagina om de nieuwe configuratie-instellingen te zien.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een nieuwe app-configuratie opgeslagen gemaakt en gebruikt om de functies in een Spring boot-web-app te beheren via de [beheer bibliotheken voor onderdelen](https://go.microsoft.com/fwlink/?linkid=2074664).

- Meer informatie over [functie beheer](./concept-feature-management.md).
- [Functie vlaggen beheren](./manage-feature-flags.md).
- [Gebruik functie vlaggen in een lente boot core-app](./use-feature-flags-spring-boot.md).
