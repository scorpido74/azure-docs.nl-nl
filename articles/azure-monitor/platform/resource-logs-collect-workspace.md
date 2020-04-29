---
title: Azure-resource logboeken verzamelen in Log Analytics werk ruimte
description: Meer informatie over het streamen van Azure-resource logboeken naar een Log Analytics-werk ruimte in Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248591"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-platform logboeken verzamelen in Log Analytics werk ruimte in Azure Monitor
[Platform logboeken](platform-logs-overview.md) in azure, inclusief Azure-activiteiten logboek en resource logboeken, bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. In dit artikel wordt beschreven hoe u bron logboeken verzamelt in een Log Analytics-werk ruimte, waarmee u het kunt analyseren met andere bewakings gegevens die zijn verzameld in Azure Monitor logboeken met behulp van krachtige logboek query's en ook om gebruik te maken van andere Azure Monitor functies, zoals waarschuwingen en visualisaties. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Wat u kunt doen met platform Logboeken in een werk ruimte
Door platform logboeken te verzamelen in een Log Analytics-werk ruimte kunt u de logboeken van al uw Azure-resources samen analyseren en profiteren van alle functies die beschikbaar zijn voor [Azure monitor logboeken](data-platform-logs.md) . Dit omvat het volgende:

* **Logboek query's** : Maak [logboek query's](../log-query/log-query-overview.md) met behulp van een krachtige query taal om snel inzicht te krijgen in uw diagnostische gegevens en deze te analyseren met gegevens die zijn verzameld uit andere bronnen in azure monitor.
* **Waarschuwing** : Ontvang proactieve meldingen over kritieke voor waarden en patronen die in uw bron logboeken zijn geïdentificeerd met behulp [van logboek waarschuwingen in azure monitor](alerts-log.md).
* **Visualisaties** : de resultaten van een logboek query vastmaken aan een Azure-dash board of opnemen in een werkmap als onderdeel van een interactief rapport.

## <a name="prerequisites"></a>Vereisten
U moet [een nieuwe werk ruimte maken](../learn/quick-create-workspace.md) als u er nog geen hebt. De werk ruimte hoeft zich niet in hetzelfde abonnement te bevinden als de resource waarmee logboeken worden verzonden zolang de gebruiker die de instelling configureert de juiste RBAC-toegang heeft tot beide abonnementen.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Platform logboeken verzenden naar een Log Analytics-werk ruimte en andere bestemmingen door een diagnostische instelling te maken voor een Azure-resource. Zie [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md) voor meer informatie.


## <a name="activity-log-collection"></a>Activiteiten logboek verzameling
U kunt het activiteiten logboek vanuit elk abonnement verzenden naar Maxi maal vijf Log Analytics-werk ruimten. Bron logboek gegevens die zijn verzameld in een Log Analytics werk ruimte worden opgeslagen in de tabel **AzureActivity** . 

## <a name="resource-log-collection-mode"></a>Modus bron logboek verzameling
Bron logboek gegevens die in een Log Analytics-werk ruimte worden verzameld, worden opgeslagen in tabellen zoals beschreven in de [structuur van Azure monitor logboeken](../log-query/logs-structure.md). De tabellen die door resource logboeken worden gebruikt, zijn afhankelijk van het type verzameling dat de resource gebruikt:

- Azure Diagnostics-alle gegevens die zijn geschreven, worden naar de tabel _AzureDiagnostics_ .
- Resource-specifiek: gegevens worden naar afzonderlijke tabellen geschreven voor elke categorie van de resource.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics modus 
In deze modus worden alle gegevens van een [Diagnostische instelling](diagnostic-settings.md) verzameld in de tabel _AzureDiagnostics_ . Dit is de verouderde methode die momenteel door de meeste Azure-Services wordt gebruikt.

Aangezien meerdere bron typen gegevens naar dezelfde tabel verzenden, is het bijbehorende schema de hoofd verzameling van de schema's van alle verschillende gegevens typen die worden verzameld.

Bekijk het volgende voor beeld waarin Diagnostische instellingen worden verzameld in dezelfde werk ruimte voor de volgende gegevens typen:

- Audit logboeken van service 1 (met een schema dat bestaat uit de kolommen A, B en C)  
- Fout logboeken van service 1 (met een schema dat bestaat uit kolommen D, E en F)  
- Audit logboeken van Service 2 (met een schema dat bestaat uit de kolommen G, H en I)  

De tabel AzureDiagnostics ziet er als volgt uit:  

| ResourceProvider    | Categorie     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Micro soft. Service1 | Audit logs bevat    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Micro soft. Service1 | ErrorLogs    |    |    |    | eerste | W1 | E1 |    |    |    |
| Micro soft. Service2 | Audit logs bevat    |    |    |    |    |    |    | j1 | K1 | L1 |
| Micro soft. Service1 | ErrorLogs    |    |    |    | inclusief | Box | E2 |    |    |    |
| Micro soft. Service2 | Audit logs bevat    |    |    |    |    |    |    | j3 | k3 | L3 |
| Micro soft. Service1 | Audit logs bevat    | x5 | y5 waardeveld | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Resource-specifiek
In deze modus worden afzonderlijke tabellen in de geselecteerde werk ruimte gemaakt voor elke categorie die in de diagnostische instelling is geselecteerd. Deze methode wordt aanbevolen omdat het veel gemakkelijker is om met de gegevens in logboek query's te werken, een betere detectie van schema's en hun structuur te bieden, de prestaties te verbeteren voor zowel opname latentie als query tijden, en de mogelijkheid om RBAC-rechten te verlenen aan een specifieke tabel. Alle Azure-Services worden uiteindelijk naar de resource-specifieke modus gemigreerd. 

Het bovenstaande voor beeld resulteert in het maken van drie tabellen:
 
- Tabel *Service1AuditLogs* als volgt:

    | Resourceprovider | Categorie | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | Audit logs bevat | x1 | Y1 | z1 |
    | Service1 | Audit logs bevat | x5 | y5 waardeveld | z5 |
    | ... |

- Tabel *Service1ErrorLogs* als volgt:  

    | Resourceprovider | Categorie | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  eerste | W1 | E1 |
    | Service1 | ErrorLogs |  inclusief | Box | E2 |
    | ... |

- Tabel *Service2AuditLogs* als volgt:  

    | Resourceprovider | Categorie | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | Audit logs bevat | j1 | K1 | L1|
    | Service2 | Audit logs bevat | j3 | k3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>De verzamelings modus selecteren
Met de meeste Azure-resources worden gegevens naar de werk ruimte geschreven in **Azure Diagnostic** of de **resource-specifieke modus** zonder dat u daarvoor een keuze hoeft te doen. Zie de [documentatie voor elke service](diagnostic-logs-schema.md) voor meer informatie over de modus die wordt gebruikt. Alle Azure-Services zullen uiteindelijk gebruikmaken van de resource-specifieke modus. Als onderdeel van deze overgang kunt u met sommige resources een modus selecteren in de diagnostische instelling. U moet de resource-specifieke modus voor nieuwe diagnostische instellingen opgeven, omdat hierdoor de gegevens eenvoudiger te beheren zijn en u mogelijk complexe migraties op een later tijdstip kunt voor komen.
  
   ![Selector voor de modus Diagnostische instellingen](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Momenteel kunnen **Azure Diagnostics** en de **resource-specifieke** modus alleen worden geselecteerd bij het configureren van de diagnostische instelling in de Azure Portal. Als u de instelling configureert met CLI, Power shell of rest API, wordt **Azure Diagnostic**standaard gebruikt.

U kunt een bestaande diagnostische instelling wijzigen in de resource-specifieke modus. In dit geval blijven de gegevens die al zijn verzameld in de tabel _AzureDiagnostics_ totdat deze is verwijderd volgens de Bewaar instelling voor de werk ruimte. Nieuwe gegevens worden verzameld in in de toegewezen tabel. Gebruik de operator [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) om gegevens op te vragen over beide tabellen.

Ga door met het bekijken van [Azure updates](https://azure.microsoft.com/updates/) blog voor aankondigingen over Azure-Services die ondersteuning bieden voor de resource-specifieke modus.

### <a name="column-limit-in-azurediagnostics"></a>Kolom limiet in AzureDiagnostics
Er is een limiet van 500 eigenschappen voor een tabel in Azure Monitor Logboeken. Zodra deze limiet is bereikt, worden alle rijen met gegevens die zich buiten de eerste 500 bevinden, verwijderd op de opname tijd. De *AzureDiagnostics* -tabel is met name vatbaar voor deze limiet omdat deze eigenschappen bevat voor alle Azure-Services die ernaar schrijven.

Als u bron logboeken verzamelt van meerdere services, overschrijden _AzureDiagnostics_ mogelijk deze limiet en worden de gegevens niet geregistreerd. Totdat alle Azure-Services ondersteuning bieden voor de resource-specifieke modus, moet u bronnen zo configureren dat deze naar meerdere werk ruimten schrijven om de kans te verkleinen dat de limiet van 500 kolommen wordt bereikt.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, vanwege een zeer gedetailleerde set logboeken, is een service waarvan bekend is dat ze een groot aantal kolommen schrijven, waardoor het mogelijk is dat _AzureDiagnostics_ de limiet overschrijdt. Voor alle diagnostische instellingen die zijn geconfigureerd voordat de resource-specifieke modus werd ingeschakeld, wordt er een nieuwe kolom gemaakt voor elke unieke benoemde gebruikers parameter voor elke activiteit. Er worden meer kolommen gemaakt vanwege de uitgebreide aard van de activiteit invoer en uitvoer.
 
U moet uw logboeken zo snel mogelijk naar de resource-specifieke modus migreren. Als u dit niet onmiddellijk kunt doen, is een tussentijds alternatief het isoleren van Azure Data Factory Logboeken in hun eigen werk ruimte om de kans te verkleinen dat deze logboeken invloed hebben op andere logboek typen die worden verzameld in uw werk ruimten.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over resource logboeken](platform-logs-overview.md).
* [Maak een diagnostische instelling voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md).
