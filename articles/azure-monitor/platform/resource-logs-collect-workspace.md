---
title: Azure-bronlogboeken verzamelen in de werkruimte Log Analytics
description: Meer informatie over het streamen van Azure-bronlogboeken naar een Log Analytics-werkruimte in Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248591"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-platformlogboeken verzamelen in de werkruimte Log Analytics in Azure Monitor
[Platformlogboeken](platform-logs-overview.md) in Azure, inclusief Azure Activity log en resource logs, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. In dit artikel worden bronlogboeken verzameld in een werkruimte van Log Analytics waarmee u deze analyseren met andere monitoringgegevens die zijn verzameld in Azure Monitor-logboeken met behulp van krachtige logboekquery's en ook andere Azure Monitor-functies zoals waarschuwingen en Visualisaties. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Wat u doen met platformlogboeken in een werkruimte
Met het verzamelen van platformlogboeken in een Log Analytics-werkruimte u de logboeken van al uw Azure-bronnen samen analyseren en profiteren van alle functies die beschikbaar zijn voor [Azure Monitor-logboeken,](data-platform-logs.md) waaronder het volgende:

* **Logboekquery's** - [Logquery's](../log-query/log-query-overview.md) maken met behulp van een krachtige querytaal om snel uw diagnostische gegevens te analyseren en inzicht te krijgen in uw diagnostische gegevens en deze te analyseren met gegevens die zijn verzameld uit andere bronnen in Azure Monitor.
* **Waarschuwingen** - Ontvang proactieve meldingen van kritieke omstandigheden en patronen die in uw bronlogboeken zijn geïdentificeerd met behulp van [logboekwaarschuwingen in Azure Monitor.](alerts-log.md)
* **Visualisaties** - Maak de resultaten van een logboekquery vast aan een Azure-dashboard of neem deze op in een werkmap als onderdeel van een interactief rapport.

## <a name="prerequisites"></a>Vereisten
U moet [een nieuwe werkruimte maken](../learn/quick-create-workspace.md) als u er nog geen hebt. De werkruimte hoeft zich niet in hetzelfde abonnement te bevinden als de logboeken voor het verzenden van resources, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang tot beide abonnementen heeft.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Stuur platformlogboeken naar een Log Analytics-werkruimte en andere bestemmingen door een diagnostische instelling voor een Azure-bron te maken. Zie [Diagnostische instelling maken om logboeken en statistieken in Azure](diagnostic-settings.md) te verzamelen voor meer informatie.


## <a name="activity-log-collection"></a>Verzameling van activiteitenlogboeken
U het activiteitenlogboek van één abonnement naar maximaal vijf Log Analytics-werkruimten verzenden. Gegevens van resourcelogboeken die zijn verzameld in een werkruimte Log Analytics, worden opgeslagen in de tabel **AzureActivity.** 

## <a name="resource-log-collection-mode"></a>Resourcelogboekverzamelingsmodus
Gegevens van resourcelogboeken die in een werkruimte Log Analytics worden verzameld, worden opgeslagen in tabellen zoals beschreven in [Structuur van Azure-monitorlogboeken.](../log-query/logs-structure.md) De tabellen die worden gebruikt door resourcelogboeken, zijn afhankelijk van het type verzameling dat de resource gebruikt:

- Azure-diagnose - Alle gegevens die zijn geschreven, zijn naar de tabel _AzureDiagnostics._
- Resourcespecifiek - Gegevens worden naar de afzonderlijke tabel geschreven voor elke categorie van de resource.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics-modus 
In deze modus worden alle gegevens uit een [diagnostische instelling](diagnostic-settings.md) verzameld in de tabel _AzureDiagnostics._ Dit is de verouderde methode die vandaag de dag door de meeste Azure-services wordt gebruikt.

Aangezien meerdere resourcetypen gegevens naar dezelfde tabel verzenden, is het schema de superset van de schema's van alle verschillende gegevenstypen die worden verzameld.

Houd rekening met het volgende voorbeeld waarbij diagnostische instellingen in dezelfde werkruimte worden verzameld voor de volgende gegevenstypen:

- Controlelogboeken van service 1 (met een schema bestaande uit kolommen A, B en C)  
- Foutlogboeken van service 1 (met een schema bestaande uit kolommen D, E en F)  
- Controlelogboeken van service 2 (met een schema bestaande uit kolommen G, H en I)  

De tabel AzureDiagnostics ziet er als volgt uit:  

| ResourceProvider    | Categorie     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | Controlelogboeken    | x1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | Foutlogboeken    |    |    |    | Q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | Controlelogboeken    |    |    |    |    |    |    | j1 | k1 k1 | l1 |
| Microsoft.Service1 | Foutlogboeken    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | Controlelogboeken    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | Controlelogboeken    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Resourcespecifiek
In deze modus worden afzonderlijke tabellen in de geselecteerde werkruimte gemaakt voor elke categorie die is geselecteerd in de diagnostische instelling. Deze methode wordt aanbevolen omdat het veel gemakkelijker maakt om te werken met de gegevens in logquery's, biedt een betere vindbaarheid van schema's en hun structuur, verbetert de prestaties in zowel de innamelaten als de querytijden, en de mogelijkheid om RBAC-rechten op een specifieke tabel. Alle Azure-services migreren uiteindelijk naar de modus Resource-specifiek. 

Het bovenstaande voorbeeld zou resulteren in drie tabellen worden gemaakt:
 
- *TabelService1AuditLogs* als volgt:

    | Resourceprovider | Categorie | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | Controlelogboeken | x1 | y1 | Z1 |
    | Service1 | Controlelogboeken | x5 | y5 | z5 |
    | ... |

- *Tabelservice1ErrorLogs* als volgt:  

    | Resourceprovider | Categorie | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | Foutlogboeken |  Q1 | w1 | e1 |
    | Service1 | Foutlogboeken |  q2 | w2 | e2 |
    | ... |

- *TabelService2AuditLogs* als volgt:  

    | Resourceprovider | Categorie | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | Controlelogboeken | j1 | k1 k1 | l1|
    | Service2 | Controlelogboeken | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>De verzamelingsmodus selecteren
De meeste **Azure-bronnen** schrijven gegevens naar de werkruimte in de azure **diagnostic-** of resourcespecifieke modus zonder dat u een keuze hebt. Zie de [documentatie voor elke service](diagnostic-logs-schema.md) voor meer informatie over de modus die deze gebruikt. Alle Azure-services maken uiteindelijk gebruik van de modus Resource-specific. Als onderdeel van deze overgang u met sommige resources een modus in de diagnostische instelling selecteren. U moet resourcespecifieke modus opgeven voor nieuwe diagnostische instellingen, omdat dit de gegevens eenvoudiger te beheren maakt en u kan helpen om complexe migraties op een later tijdstip te voorkomen.
  
   ![Optie voor de modus Diagnostische instellingen](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Op dit moment kunnen **Azure-diagnostische gegevens** en resourcespecifieke modus alleen worden geselecteerd bij het configureren van de diagnostische instelling in de **Azure-portal.** Als u de instelling configureert met CLI, PowerShell of Rest API, wordt deze standaard ingesteld op **Azure-diagnose.**

U een bestaande diagnostische instelling wijzigen in resourcespecifieke modus. In dit geval blijven gegevens die al zijn verzameld in de tabel _AzureDiagnostics_ totdat deze zijn verwijderd op basis van de bewaarinstelling voor de werkruimte. Nieuwe gegevens worden verzameld in de speciale tabel. Gebruik de [union operator](https://docs.microsoft.com/azure/kusto/query/unionoperator) om gegevens in beide tabellen op te vragen.

Blijf [de blog van Azure Updates](https://azure.microsoft.com/updates/) bekijken voor aankondigingen over azure-services die de modus Resource-specifieke ondersteuning ondersteunen.

### <a name="column-limit-in-azurediagnostics"></a>Kolomlimiet in AzureDiagnostics
Er is een eigenschapslimiet van 500 voor elke tabel in Azure Monitor-logboeken. Zodra deze limiet is bereikt, worden alle rijen met gegevens met een eigenschap buiten de eerste 500 bij inname tijd verwijderd. De tabel *AzureDiagnostics* is met name gevoelig voor deze limiet, omdat deze eigenschappen bevat voor alle Azure-services die ernaar schrijven.

Als u resourcelogboeken verzamelt van meerdere services, kan _AzureDiagnostics_ deze limiet overschrijden en worden gegevens gemist. Totdat alle azure-services resourcespecifieke modus ondersteunen, moet u resources configureren om naar meerdere werkruimten te schrijven om de kans op het bereiken van de limiet van 500 kolom te verkleinen.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory is vanwege een zeer gedetailleerde set logboeken een service waarvan bekend is dat deze een groot aantal kolommen schrijft en mogelijk _azurediagnostics_ de limiet heeft overschreden. Voor diagnostische instellingen die zijn geconfigureerd voordat de resourcespecifieke modus is ingeschakeld, wordt er een nieuwe kolom gemaakt voor elke unieke gebruikersparameter die wordt genoemd tegen elke activiteit. Meer kolommen zullen worden gemaakt vanwege de verbose aard van activiteit ingangen en uitgangen.
 
U moet uw logboeken zo snel mogelijk migreren om de resourcespecifieke modus te gebruiken. Als u dit niet onmiddellijk doen, is een voorlopig alternatief het isoleren van Azure Data Factory-logboeken in hun eigen werkruimte om de kans te minimaliseren dat deze logboeken invloed hebben op andere logboektypen die in uw werkruimten worden verzameld.


## <a name="next-steps"></a>Volgende stappen

* [Lees meer over resourcelogs](platform-logs-overview.md).
* [Diagnostische instelling maken om logboeken en statistieken in Azure te verzamelen.](diagnostic-settings.md)
