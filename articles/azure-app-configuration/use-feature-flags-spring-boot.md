---
title: Zelfstudie voor het gebruik van functievlaggen in een app voor het opstarten van de lente - Azure App-configuratie | Microsoft Documenten
description: In deze zelfstudie leert u hoe u functievlaggen implementeert in apps voor springboot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: d519ac44d617f725aa9b3d3f11671122bd9477bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944315"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Zelfstudie: Functievlaggen gebruiken in een app voor springboot

De Spring Boot Core Feature Management-bibliotheken bieden ondersteuning voor het implementeren van functievlaggen in een Spring Boot-toepassing. Met deze bibliotheken u declaratief functievlaggen aan uw code toevoegen.

De functiesbeheerbibliotheken beheren ook de levenscyclus van functievlag's achter de schermen. De bibliotheken vernieuwen en cachevlagen of garanderen bijvoorbeeld dat een vlagstatus onveranderlijk is tijdens een aanvraaggesprek. Daarnaast biedt de Spring Boot-bibliotheek integraties, waaronder MVC-controlleracties, routes en middleware.

Met [de functievlaggen toevoegen aan een App Spring Boot,](./quickstart-feature-flag-spring-boot.md) toont Quickstart verschillende manieren om functievlaggen toe te voegen in een toepassing voor springlaars. Deze zelfstudie legt deze methoden in meer detail uit.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg functievlaggen toe in belangrijke delen van uw toepassing om de beschikbaarheid van functies te beheren.
> * Integreer met app-configuratie wanneer u deze gebruikt om functievlaggen te beheren.

## <a name="set-up-feature-management"></a>Functiebeheer instellen

De functiemanager `FeatureManager` Spring Boot krijgt functievlaggen van het native configuratiesysteem van het framework. Als gevolg hiervan u de functievlaggen van uw toepassing definiëren met behulp van elke configuratiebron die Spring Boot ondersteunt, inclusief het lokale *bootstrap.yml-bestand* of omgevingsvariabelen. `FeatureManager`vertrouwt op afhankelijkheidsinjectie. U de functiesbeheerservices registreren met behulp van standaardconventies:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

We raden u aan functievlaggen buiten de toepassing te houden en deze afzonderlijk te beheren. Hiermee u op elk gewenst moment vlagstatussen wijzigen en deze wijzigingen meteen in de toepassing laten toepassen. App-configuratie biedt een centrale plaats voor het ordenen en beheren van al uw functievlaggen via een speciale portal-gebruikersinterface. App Configuration levert de vlaggen ook rechtstreeks via de clientbibliotheken van Spring Boot aan uw toepassing.

De eenvoudigste manier om uw springboot-toepassing aan te sluiten op app-configuratie is via de configuratieprovider:

### <a name="spring-cloud-11x"></a>Voorjaarswolk 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Voorjaarswolk 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Verklaring functievlag

Elke functievlag bestaat uit twee delen: een naam en een lijst met een of *on* meer filters die worden `True`gebruikt om te evalueren of de status van een functie is ingeschakeld (dat wil zeggen wanneer de waarde ervan is). Een filter definieert een use case voor wanneer een functie moet worden ingeschakeld.

Wanneer een functievlag meerdere filters heeft, wordt de filterlijst in volgorde doorlopen totdat een van de filters bepaalt dat de functie moet zijn ingeschakeld. Op dat moment is de functievlag *ingeschakeld*en worden de resterende filterresultaten overgeslagen. Als er geen filter aangeeft dat de functie moet zijn ingeschakeld, is de functievlag *uitgeschakeld.*

De functiebeheerder ondersteunt *application.yml* als configuratiebron voor functievlaggen. In het volgende voorbeeld ziet u hoe u functievlaggen in een YAML-bestand instelt:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Volgens de `feature-management` conventie wordt het gedeelte van dit YML-document gebruikt voor instellingen voor functievlag. In het vorige voorbeeld worden drie functievlaggen `EnabledFor` weergegeven met hun filters die in de eigenschap zijn gedefinieerd:

* `feature-a`is *ingeschakeld.*
* `feature-b`is *uitgeschakeld.*
* `feature-c`hiermee wordt `Percentage` een `parameters` filter opgegeven met de naam een eigenschap. `Percentage`is een configureerbaar filter. Hiermee geeft u een kans van 50 `feature-c` procent op dat de vlag moet worden ingeschakeld . *on* `Percentage`

## <a name="feature-flag-checks"></a>Functievlagcontroles

Het basispatroon van functiebeheer is om eerst te controleren of een functievlag is ingesteld *op*. Als dat het zo is, voert de functiebeheerder vervolgens de acties uit die de functie bevat. Bijvoorbeeld:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Afhankelijkheidsinjectie

In Spring Boot hebt u `FeatureManager` toegang tot de functiebeheerder via afhankelijkheidsinjectie:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Acties voor controller

In MVC-controllers gebruikt `@FeatureGate` u het kenmerk om te bepalen of een specifieke actie is ingeschakeld. De `Index` volgende `feature-a` actie moet worden *ingeschakeld* voordat deze kan worden uitgevoerd:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Wanneer een MVC-controller of actie wordt geblokkeerd omdat de `IDisabledFeaturesHandler` vlag van de controlerende functie *is uitgeschakeld,* wordt een geregistreerde interface aangeroepen. De `IDisabledFeaturesHandler` standaardinterface retourneert een statuscode van 404 naar de client zonder antwoordbody.

## <a name="mvc-filters"></a>MVC-filters

U MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functievlag. Met de volgende code wordt `FeatureFlagFilter`een MVC-filter met de naam . Dit filter wordt alleen geactiveerd binnen `feature-a` de MVC-pijplijn als deze is ingeschakeld.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Routes

U functievlaggen gebruiken om routes om te leiden. De volgende code wordt `feature-a` omgeleid van een gebruiker is ingeschakeld:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u functievlaggen `spring-cloud-azure-feature-management-web` in uw toepassing Spring Boot implementeert met behulp van de bibliotheken. Zie de volgende bronnen voor meer informatie over ondersteuning voor functiebeheer in de voorstart en app-configuratie:

* [Voorbeeldcode voor springbootfunctie](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Functievlaggen beheren](./manage-feature-flags.md)
