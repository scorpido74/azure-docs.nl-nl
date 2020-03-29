---
title: Handleiding voor probleemoplossing voor Azure Spring Cloud | Microsoft Documenten
description: Handleiding voor probleemoplossing voor Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277588"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Veelvoorkomende Azure Spring Cloud-problemen oplossen

In dit artikel vindt u instructies voor het oplossen van problemen met azure spring cloud-ontwikkelingsproblemen. Zie [Veelgestelde vragen over Azure Spring Cloud voor](spring-cloud-faq.md)meer informatie.

## <a name="availability-performance-and-application-issues"></a>Problemen met beschikbaarheid, prestaties en toepassingen

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Mijn toepassing kan niet worden gestart (het eindpunt kan bijvoorbeeld niet worden aangesloten of het retourneert een 502 na een paar nieuwe pogingen)

Exporteer de logboeken naar Azure Log Analytics. De tabel voor logboeken van de toepassing van de lente wordt genoemd *AppPlatformLogsforSpring*. Zie [Logboeken en statistieken analyseren met diagnostische instellingen](diagnostic-services.md)voor meer informatie.

Het volgende foutbericht wordt mogelijk weergegeven in uw logboeken:

> "org.springframework.context.ApplicationContextException: Niet in staat om webserver te starten"

Het bericht geeft een van de twee waarschijnlijke problemen aan: 
* Een van de bonen of een van zijn afhankelijkheden ontbreekt.
* Een van de bean-eigenschappen ontbreekt of is ongeldig In dit geval zal "java.lang.IllegalArgumentException" waarschijnlijk worden weergegeven.

Servicebindingen kunnen ook leiden tot fouten bij het starten van toepassingen. Als u de logboeken wilt opvragen, gebruikt u trefwoorden die gerelateerd zijn aan de gebonden services. Laten we er bijvoorbeeld van uitgaan dat uw toepassing een binding heeft met een MySQL-exemplaar dat is ingesteld op lokale systeemtijd. Als de toepassing niet wordt gestart, wordt mogelijk het volgende foutbericht in het logboek weergegeven:

> "java.sql.SQLException: De servertijdzonewaarde 'Coordinated Universal Time' wordt niet herkend of vertegenwoordigt meer dan één tijdzone."

Als u deze fout `server parameters` wilt oplossen, gaat u `time_zone` naar de van uw MySQL-exemplaar en wijzigt u de waarde van *SYSTEEM* naar *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mijn toepassing is vastgelopen of heeft een onverwachte fout gegenereerd

Wanneer u toepassingsvastabuggen foutloopt, controleert u eerst de status en detectiestatus van de toepassing. Ga hiervoor naar _App-beheer_ in de Azure-portal om ervoor te zorgen dat de statussen van alle toepassingen _actief_ en _UP_zijn.

* Als de status _actief is,_ maar de detectiestatus niet _UP_is, gaat u naar de sectie ['Mijn toepassing kan niet worden geregistreerd'.](#my-application-cant-be-registered)

* Als de detectiestatus _UP_is, gaat u naar Metrische gegevens om de status van de toepassing te controleren. Controleer de volgende statistieken:


  - `TomcatErrorCount`(_tomcat.global.error):_ Alle uitzonderingen op de voorjaarstoepassing worden hier geteld. Als dit aantal groot is, gaat u naar Azure Log Analytics om uw toepassingslogboeken te inspecteren.

  - `AppMemoryMax`(_jvm.memory.max):_ De maximale hoeveelheid geheugen beschikbaar voor de toepassing. Het bedrag kan niet worden gedefinieerd of in de loop van de tijd veranderen als het wordt gedefinieerd. Als het is gedefinieerd, is de hoeveelheid gebruikt en vastgelegd geheugen altijd minder dan of gelijk aan max. Een geheugentoewijzing kan echter `OutOfMemoryError` mislukken met een bericht als de toewijzing probeert het gebruikte geheugen zodanig te verhogen dat *> vastgelegd*, zelfs als <= max wordt *gebruikt,* nog steeds waar is. Probeer in een dergelijke situatie de maximale `-Xmx` heapgrootte te vergroten met behulp van de parameter.

  - `AppMemoryUsed`(_jvm.memory.used):_ De hoeveelheid geheugen in bytes die momenteel wordt gebruikt door de toepassing. Voor een normale lading Java-toepassing vormt deze metrische reeks een *zaagtandpatroon,* waarbij het geheugengebruik gestaag toeneemt en afneemt in kleine stappen en plotseling veel daalt, en dan het patroon terugkeert. Deze metrische serie treedt op als gevolg van garbage collection in Java virtuele machine, waar de verzameling acties vertegenwoordigen druppels op de zaagtand patroon.
    
    Deze statistiek is belangrijk om geheugenproblemen te identificeren, zoals:
    * Een geheugenexplosie aan het begin.
    * De toewijzing van piekgeheugenvoor een specifiek logicapad.
    * Geleidelijk geheugen lekt.

  Zie [Statistieken voor](spring-cloud-concept-metrics.md)meer informatie .

Zie [Aan de slag met Logboekanalyse in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie over Azure Log Analytics.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mijn toepassing heeft een hoog CPU-gebruik of een hoog geheugengebruik

Als uw toepassing een hoog CPU- of geheugengebruik ervaart, is een van de twee dingen waar:
* Alle app-exemplaren ervaren een hoog CPU- of geheugengebruik.
* Sommige app-exemplaren ervaren een hoog CPU- of geheugengebruik.

Ga als volgt te werk om na te gaan welke situatie van toepassing is:

1. Ga naar **Metrics**en selecteer vervolgens **het service-CPU-gebruikspercentage** of **het gebruikte servicegeheugen**.
2. Voeg een **App=-filter** toe om op te geven welke toepassing u wilt controleren.
3. De statistieken splitsen op **instantie**.

Als *alle exemplaren* een hoog CPU- of geheugengebruik ervaren, moet u de toepassing uitschalen of het CPU- of geheugengebruik opschalen. Zie [Zelfstudie: Een toepassing schalen in Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md)voor meer informatie.

Als *in sommige gevallen* een hoog CPU- of geheugengebruik wordt ervaren, controleert u de instantiestatus en de detectiestatus.

Zie [Statistieken voor Azure Spring Cloud voor](spring-cloud-concept-metrics.md)meer informatie .

Als alle exemplaren actief zijn, gaat u naar Azure Log Analytics om uw toepassingslogboeken op te vragen en uw codelogica te controleren. Zo u zien of een van deze gevolgen kan hebben voor schaalverdeling. Zie [Logboeken en statistieken analyseren met diagnostische instellingen voor](diagnostic-services.md)meer informatie.

Zie [Aan de slag met Logboekanalyse in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie over Azure Log Analytics. Vraag de logboeken met de [Kusto-querytaal](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Checklist voor het implementeren van uw voorjaarstoepassing naar Azure Spring Cloud

Voordat u aan boord van uw aanvraag, ervoor te zorgen dat het voldoet aan de volgende criteria:

* De toepassing kan lokaal worden uitgevoerd met de opgegeven Java-runtime-versie.
* De omgevingsconfig (CPU/RAM/Instances) voldoet aan de minimale eis die is ingesteld door de toepassingsprovider.
* De configuratie-items hebben hun verwachte waarden. Zie [Config Server](spring-cloud-tutorial-config-server.md)voor meer informatie.
* De omgevingsvariabelen hebben hun verwachte waarden.
* De JVM-parameters hebben hun verwachte waarden.
* We raden u aan de ingesloten _Config Server-_ en _Spring Service Registry-services_ uit het toepassingspakket uit te schakelen of te verwijderen.
* Als Azure-resources moeten worden gebonden via _servicebinding_, moet u ervoor zorgen dat de doelresources actief zijn.

## <a name="configuration-and-management"></a>Configuratie en beheer

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Ik heb een probleem ondervonden bij het maken van een Azure Spring Cloud-service-exemplaar

Wanneer u een Azure Spring Cloud-service-exemplaar instelt met behulp van de Azure-portal, voert Azure Spring Cloud de validatie voor u uit.

Maar als u de instantie Azure Spring Cloud-service probeert in te stellen met behulp van de azure [CLI-](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) of Azure [Resource Manager-sjabloon,](https://docs.microsoft.com/azure/azure-resource-manager/)controleert u of:

* Het abonnement is actief.
* De locatie [wordt ondersteund](spring-cloud-faq.md) door Azure Spring Cloud.
* De resourcegroep voor de instantie is al gemaakt.
* De resourcenaam voldoet aan de naamgevingsregel. Het mag alleen kleine letters, cijfers en koppeltekens bevatten. Het eerste teken moet een letter zijn. Het laatste teken moet een letter of getal zijn. De waarde moet 2 tot 32 tekens bevatten.

Als u de azure spring cloud-service-instantie wilt instellen met behulp van de sjabloon Resourcebeheer, raadpleegt u eerst [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

De naam van de Azure Spring Cloud-serviceinstantie wordt gebruikt `azureapps.io`voor het aanvragen van een subdomeinnaam onder , zodat de instelling mislukt als de naam in strijd is met een bestaande naam. Mogelijk vindt u meer details in de activiteitenlogboeken.

### <a name="i-cant-deploy-a-jar-package"></a>Ik kan geen JAR-pakket implementeren

U het JAR-bestand (Java Archive)/bronpakket niet uploaden met behulp van de Azure-portal of de sjabloon ResourceManager.

Wanneer u uw toepassingspakket implementeert met behulp van de [Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)wordt de implementatievoortgang in Azure CLI periodiek gepeild en wordt uiteindelijk het implementatieresultaat weergegeven.

Als de controles worden onderbroken, kunt u nog steeds de volgende opdrachten gebruiken om de implementatielogboeken op te halen:

`az spring-cloud app show-deploy-log -n <app-name>`

Zorg ervoor dat uw toepassing is verpakt in de juiste [uitvoerbare JAR-indeling.](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html) Als het niet correct is verpakt, ontvangt u een foutbericht dat vergelijkbaar is met het volgende:

> "Fout: Ongeldig of corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Ik kan geen bronpakket implementeren

U jar/bronpakket niet uploaden met de Azure-portal of de sjabloon ResourceManager.

Wanneer u uw toepassingspakket implementeert met behulp van de [Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)wordt de implementatievoortgang in Azure CLI periodiek gepeild en wordt uiteindelijk het implementatieresultaat weergegeven.

Als de controles worden onderbroken, kunt u nog steeds de volgende opdrachten gebruiken om de compilatie- en implementatielogboeken op te halen:

`az spring-cloud app show-deploy-log -n <app-name>`

Houd er echter rekening mee dat één Azure Spring Cloud-serviceinstantie slechts één buildtaak voor één bronpakket tegelijk kan activeren. Zie [Een toepassing implementeren](spring-cloud-quickstart-launch-app-portal.md) en Een [faseringsomgeving instellen in Azure Spring Cloud](spring-cloud-howto-staging-environment.md)voor meer informatie.

### <a name="my-application-cant-be-registered"></a>Mijn aanvraag kan niet worden geregistreerd

In de meeste gevallen treedt deze situatie op wanneer *vereiste afhankelijkheden* en *servicedetectie* niet goed zijn geconfigureerd in het POM-bestand (Project Object Model). Zodra het is geconfigureerd, wordt het ingebouwde serviceregisterservereindpunt geïnjecteerd als een omgevingsvariabele met uw toepassing. Toepassingen registreren zich vervolgens bij de Service Registry-server en ontdekken andere afhankelijke microservices.

Wacht ten minste twee minuten voordat een nieuw geregistreerde instantie verkeer begint te ontvangen.

Als u een bestaande op Spring Cloud gebaseerde oplossing migreert naar Azure, moet u ervoor zorgen dat uw _ad-hocserviceregister_ en _Config Server-exemplaren_ worden verwijderd (of uitgeschakeld) om te voorkomen dat deze in strijd zijn met de beheerde exemplaren van Azure Spring Cloud.

U ook de clientlogboeken van het _serviceregister_ controleren in Azure Log Analytics. Zie [Logboeken en statistieken analyseren met diagnostische instellingen voor](diagnostic-services.md) meer informatie

Zie [Aan de slag met Logboekanalyse in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie over Azure Log Analytics. Vraag de logboeken met de [Kusto-querytaal](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Ik wil de omgevingsvariabelen van mijn toepassing inspecteren

Omgevingsvariabelen informeren het Azure Spring Cloud-framework en zorgen ervoor dat Azure begrijpt waar en hoe u de services configureren waaruit uw toepassing bestaat. Ervoor zorgen dat uw omgevingsvariabelen correct zijn, is een noodzakelijke eerste stap in het oplossen van mogelijke problemen.  U het eindpunt van de Spring Boot Actuator gebruiken om uw omgevingsvariabelen te bekijken.  

> [!WARNING]
> Deze procedure legt uw omgevingsvariabelen bloot met behulp van uw testeindpunt.  Ga niet verder als uw testeindpunt openbaar toegankelijk is of als u een domeinnaam aan uw toepassing hebt toegewezen.

1. Ga naar `https://<your application test endpoint>/actuator/health`.  
    - Een antwoord `{"status":"UP"}` dat vergelijkbaar is met dat het eindpunt is ingeschakeld.
    - Als het antwoord negatief is, neemt u de volgende afhankelijkheid op in uw *POM.xml-bestand:*

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Als het eindpunt van de VeerbootActuator is ingeschakeld, gaat u naar de Azure-portal en zoekt u naar de configuratiepagina van uw toepassing.  Voeg een omgevingsvariabele `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` toe met `*` de naam en de waarde . 

1. Start uw toepassing opnieuw.

1. Ga `https://<your application test endpoint>/actuator/env` naar en inspecteer het antwoord.  Dit ziet er als volgt uit:

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

Zoek naar het kinderknooppunt met de naam `systemEnvironment`.  Dit knooppunt bevat de omgevingsvariabelen van uw toepassing.

> [!IMPORTANT]
> Vergeet niet om de belichting van uw omgevingsvariabelen om te keren voordat u uw toepassing toegankelijk maakt voor het publiek.  Ga naar de Azure-portal, zoek naar de configuratiepagina van `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`uw toepassing en verwijder deze omgevingsvariabele: .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Ik kan geen statistieken of logboeken voor mijn toepassing vinden

Ga naar **App-beheer** om ervoor te zorgen dat de toepassingsstatussen _actief_ en _UP_zijn.

Als u statistieken van _JVM_ zien, maar geen statistieken `spring-boot-actuator` van _Tomcat,_ controleert u of de afhankelijkheid is ingeschakeld in uw toepassingspakket en of deze met succes wordt opgestart.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Als uw toepassingslogboeken kunnen worden gearchiveerd naar een opslagaccount, maar niet naar Azure Log Analytics worden verzonden, controleert u of u [uw werkruimte correct hebt ingesteld.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Als u een gratis laag Azure Log Analytics gebruikt, moet u er rekening mee houden dat [de gratis laag geen SLA (Service-level Agreement) biedt.](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)
