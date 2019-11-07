---
title: Gids voor probleem oplossing voor Azure lente-Cloud | Microsoft Docs
description: Gids voor probleem oplossing voor Azure lente-Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 9603f4a687b55f45be2875ccaa7b801c0c5589c9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607614"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Gids voor probleem oplossing voor veelvoorkomende problemen

In dit artikel worden enkele veelvoorkomende problemen beschreven en stappen voor probleem oplossing voor ontwikkel aars die werken in de Azure lente-Cloud. We raden u ook aan het [artikel over veelgestelde vragen](spring-cloud-faq.md)te lezen.

## <a name="availability-performance-and-application-issues"></a>Problemen met Beschik baarheid, prestaties en toepassingen

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Mijn toepassing kan niet worden gestart (bijvoorbeeld omdat het eind punt niet kan worden verbonden of 502 na enkele nieuwe pogingen)

De logboeken exporteren naar _Azure log Analytics_. De tabel voor Spring-toepassingslogboeken heeft de naam `AppPlatformLogsforSpring`. Ga voor meer informatie naar [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](diagnostic-services.md)

Als u de volgende fout in uw logboeken zoekt, wordt een van de twee mogelijke problemen aangegeven:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Een van de bonen of een van de afhankelijkheden ontbreekt.
* Een van de bean-eigenschappen ontbreekt of is ongeldig In dit geval zult u waarschijnlijk `java.lang.IllegalArgumentException` zien.

Service bindingen kunnen ook start fouten van toepassingen veroorzaken. Gebruik trefwoorden met betrekking tot de gebonden services om de logboeken te doorzoeken.  Stel bijvoorbeeld dat uw toepassing een binding met een MySQL-exemplaar heeft ingesteld op lokale systeem tijd. Als de toepassing niet kan worden gestart, ziet u mogelijk de volgende fout code in het logboek:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Als u deze fout wilt oplossen, gaat u naar de `server parameters` van uw MySql-exemplaar en wijzigt u `time_zone` van `SYSTEM` in `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mijn toepassing is vastgelopen of heeft een onverwachte fout gegenereerd

Als u fouten opspoort in de toepassing, moet u eerst de status en detectie status van de toepassing controleren. Ga naar _app-beheer_ in de Azure Portal om ervoor te zorgen dat alle toepassingenworden _uitgevoerd_ .

* Als de status _actief_ is, maar de detectie status niet _actief is_, gaat u naar [mijn toepassing kan niet worden geregistreerd](#my-application-cannot-be-registered).

* Als de detectie status _actief_is, gaat u naar _metrische gegevens_ om de status van de toepassing te controleren. Inspecteer de volgende metrische gegevens:


  - `TomcatErrorCount` (_Tomcat. Global. error_): alle uitzonde ringen voor de lente toepassing worden hier geteld. Als dit aantal groot is, gaat u naar _Azure log Analytics_ om uw toepassings logboeken te controleren.

  - `AppMemoryMax` (_JVM. Memory. Max_): de maximale hoeveelheid geheugen die beschikbaar is voor de toepassing. Het kan niet worden gedefinieerd of in de loop van de tijd worden gewijzigd indien gedefinieerd. De gebruikte en toegewezen hoeveelheid geheugen is altijd kleiner dan of gelijk aan de maximale hoeveelheid geheugen als deze is gedefinieerd. Een geheugentoewijzing kan echter mislukken met de fout `OutOfMemoryError` als wordt geprobeerd het gebruikte geheugen te verhogen tot meer dan het toegewezen geheugen, ook al wordt de maximale hoeveelheid geheugen niet overschreden. Probeer in dat geval de maximale heap-grootte te verhogen via de parameter `-Xmx`.

  - `AppMemoryUsed` (_JVM. Memory. used_): de hoeveelheid geheugen in bytes die momenteel door de toepassing wordt gebruikt. Voor een Java-toepassing met een normale belasting wordt deze metrische reeks omgezet in een zaagtandpatroon, waarbij het geheugengebruik steeds geleidelijk wordt verhoogd en abrupt wordt verlaagd. Dit komt door garbagecollection in Java Virtual Machine, waarbij verzamelings acties worden weer gegeven op de sawteeth.
    Deze metrische gegevens zijn belang rijk voor het identificeren van geheugen problemen, zoals: * memory-explosie aan het begin van * piek geheugen toewijzing voor een specifiek logisch pad * geleidelijke geheugen lekkages

  Ga naar [metrische](spring-cloud-concept-metrics.md)gegevens voor meer informatie.

Ga naar [het artikel aan de](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) slag om aan de slag te gaan met _Azure log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mijn toepassing heeft een hoog CPU-gebruik of een hoog geheugengebruik

Als uw toepassing een hoog CPU-of geheugen gebruik ondervindt, is een van de twee dingen waar:
* Alle app-exemplaren hebben een hoog CPU-of geheugen gebruik, of
* Enkele van de app-exemplaren hebben een hoog CPU-of geheugen gebruik.

Om te bevestigen welke situatie het is,

1. Ga naar _Metrische gegevens_ en selecteer `Service CPU Usage Percentage` of `Service Memory Used`,
2. Voeg een `App=` filter toe om op te geven welke toepassing u wilt bewaken, en
3. Splits de metrische gegevens op `Instance`.

Als alle instanties een hoge CPU/geheugen hebben, moet u de toepassing uitschalen of de CPU/het geheugen omhoog schalen. Ga voor meer informatie naar [schaal toepassingen](spring-cloud-tutorial-scale-manual.md)

Als sommige instanties een hoge CPU/geheugen ondervinden, controleert u de exemplaar status en de detectie status ervan.

Ga naar [metrische](spring-cloud-concept-metrics.md)gegevens voor meer informatie.

Als alle exemplaren actief zijn, gaat u naar _Azure log Analytics_ om uw toepassings logboeken op te vragen en de code logica te controleren om te zien of deze van invloed kan zijn op schaal partities. Ga naar [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](diagnostic-services.md)voor meer informatie.

Ga naar [het artikel aan de](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) slag om aan de slag te gaan met _Azure log Analytics_. Query's uitvoeren op de logboeken met behulp van de [Kusto-query taal](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Controle lijst voor het onboarden van uw lente toepassing naar Azure lente-Cloud

* De toepassing kan lokaal worden uitgevoerd met de opgegeven Java runtime-versie.
* De omgevings configuratie (CPU/RAM/exemplaren) voldoet aan de minimum vereisten die zijn ingesteld door de provider van de toepassing.
* De configuratie-items hebben verwachte waarden. Zie [config server](spring-cloud-tutorial-config-server.md)(Engelstalig) voor meer informatie.
* De omgevings variabelen hebben verwachte waarden.
* De JVM-para meters hebben verwachte waarden.
* U wordt aangeraden de Inge sloten _Configuratie server_ en de service _register_ service uit het toepassings pakket uit te scha kelen of te verwijderen.
* Als Azure-resources moeten worden gebonden via _servicebinding_, moet u ervoor zorgen dat de doelresources actief zijn.

## <a name="configuration-and-management"></a>Configuratie en beheer

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Er is een probleem opgetreden bij het maken van een Azure lente-Cloud service-exemplaar

Wanneer u probeert een _Azure lente-Cloud_ service-exemplaar in te richten via de portal, wordt de validatie voor u door Azure lente-Cloud uitgevoerd.

Als u echter probeert het _Azure lente-Cloud_ service-exemplaar in te richten via een [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -of [Resource Manager-sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/), controleert u het volgende:

* Het abonnement is actief.
* De locatie wordt [ondersteund](spring-cloud-faq.md) door de _Azure lente-Cloud_.
* De resource groep voor het exemplaar is al gemaakt.
* De resource naam voldoet aan de naamgevings regel. (De naam mag alleen kleine letters, cijfers en afbreek streepjes bevatten. Het eerste teken moet een letter zijn. Het laatste teken moet een letter of getal zijn. De waarde moet tussen 2 en 32 tekens lang zijn.)

Als u probeert het _Azure lente-Cloud_ service-exemplaar in te richten via de Resource Manager-sjabloon, gaat u naar https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates om de syntaxis van de sjabloon te controleren.

De naam van het _Azure veer Cloud_ service-exemplaar wordt gebruikt voor het aanvragen van een subdomeinnaam onder `azureapps.io`, zodat de inrichting mislukt als de naam in conflict is met een bestaande. Meer informatie vindt u in de activiteitenlogboeken.

### <a name="i-cannot-deploy-a-jar-package"></a>Ik kan geen JAR-pakket implementeren

U kunt JAR/source pakket niet uploaden via de portal of Resource Manager-sjabloon.

Wanneer u uw toepassings pakket implementeert met behulp van [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), wordt de voortgang van de implementatie periodiek gecontroleerd en wordt het resultaat weer gegeven.

Als de controles worden onderbroken, kunt u nog steeds de volgende opdrachten gebruiken om de implementatielogboeken op te halen:

`az spring-cloud app show-deploy-log -n <app-name>`

Zorg ervoor dat de toepassing is verpakt in de juiste [uitvoerbare JAR-indeling](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Als dit niet het geval is, ziet u een fout als de volgende:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Ik kan geen bronpakket implementeren

U kunt JAR/source pakket niet uploaden via de portal of Resource Manager-sjabloon.

Wanneer u uw toepassingspakket implementeert via [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), wordt de implementatievoortgang periodiek gecontroleerd en uiteindelijk wordt het implementatieresultaat weergegeven.

Als de controles worden onderbroken, kunt u nog steeds de volgende opdrachten gebruiken om de compilatie- en implementatielogboeken op te halen:

`az spring-cloud app show-deploy-log -n <app-name>`

Houd er echter rekening mee dat één _Azure veer Cloud_ service-exemplaar slechts één bouw taak voor één bron pakket tegelijk kan activeren. Raadpleeg voor meer informatie de hand leiding [een toepassings](spring-cloud-quickstart-launch-app-portal.md) -en [faserings omgeving](spring-cloud-howto-staging-environment.md)implementeren.

### <a name="my-application-cannot-be-registered"></a>Mijn toepassing kan niet worden geregistreerd

In de meeste gevallen gebeurt dit wanneer de vereiste afhankelijkheden/service detectie niet juist zijn geconfigureerd in uw POM-bestand. Na de configuratie wordt het ingebouwde eind punt van het service register server ingevoegd als een omgevings variabele met uw toepassing. Toepassingen worden vervolgens geregistreerd bij de service register server en ontdekken andere afhankelijke micro Services.

Wacht ten minste 2 minuten voordat een nieuw geregistreerd exemplaar het verkeer begint te ontvangen.

Als u een bestaande tot de cloud gebaseerde oplossing naar Azure migreert, moet u ervoor zorgen dat het _REGI ster_ van de ad-hoc service en de _Configuratie server_ worden verwijderd (of uitgeschakeld) om te voor komen dat er conflicten optreden met de beheerde exemplaren van _Azure lente Cloud_ .

U kunt ook client logboeken van _service register_ in _Azure log Analytics_controleren. Ga voor meer informatie naar [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](diagnostic-services.md)

Ga naar [het artikel aan de](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) slag om aan de slag te gaan met _Azure log Analytics_. Query's uitvoeren op de logboeken met behulp van de [Kusto-query taal](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Ik wil de omgevings variabelen van mijn toepassing controleren

Met omgevings variabelen wordt het Azure lente Cloud-Framework op de hoogte gebracht, zodat Azure begrijpt waar en hoe u de Services kunt configureren waaruit uw toepassing bestaat.  Zorg ervoor dat uw omgevings variabelen correct zijn om mogelijke problemen op te lossen.  U kunt het eind punt voor de Spring boot-klep gebruiken om uw omgevings variabelen te controleren.  

> [!WARNING]
> Met deze procedure worden de omgevings variabelen van uw test eindpunt weer gegeven.  Ga niet verder als uw test eindpunt openbaar toegankelijk is of als u een domein naam aan uw toepassing hebt toegewezen.

1. Navigeer naar deze URL: `https://<your application test endpoint>/actuator/health`.  
    - Een antwoord dat lijkt op `{"status":"UP"}` geeft aan dat het eind punt is ingeschakeld.
    - Als het antwoord negatief is, neemt u de volgende afhankelijkheden op in uw `POM.xml`:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Als het eind punt voor de installatie van de veer boot is ingeschakeld, gaat u naar de Azure Portal en zoekt u de configuratie pagina van uw toepassing.  Voeg een omgevings variabele toe met de naam `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` en de waarde `*`. 

1. Start de toepassing opnieuw.

1. Navigeer naar `https://<the test endpoint of your app>/actuator/env` en controleer het antwoord.  Dit ziet er als volgt uit:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Zoek het onderliggende knoop punt met de naam `systemEnvironment`.  Dit knoop punt bevat de omgevings variabelen van uw toepassing.

> [!IMPORTANT]
> Vergeet niet om de bloot stelling van uw omgevings variabelen terug te draaien voordat u uw toepassing toegankelijk maakt voor het publiek.  Ga naar de Azure Portal, zoek de configuratie pagina van uw toepassing en verwijder deze omgevings variabele: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Ik kan geen metrische gegevens of logboeken vinden voor mijn toepassing

Ga naar _app-beheer_ om te _controleren of de_toepassing wordt _uitgevoerd_ .

Als u metrische gegevens kunt zien van _JVM_ maar geen metrische gegevens van _Tomcat_, controleert u of de`spring-boot-actuator` afhankelijkheid is ingeschakeld in uw toepassings pakket en wordt opgestart.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Als uw toepassingslogboeken kunnen worden gearchiveerd in een opslagaccount, maar niet verzonden naar _Azure Log Analytics_, controleert u of u [de werkruimte juist hebt ingesteld](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Als u een gratis laag van _Azure log Analytics_gebruikt, moet u er rekening mee houden dat [de gratis laag geen sla biedt](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).