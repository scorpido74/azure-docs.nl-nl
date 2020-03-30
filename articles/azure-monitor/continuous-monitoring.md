---
title: Continue bewaking met Azure Monitor | Microsoft Documenten
description: Beschrijft specifieke stappen voor het gebruik van Azure Monitor om continue bewaking in uw werkstromen mogelijk te maken.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: b9ca8a703ed8a84148abd23e90114402d8806bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667190"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Continue bewaking met Azure Monitor

Continue monitoring verwijst naar het proces en de technologie die nodig zijn om monitoring op te nemen in elke fase van uw levenscyclus van DevOps en IT-bewerkingen. Het helpt om continu de gezondheid, prestaties en betrouwbaarheid van uw toepassing en infrastructuur te garanderen terwijl deze van ontwikkeling naar productie gaat. Continue monitoring bouwt voort op de concepten van Continuous Integration and Continuous Deployment (CI/CD) die u helpen om software sneller en betrouwbaarder te ontwikkelen en te leveren om continue waarde te bieden aan uw gebruikers.

[Azure Monitor](overview.md) is de uniforme bewakingsoplossing in Azure die volledige onwaarvindbaarheid biedt voor toepassingen en infrastructuur in de cloud en on-premises. Het werkt naadloos samen met [Visual Studio en Visual Studio Code](https://visualstudio.microsoft.com/) tijdens de ontwikkeling en test en integreert met Azure [DevOps](/azure/devops/user-guide/index) voor releasebeheer en werkitembeheer tijdens implementatie en bewerkingen. Het integreert zelfs alle ITSM- en SIEM-tools van uw keuze om problemen en incidenten binnen uw bestaande IT-processen bij te houden.

In dit artikel worden specifieke stappen beschreven voor het gebruik van Azure Monitor om continue bewaking in uw werkstromen mogelijk te maken. Het bevat links naar andere documentatie die details geeft over het implementeren van verschillende functies.


## <a name="enable-monitoring-for-all-your-applications"></a>Monitoring inschakelen voor al uw toepassingen
Om waarneembaar te worden in uw hele omgeving, moet u monitoring inschakelen op al uw webapplicaties en services. Hierdoor u eenvoudig end-to-end transacties en verbindingen over alle componenten visualiseren.

- [Azure DevOps Projects](../devops-project/overview.md) bieden u een vereenvoudigde ervaring met uw bestaande code en Git-repository, of kiezen uit een van de voorbeeldtoepassingen om een Continuous Integration (CI) en Continuous Delivery (CD)-pijplijn naar Azure te maken.
- [Met continue bewaking in uw DevOps-releasepijplijn](../azure-monitor/app/continuous-monitoring.md) u uw implementatie gate- of terugdraaien op basis van bewakingsgegevens.
- [Met Statusmonitor](../azure-monitor/app/monitor-performance-live-website-now.md) u een live .NET-app op Windows implementeren met Azure Application Insights, zonder dat u uw code hoeft te wijzigen of opnieuw te implementeren.
- Als u toegang hebt tot de code voor uw toepassing, schakelt u volledige bewaking in met [Application Insights](../azure-monitor/app/app-insights-overview.md) door de Azure Monitor Application Insights SDK voor [.NET,](../azure-monitor/learn/quick-monitor-portal.md) [Java,](../azure-monitor/app/java-get-started.md) [Node.js](../azure-monitor/learn/nodejs-quick-start.md)of [andere programmeertalen](../azure-monitor/app/platforms.md)te installeren. Hiermee u aangepaste gebeurtenissen, statistieken of paginaweergaven opgeven die relevant zijn voor uw toepassing en uw bedrijf.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Monitoring inschakelen voor uw hele infrastructuur
Toepassingen zijn net zo betrouwbaar als hun onderliggende infrastructuur. Als u monitoring hebt ingeschakeld in uw hele infrastructuur, u volledige waarneembaarheid bereiken en het gemakkelijker maken om een potentiële oorzaak te ontdekken wanneer iets mislukt. Azure Monitor helpt u de status en prestaties van uw volledige hybride infrastructuur bij te houden, inclusief resources zoals VM's, containers, opslag en netwerk.

- U krijgt automatisch [platformstatistieken, activiteitslogboeken en diagnostische logboeken](platform/data-sources.md) van de meeste van uw Azure-bronnen zonder configuratie.
- Schakel diepere bewaking voor VM's in met [Azure Monitor voor VM's.](insights/vminsights-overview.md)
-  Schakel diepere bewaking in voor AKS-clusters met [Azure Monitor voor containers.](insights/container-insights-overview.md)
- Voeg [bewakingsoplossingen](insights/solutions-inventory.md) toe voor verschillende toepassingen en services in uw omgeving.


[Infrastructuur als code](/azure/devops/learn/what-is-infrastructure-as-code) is het beheer van de infrastructuur in een beschrijvend model, met dezelfde versie als DevOps teams gebruiken voor broncode. Het voegt betrouwbaarheid en schaalbaarheid toe aan uw omgeving en stelt u in staat om vergelijkbare processen te gebruiken die worden gebruikt om uw toepassingen te beheren.

-  Gebruik [Resourcebeheersjablonen](platform/template-workspace-configuration.md) om waarschuwingen over een grote set resources te controleren en te configureren.
- Gebruik [Azure Policy](../governance/policy/overview.md) om verschillende regels voor uw resources af te dwingen. Dit zorgt ervoor dat deze resources voldoen aan uw bedrijfsstandaarden en servicelevelovereenkomsten. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Resources in Azure Resource-groepen combineren
Een typische toepassing op Azure bevat tegenwoordig meerdere bronnen, zoals VM's en App Services of microservices die worden gehost op Cloud Services, AKS-clusters of Service Fabric. Deze toepassingen maken vaak gebruik van afhankelijkheden zoals Event Hubs, Storage, SQL en Service Bus.

- Combineer resources in Azure Resource Groups om volledige zichtbaarheid te krijgen in al uw resources die deel uitmaken van uw verschillende toepassingen. [Azure Monitor for Resource Groups](../azure-monitor/insights/resource-group-insights.md) biedt een eenvoudige manier om de status en prestaties van uw volledige volledige stack-toepassing bij te houden en maakt het mogelijk om in betreffende componenten te worden opgenomen voor onderzoeken of foutopsporing.

## <a name="ensure-quality-through-continuous-deployment"></a>Zorg voor kwaliteit door continue implementatie
Met continue integratie / continue implementatie u automatisch codewijzigingen in uw toepassing integreren en implementeren op basis van de resultaten van geautomatiseerde tests. Het stroomlijnt het implementatieproces en garandeert de kwaliteit van eventuele wijzigingen voordat ze in productie gaan.


- Gebruik [Azure Pipelines](/azure/devops/pipelines) om continue implementatie te implementeren en uw hele proces te automatiseren, van codecommit tot productie op basis van uw CI/CD-tests.
- Gebruik [Kwaliteitsgates](/azure/devops/pipelines/release/approvals/gates) om monitoring te integreren in uw pre-implementatie of post-deployment. Dit zorgt ervoor dat u voldoet aan de belangrijkste health/performance metrics (KPI's) terwijl uw toepassingen van dev naar productie gaan en eventuele verschillen in de infrastructuuromgeving of -schaal geen negatieve gevolgen hebben voor uw KPI's.
- [Houd afzonderlijke bewakingsinstanties tussen](../azure-monitor/app/separate-resources.md) uw verschillende implementatieomgevingen zoals Dev, Test, Canary en Prod. Dit zorgt ervoor dat verzamelde gegevens relevant zijn voor de bijbehorende toepassingen en infrastructuur. Als u gegevens in verschillende omgevingen moet correleren, u [grafieken met meerdere resources](../azure-monitor/platform/metrics-charts.md) gebruiken in Metrics Explorer of [kruisbronquery's](log-query/cross-workspace-query.md)maken in Azure Monitor.


## <a name="create-actionable-alerts-with-actions"></a>Bruikbare waarschuwingen maken met acties
Een cruciaal aspect van de monitoring is proactief beheerders op de hoogte te stellen van actuele en voorspelde problemen. 

- Maak [waarschuwingen in Azure Monitor](../azure-monitor/platform/alerts-overview.md) op basis van logboeken en statistieken om voorspelbare foutstatussen te identificeren. U moet een doel hebben om alle waarschuwingen bruikbaar te maken, wat betekent dat ze werkelijke kritieke omstandigheden vertegenwoordigen en proberen fout-positieven te verminderen. Gebruik [Dynamische drempels](platform/alerts-dynamic-thresholds.md) om basislijnen op metrische gegevens automatisch te berekenen in plaats van uw eigen statische drempelwaarden te definiëren. 
- Definieer acties voor waarschuwingen om de meest effectieve manier te gebruiken om uw beheerders hiervan op de hoogte te stellen. Beschikbare [acties voor meldingen](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) zijn sms,e-mails, pushmeldingen of spraakoproepen.
- Gebruik meer geavanceerde acties om [verbinding te maken met uw ITSM-tool](platform/itsmc-overview.md) of andere waarschuwingsbeheersystemen via [webhooks.](platform/activity-log-alerts-webhook.md)
- Herstel situaties die ook in waarschuwingen zijn geïdentificeerd met [Azure Automation-runbooks](../automation/automation-webhooks.md) of [Logische apps](/connectors/custom-connectors/create-webhook-trigger) die kunnen worden gestart vanaf een waarschuwing met behulp van webhooks. 
- Gebruik [automatisch schalen](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) om uw rekenresources dynamisch te verhogen en te verlagen op basis van verzamelde statistieken.

## <a name="prepare-dashboards-and-workbooks"></a>Dashboards en werkmappen voorbereiden
Als u ervoor zorgt dat uw ontwikkeling en bewerkingen toegang hebben tot dezelfde telemetrie en tools, kunnen ze patronen in uw hele omgeving bekijken en uw Mean Time To Detect (MTTD) en Mean Time To Restore (MTTR) minimaliseren.

- Bereid [aangepaste dashboards](../azure-monitor/learn/tutorial-app-dashboards.md) voor op basis van algemene statistieken en logboeken voor de verschillende rollen in uw organisatie. Dashboards kunnen gegevens uit alle Azure-bronnen combineren.
- [Werkmappen voorbereiden](../azure-monitor/app/usage-workbooks.md) om kennisdeling tussen ontwikkeling en bedrijfsvoering te waarborgen. Deze kunnen worden opgesteld als dynamische rapporten met metrische grafieken en logboekquery's, of zelfs als handleidingen voor het oplossen van problemen die zijn opgesteld door ontwikkelaars die klantenondersteuning of bewerkingen helpen om basisproblemen af te handelen.

## <a name="continuously-optimize"></a>Continu optimaliseren
 Monitoring is een van de fundamentele aspecten van de populaire Build-Measure-Learn filosofie, die aanbeveelt om continu uw KPI's en gebruikersgedragsstatistieken te volgen en deze vervolgens te optimaliseren door middel van planningsiteraties. Azure Monitor helpt u bij het verzamelen van statistieken en logboeken die relevant zijn voor uw bedrijf en om nieuwe gegevenspunten toe te voegen in de volgende implementatie als dat nodig is.

- Gebruik tools in Application Insights om het gedrag en de betrokkenheid van eindgebruikers bij te [houden.](../azure-monitor/learn/tutorial-users.md)
- Gebruik [Impact Analysis](../azure-monitor/app/usage-impact.md) om u te helpen bij het prioriteren van welke gebieden u moet richten om naar belangrijke KPI's te rijden.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de verschillen van [Azure Monitor](overview.md).
- [Voeg continue bewaking toe](../azure-monitor/app/continuous-monitoring.md) aan uw releasepijplijn.
