---
title: Bewakingsoplossingen in Azure Monitor | Microsoft Documenten
description: Monitoringoplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevensverwerving die statistieken bieden die zijn gedraaid rond een bepaald probleemgebied.  In dit artikel vindt u informatie over het installeren en gebruiken van bewakingsoplossingen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275202"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Bewakingsoplossingen in Azure Monitor
Monitoringoplossingen maken gebruik van services in Azure om extra inzicht te bieden in de werking van een bepaalde toepassing of service. In dit artikel vindt u een kort overzicht van bewakingsoplossingen in Azure en details over het gebruik en de installatie ervan.

> [!NOTE]
> Monitoringoplossingen werden voorheen managementoplossingen genoemd.

Monitoringoplossingen verzamelen doorgaans logboekgegevens en bieden query's en weergaven om verzamelde gegevens te analyseren. Ze kunnen ook andere services zoals Azure Automation gebruiken om acties uit te voeren met betrekking tot de toepassing of service.

U bewakingsoplossingen toevoegen aan Azure Monitor voor alle toepassingen en services die u gebruikt. Ze zijn meestal gratis beschikbaar, maar verzamelen gegevens die gebruikskosten kunnen inroepen. Naast oplossingen van Microsoft kunnen partners en klanten [beheeroplossingen maken](solutions-creating.md) die in hun eigen omgeving kunnen worden gebruikt of beschikbaar worden gesteld aan klanten via de community.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Bewakingsoplossingen gebruiken
Open de **pagina Overzicht** in Azure Monitor om een tegel weer te geven voor elke oplossing die in de werkruimte is geïnstalleerd. 

1. Ga naar de [Azure-portal.](https://ms.portal.azure.com) Zoeken naar en selecteer **Monitor**.
1. Selecteer **meer**in het menu **Insights** .
1. Gebruik de vervolgkeuzevakken boven aan het scherm om de werkruimte of het tijdsbereik dat voor de tegels wordt gebruikt, te wijzigen.
1. Klik op de tegel voor een oplossing om de weergave te openen die meer gedetailleerde analyse bevat van de verzamelde gegevens.

![Overzicht](media/solutions/overview.png)

Bewakingsoplossingen kunnen meerdere typen Azure-bronnen bevatten en u alle bronnen bekijken die bij een oplossing zijn opgenomen, net als elke andere bron. Logquery's die in de oplossing zijn opgenomen, worden bijvoorbeeld weergegeven onder **Oplossingsquery's** in [Query explorer](../log-query/get-started-portal.md#load-queries) U deze query's gebruiken bij het uitvoeren van ad-hocanalyse met [logboekquery's.](../log-query/log-query-overview.md)

## <a name="list-installed-monitoring-solutions"></a>Geïnstalleerde bewakingsoplossingen weergeven 
Gebruik de volgende procedure om de bewakingsoplossingen aan te geven die in uw abonnement zijn geïnstalleerd.

1. Ga naar de [Azure-portal.](https://ms.portal.azure.com) Zoeken naar en selecteer **Oplossingen**.
1. Oplossingen die in al uw werkruimten zijn geïnstalleerd, worden weergegeven. De naam van de oplossing wordt gevolgd door de naam van de werkruimte waarin de werkruimte is geïnstalleerd.
1. Gebruik de vervolgkeuzevakken boven aan het scherm om te filteren op abonnements- of resourcegroep.


![Alle oplossingen weergeven](media/solutions/list-solutions-all.png)

Klik op de naam van een oplossing om de overzichtspagina te openen. Deze pagina toont alle weergaven in de oplossing en biedt verschillende opties voor de oplossing zelf en de werkruimte. Bekijk de overzichtspagina voor een oplossing door een van de bovenstaande procedures te gebruiken om oplossingen op te sommen en vervolgens op de naam van de oplossing te klikken.

![Eigenschappen van oplossing](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Een bewakingsoplossing installeren
Monitoringoplossingen van Microsoft en partners zijn beschikbaar via de [Azure Marketplace.](https://azuremarketplace.microsoft.com) U de beschikbare oplossingen doorzoeken en installeren via de volgende procedure. Wanneer u een oplossing installeert, moet u een [Log Analytics-werkruimte](../platform/manage-access.md) selecteren waar de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.

1. Klik in de [lijst met oplossingen voor uw abonnement](#list-installed-monitoring-solutions)op **Toevoegen**.
1. Blader of zoek naar een oplossing. U ook zoeken naar oplossingen via [deze zoeklink.](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)
1. Zoek de gewenste bewakingsoplossing en lees de beschrijving ervan door.
1. Klik **op Maken** om het installatieproces te starten.
1. Wanneer het installatieproces wordt gestart, wordt u gevraagd de werkruimte Log Analytics op te geven en de vereiste configuratie voor de oplossing te bieden.

![Een oplossing installeren](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Een oplossing van de community installeren
Leden van de community kunnen beheeroplossingen indienen bij Azure Quickstart-sjablonen. U deze oplossingen direct installeren of sjablonen downloaden voor latere installatie.

1. Volg het proces dat is beschreven in [het log analytics-werk- en automatiseringsaccount](#log-analytics-workspace-and-automation-account) om een werkruimte en account te koppelen.
2. Ga naar [Azure Quickstart-sjablonen](https://azure.microsoft.com/documentation/templates/). 
3. Zoek naar een oplossing waarin u geïnteresseerd bent.
4. Selecteer de oplossing in de resultaten om de details te bekijken.
5. Klik op de knop **Implementeren naar Azure.**
6. U wordt gevraagd om informatie te verstrekken, zoals de resourcegroep en locatie, naast waarden voor alle parameters in de oplossing.
7. Klik **op Kopen** om de oplossing te installeren.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werkruimte en automatiseringsaccount
Voor alle bewakingsoplossingen is een [Log Analytics-werkruimte](../platform/manage-access.md) vereist om gegevens die door de oplossing zijn verzameld, op te slaan en de logboekzoekopdrachten en -weergaven te hosten. Voor sommige oplossingen is ook een [Automation-account](../../automation/automation-security-overview.md#automation-account-overview) vereist dat runbooks en gerelateerde bronnen bevatten. De werkruimte en het account moeten aan de volgende vereisten voldoen.

* Elke installatie van een oplossing kan slechts één Log Analytics-werkruimte en één automatiseringsaccount gebruiken. U de oplossing afzonderlijk in meerdere werkruimten installeren.
* Als een oplossing een Automatiseringsaccount vereist, moeten de werkruimte Log Analytics en het automatiseringsaccount aan elkaar worden gekoppeld. Een Log Analytics-werkruimte mag slechts aan één Automatiseringsaccount worden gekoppeld en een Automatiseringsaccount mag slechts aan één Log Analytics-werkruimte worden gekoppeld.
* Om te worden gekoppeld, moeten de werkruimte Log Analytics en het automatiseringsaccount zich in dezelfde resourcegroep en -regio bevinden. De uitzondering is een werkruimte in de regio Oost-VS en automatiseringsaccount in Oost-VS 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Een koppeling maken tussen een Log Analytics-werkruimte en een automatiseringsaccount
Hoe u het log analytics-werkruimte en automatiseringsaccount opgeeft, is afhankelijk van de installatiemethode voor uw oplossing.

* Wanneer u een oplossing installeert via de Azure Marketplace, wordt u gevraagd om een werkruimte- en automatiseringsaccount. De koppeling tussen hen wordt gemaakt als ze nog niet zijn gekoppeld.
* Voor oplossingen buiten de Azure Marketplace moet u het log analytics-werkruimte- en automatiseringsaccount koppelen voordat u de oplossing installeert. U dit doen door elke oplossing in de Azure Marketplace te selecteren en het log Analytics-werkruimte- en automatiseringsaccount te selecteren. U hoeft de oplossing niet daadwerkelijk te installeren omdat de koppeling wordt gemaakt zodra de werkruimte log-analyse en het automatiseringsaccount zijn geselecteerd. Zodra de koppeling is gemaakt, u dat Log Analytics-werkruimte en automatiseringsaccount gebruiken voor elke oplossing.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>De koppeling tussen een Log Analytics-werkruimte en een automatiseringsaccount verifiëren
U de koppeling tussen een log-analysewerkruimte en een automatiseringsaccount verifiëren via de volgende procedure.

1. Selecteer het automatiseringsaccount in de Azure-portal.
1. Schuif naar de sectie **Gerelateerde bronnen** van het menu.
1. Als de **instelling Werkruimte** is ingeschakeld, is dit account gekoppeld aan een werkruimte log analytics. U op **Werkruimte** klikken om de details van de werkruimte weer te geven.

## <a name="remove-a-monitoring-solution"></a>Een bewakingsoplossing verwijderen
Als u een geïnstalleerde oplossing wilt verwijderen, zoekt u deze in de [lijst met geïnstalleerde oplossingen.](#list-installed-monitoring-solutions) Klik op de naam van de oplossing om de overzichtspagina te openen en klik vervolgens op **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen
* Ontvang een [lijst met bewakingsoplossingen van Microsoft.](solutions-inventory.md)
* Meer informatie over het [maken van query's](../log-query/log-query-overview.md) om gegevens te analyseren die worden verzameld door oplossingen te bewaken.

