---
title: Zelf studie voor het gebruik van functie vlaggen in een Spring boot-app-Azure-app configuratie | Microsoft Docs
description: In deze zelf studie leert u hoe u functie vlaggen kunt implementeren in veer boot-apps.
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
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687169"
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

De bron voor de configuratie van de Spring boot-functie `FeatureManager` de functie vlaggen van het systeem eigen systeem van het Framework ophalen. Als gevolg hiervan kunt u de functie vlaggen van uw toepassing definiëren met behulp van een configuratie bron die Spring boot ondersteunt, met inbegrip van het lokale *Boots trap. yml* -bestand of omgevings variabelen. `FeatureManager` is afhankelijk van de injectie van een afhankelijkheids plaats. U kunt de functie Beheer Services registreren met behulp van standaard conventies:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

U wordt aangeraden de functie vlaggen buiten de toepassing te blijven en deze afzonderlijk te beheren. Als u dit doet, kunt u de status van de vlaggen op elk gewenst moment wijzigen. deze wijzigingen worden direct doorgevoerd in de toepassing. App-configuratie biedt een centrale locatie voor het organiseren en beheren van al uw functie vlaggen via een speciale portal-gebruikers interface. App-configuratie levert ook rechtstreeks de vlaggen aan uw toepassing via de client bibliotheken voor Spring boot.

De eenvoudigste manier om uw Spring boot-toepassing te verbinden met app-configuratie is via de configuratie provider:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Declaratie van functie vlaggen

Elke functie vlag bestaat uit twee delen: een naam en een lijst met een of meer filters die worden gebruikt om te evalueren of de status van een functie is *ingeschakeld* (dat wil zeggen, wanneer de waarde ervan is `True`). Een filter definieert een use-case voor wanneer een functie moet worden ingeschakeld.

Wanneer een functie vlag meerdere filters heeft, wordt de filter lijst in de juiste volg orde gepasseerd totdat een van de filters bepaalt dat de functie moet worden ingeschakeld. Op dat moment is de functie vlag *ingeschakeld*en worden alle resterende filter resultaten overgeslagen. Als geen filter aangeeft dat de functie moet worden ingeschakeld, is de functie vlag *uitgeschakeld*.

Feature Manager ondersteunt *Application. yml* als een configuratie bron voor functie vlaggen. In het volgende voor beeld ziet u hoe u functie vlaggen kunt instellen in een YAML-bestand:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Per Conventie wordt het gedeelte `feature-management` van dit YML-document gebruikt voor instellingen voor functie vlaggen. Het vorige voor beeld toont drie functie vlaggen waarvoor de filters zijn gedefinieerd in de eigenschap `EnabledFor`:

* `FeatureA` is *aan*.
* `FeatureB` is *uitgeschakeld*.
* `FeatureC` Hiermee geeft u een filter op met de naam `Percentage` met een `Parameters` eigenschap. `Percentage` is een configureerbaar filter. In dit voor beeld geeft `Percentage` een kans van 50 procent *op*voor de `FeatureC`-vlag.

## <a name="feature-flag-checks"></a>Controles van functie vlaggen

Het basis patroon van functie beheer is om eerst te controleren of een functie vlag is ingesteld op *aan*. Als dit het geval is, voert het onderdeel beheer de acties uit die de functie bevat. Bijvoorbeeld:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Afhankelijkheidsinjectie

In Spring boot krijgt u toegang tot `FeatureManager` functie beheer via afhankelijkheids injectie:

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

In MVC-controllers gebruikt u het kenmerk `@FeatureGate` om te bepalen of een bepaalde actie is ingeschakeld. Voor de volgende `Index` actie moet `FeatureA` zijn *ingeschakeld* voordat deze kan worden uitgevoerd:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Wanneer een MVC-controller of-actie wordt geblokkeerd omdat de vlag voor het bepalen van de functie is *uitgeschakeld*, wordt een geregistreerde `IDisabledFeaturesHandler`-interface aangeroepen. De standaard interface van `IDisabledFeaturesHandler` retourneert een 404-status code naar de client zonder antwoord tekst.

## <a name="mvc-filters"></a>MVC-filters

U kunt MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functie vlag. Met de volgende code wordt een MVC-filter toegevoegd met de naam `FeatureFlagFilter`. Dit filter wordt alleen in de MVC-pijp lijn geactiveerd als `FeatureA` is ingeschakeld.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Routes

U kunt functie vlaggen gebruiken om routes om te leiden. Met de volgende code wordt een gebruiker omgeleid van `FeatureA` is ingeschakeld:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u functie vlaggen kunt implementeren in een Spring boot-toepassing met behulp van de `spring-cloud-azure-feature-management-web` bibliotheken. Raadpleeg de volgende bronnen voor meer informatie over de ondersteuning van functie beheer in Spring boot en app-configuratie:

* [Voorbeeld code voor de Spring boot-functie vlag](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Functievlaggen beheren](./manage-feature-flags.md)
