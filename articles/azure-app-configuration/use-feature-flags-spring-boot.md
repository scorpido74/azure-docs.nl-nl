---
title: Zelf studie voor het gebruik van functie vlaggen in een Spring boot-app-Azure-app configuratie | Microsoft Docs
description: In deze zelf studie leert u hoe u functie vlaggen kunt implementeren in veer boot-apps.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: d924975d852320fcddd5ae988f1d52f10d366f81
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790742"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Zelf studie: functie vlaggen gebruiken in een Spring boot-app

De bron voor het opstarten van de configuratie van de lente-boot functie biedt ondersteuning voor het implementeren van functie vlaggen in een Spring boot-toepassing. Deze bibliotheken bieden u de mogelijkheid om functie vlaggen declaratief toe te voegen aan uw code.

De functie beheer bibliotheken beheren ook de levens cyclus van functie vlaggen achter de schermen. Bijvoorbeeld: de bibliotheken vernieuwen en cache vlag statussen of garanderen dat een vlag status onveranderbaar is tijdens het aanroepen van een aanvraag. Daarnaast biedt de Spring boot-bibliotheek integraties, waaronder MVC-controller acties, routes en middleware.

De [functie vlaggen toevoegen aan een Spring boot-app Snelstartgids](./quickstart-feature-flag-spring-boot.md) toont verschillende manieren om functie vlaggen toe te voegen in een Spring boot-toepassing. In deze zelf studie worden deze methoden uitgebreid beschreven.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg functie vlaggen toe aan de belangrijkste onderdelen van uw toepassing om de beschik baarheid van functies te bepalen.
> * Integreer met app-configuratie wanneer u deze gebruikt voor het beheren van functie vlaggen.

## <a name="set-up-feature-management"></a>Functie beheer instellen

De Spring boot-functie `FeatureManager` beheerder haalt functie vlaggen op uit het systeem eigen configuratiesysteem van het Framework. Als gevolg hiervan kunt u de functie vlaggen van uw toepassing definiëren met behulp van een configuratie bron die Spring boot ondersteunt, met inbegrip van het lokale *Boots trap. yml* -bestand of omgevings variabelen. `FeatureManager`maakt gebruik van afhankelijkheids injectie. U kunt de functie Beheer Services registreren met behulp van standaard conventies:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

U wordt aangeraden de functie vlaggen buiten de toepassing te blijven en deze afzonderlijk te beheren. Als u dit doet, kunt u de status van de vlaggen op elk gewenst moment wijzigen. deze wijzigingen worden direct doorgevoerd in de toepassing. App-configuratie biedt een centrale locatie voor het organiseren en beheren van al uw functie vlaggen via een speciale portal-gebruikers interface. App-configuratie levert ook rechtstreeks de vlaggen aan uw toepassing via de client bibliotheken voor Spring boot.

De eenvoudigste manier om uw Spring boot-toepassing te verbinden met app-configuratie is via de configuratie provider:

### <a name="spring-cloud-11x"></a>Lente Cloud 1.1. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Lente Cloud 1.2. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Declaratie van functie vlaggen

Elke functie vlag bestaat uit twee delen: een naam en een lijst met een of meer filters die worden gebruikt om te evalueren of de status van een functie is *ingeschakeld* (dat wil zeggen, `True`wanneer de waarde ervan is). Een filter definieert een use-case voor wanneer een functie moet worden ingeschakeld.

Wanneer een functie vlag meerdere filters heeft, wordt de filter lijst in de juiste volg orde gepasseerd totdat een van de filters bepaalt dat de functie moet worden ingeschakeld. Op dat moment is de functie vlag *ingeschakeld*en worden alle resterende filter resultaten overgeslagen. Als geen filter aangeeft dat de functie moet worden ingeschakeld, is de functie vlag *uitgeschakeld*.

Feature Manager ondersteunt *Application. yml* als een configuratie bron voor functie vlaggen. In het volgende voor beeld ziet u hoe u functie vlaggen kunt instellen in een YAML-bestand:

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

De `feature-management` sectie van dit yml-document wordt per conventie gebruikt voor instellingen voor functie vlaggen. Het vorige voor beeld toont drie functie vlaggen waarvoor de filters zijn gedefinieerd `EnabledFor` in de eigenschap:

* `feature-a`is *ingeschakeld*.
* `feature-b`is *uit*.
* `feature-c`Hiermee geeft u een `Percentage` filter op `parameters` met de naam met een eigenschap. `Percentage`is een configureerbaar filter. In dit voor beeld `Percentage` geeft u een kans van 50 procent `feature-c` *op*voor de vlag.

## <a name="feature-flag-checks"></a>Controles van functie vlaggen

Het basis patroon van functie beheer is om eerst te controleren of een functie vlag is ingesteld op *aan*. Als dit het geval is, voert het onderdeel beheer de acties uit die de functie bevat. Bijvoorbeeld:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Afhankelijkheidsinjectie

In Spring boot kunt u de functie beheer `FeatureManager` openen via afhankelijkheids injectie:

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

## <a name="controller-actions"></a>Controller acties

In MVC-controllers gebruikt u het `@FeatureGate` kenmerk om te bepalen of een bepaalde actie is ingeschakeld. De volgende `Index` actie moet `feature-a` worden *ingeschakeld* voordat deze kan worden uitgevoerd:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Wanneer een MVC-controller of-actie wordt geblokkeerd omdat de vlag voor *off*het bepalen van de `IDisabledFeaturesHandler` functie is uitgeschakeld, wordt een geregistreerde interface aangeroepen. De standaard `IDisabledFeaturesHandler` interface retourneert een 404-status code naar de client zonder antwoord tekst.

## <a name="mvc-filters"></a>MVC-filters

U kunt MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functie vlag. Met de volgende code wordt een MVC- `FeatureFlagFilter`filter toegevoegd met de naam. Dit filter wordt alleen in de MVC-pijp lijn geactiveerd als `feature-a` dit is ingeschakeld.

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

U kunt functie vlaggen gebruiken om routes om te leiden. Met de volgende code wordt een gebruiker omgeleid vanuit `feature-a` ingeschakeld:

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

In deze zelf studie hebt u geleerd hoe u functie vlaggen kunt implementeren in een Spring boot-toepassing `spring-cloud-azure-feature-management-web` met behulp van de bibliotheken. Raadpleeg de volgende bronnen voor meer informatie over de ondersteuning van functie beheer in Spring boot en app-configuratie:

* [Voorbeeld code voor de Spring boot-functie vlag](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Functievlaggen beheren](./manage-feature-flags.md)
