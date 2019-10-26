---
title: Bewakings oplossingen in Azure Monitor | Microsoft Docs
description: Bewakings oplossingen in Azure Monitor zijn een verzameling regels voor logica, visualisatie en gegevens aanschaf die gegevens over een bepaald probleem gebied hebben gedraaid.  Dit artikel bevat informatie over het installeren en gebruiken van bewakings oplossingen.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 4006a3c66da9cb98db16b72da0b5fb7059affe22
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900662"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Bewakings oplossingen in Azure Monitor
Bewakings oplossingen maken gebruik van services in azure om meer inzicht te krijgen in de werking van een bepaalde toepassing of service. Dit artikel bevat een kort overzicht van de bewakings oplossingen in Azure en informatie over het gebruik en de installatie ervan.

> [!NOTE]
> Bewakings oplossingen werden voorheen beheer oplossingen genoemd.

Bewakings oplossingen verzamelen doorgaans logboek gegevens en bieden query's en weer gaven om verzamelde gegevens te analyseren. Ze kunnen ook andere services gebruiken, zoals Azure Automation om acties uit te voeren die betrekking hebben op de toepassing of service.

U kunt bewakings oplossingen toevoegen aan Azure Monitor voor alle toepassingen en services die u gebruikt. Ze zijn doorgaans gratis beschikbaar, maar verzamelen gegevens die gebruiks kosten kunnen aanroepen. Naast de oplossingen die door micro soft worden geleverd, kunnen partners en klanten [beheer oplossingen maken](solutions-creating.md) voor gebruik in hun eigen omgeving of worden ze beschikbaar gesteld aan klanten via de community.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Bewakingsoplossingen gebruiken
Open de pagina **overzicht** in azure monitor om een tegel weer te geven voor elke oplossing die in de werk ruimte is geïnstalleerd. 

1. Meld u aan bij Azure Portal.
1. Open **alle services** en zoek naar **monitor**.
1. Selecteer in het menu **inzichten** **meer**.
1. Gebruik de vervolg keuzelijsten aan de bovenkant van het scherm om de werk ruimte of het tijds bereik te wijzigen dat wordt gebruikt voor de tegels.
1. Klik op de tegel voor een oplossing om de weer gave ervan te openen, met meer gedetailleerde analyse van de verzamelde gegevens.

![Overzicht](media/solutions/overview.png)

Bewakings oplossingen kunnen meerdere typen Azure-resources bevatten en u kunt alle resources die zijn opgenomen in een oplossing, net als elke andere resource bekijken. Alle logboek query's die in de oplossing zijn opgenomen, worden bijvoorbeeld vermeld onder **oplossingen query's** in [query Explorer](../log-query/get-started-portal.md#load-queries) . u kunt deze query's gebruiken bij het uitvoeren van ad hoc analyses met [logboek query's](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Geïnstalleerde bewakings oplossingen weer geven 
Gebruik de volgende procedure om de bewakings oplossingen weer te geven die in uw abonnement zijn geïnstalleerd.

1. Meld u aan bij Azure Portal.
1. Open **alle services** en zoek **oplossingen**.
4. Oplossingen die in al uw werk ruimten zijn geïnstalleerd, worden weer gegeven. De naam van de oplossing wordt gevolgd door de naam van de werk ruimte waarin deze is geïnstalleerd.
1. Gebruik de vervolg keuzelijsten aan de bovenkant van het scherm om te filteren op abonnement of resource groep.


![Alle oplossingen weer geven](media/solutions/list-solutions-all.png)

Klik op de naam van een oplossing om de bijbehorende pagina samen vatting te openen. Op deze pagina ziet u alle weer gaven die zijn opgenomen in de oplossing en worden verschillende opties geboden voor de oplossing zelf en de bijbehorende werk ruimte. Bekijk de overzichts pagina voor een oplossing met behulp van een van de bovenstaande procedures om oplossingen weer te geven en klik vervolgens op de naam van de oplossing.

![Eigenschappen van oplossing](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Een bewakings oplossing installeren
Bewakings oplossingen van micro soft en partners zijn beschikbaar via [Azure Marketplace](https://azuremarketplace.microsoft.com). U kunt zoeken naar beschik bare oplossingen en deze installeren met behulp van de volgende procedure. Wanneer u een oplossing installeert, moet u een [log Analytics-werk ruimte](../platform/manage-access.md) selecteren waarin de oplossing wordt geïnstalleerd en waar de gegevens worden verzameld.

1. Klik in de [lijst met oplossingen voor uw abonnement](#list-installed-monitoring-solutions)op **toevoegen**.
1. Blader of zoek naar een oplossing. U kunt ook naar oplossingen bladeren via [deze zoek koppeling](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Zoek de gewenste bewakings oplossing en lees de beschrijving.
1. Klik op **maken** om het installatie proces te starten.
1. Wanneer het installatie proces wordt gestart, wordt u gevraagd om de Log Analytics-werk ruimte op te geven en de vereiste configuratie voor de oplossing op te geven.

![Een oplossing installeren](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Een oplossing installeren vanuit de Community
Leden van de community kunnen beheer oplossingen verzenden naar Azure Quick Start-sjablonen. U kunt deze oplossingen direct installeren of de sjablonen downloaden voor een latere installatie.

1. Volg het proces dat wordt beschreven in [log Analytics werk ruimte en het Automation-account](#log-analytics-workspace-and-automation-account) om een werk ruimte en een account te koppelen.
2. Ga naar de [Azure Quick](https://azure.microsoft.com/documentation/templates/)start-sjablonen. 
3. Zoek naar een oplossing waarin u bent geïnteresseerd.
4. Selecteer de oplossing in de resultaten om de details ervan weer te geven.
5. Klik op de knop **implementeren naar Azure** .
6. U wordt gevraagd om informatie op te geven, zoals de resource groep en de locatie, naast de waarden voor eventuele para meters in de oplossing.
7. Klik op **kopen** om de oplossing te installeren.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werk ruimte en Automation-account
Voor alle bewakings oplossingen is een [log Analytics-werk ruimte](../platform/manage-access.md) vereist voor het opslaan van gegevens die zijn verzameld door de oplossing en het vinden van zoek opdrachten in Logboeken en weer gaven. Sommige oplossingen hebben ook een [Automation-account](../../automation/automation-security-overview.md#automation-account-overview) nodig om runbooks en gerelateerde resources te bevatten. De werk ruimte en het account moeten voldoen aan de volgende vereisten.

* Bij elke installatie van een oplossing kan slechts één Log Analytics-werk ruimte en één Automation-account worden gebruikt. U kunt de oplossing afzonderlijk in meerdere werk ruimten installeren.
* Als voor een oplossing een Automation-account is vereist, moet de Log Analytics-werk ruimte en het Automation-account aan elkaar zijn gekoppeld. Een Log Analytics-werk ruimte kan alleen worden gekoppeld aan een Automation-account en een Automation-account kan alleen worden gekoppeld aan een Log Analytics-werk ruimte.
* Als u een koppeling wilt maken, moeten de Log Analytics-werk ruimte en het Automation-account zich in dezelfde resource groep en regio bevinden. De uitzonde ring is een werk ruimte in de regio VS-Oost en Automation-account in VS-Oost 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Een koppeling maken tussen een Log Analytics-werk ruimte en een Automation-account
Hoe u de Log Analytics-werk ruimte en het Automation-account opgeeft, is afhankelijk van de installatie methode voor uw oplossing.

* Wanneer u een oplossing installeert via de Azure Marketplace, wordt u gevraagd naar een werk ruimte en een Automation-account. De koppeling tussen deze wordt gemaakt als ze nog niet zijn gekoppeld.
* Voor oplossingen buiten Azure Marketplace moet u de Log Analytics-werk ruimte en het Automation-account koppelen voordat u de oplossing installeert. U kunt dit doen door een wille keurige oplossing te selecteren in azure Marketplace en de Log Analytics-werk ruimte en het Automation-account te selecteren. U hoeft de oplossing niet echt te installeren omdat de koppeling is gemaakt zodra de Log Analytics-werk ruimte en het Automation-account zijn geselecteerd. Zodra de koppeling is gemaakt, kunt u die Log Analytics werk ruimte en het Automation-account voor elke oplossing gebruiken.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>De koppeling tussen een Log Analytics-werk ruimte en een Automation-account controleren
U kunt de koppeling tussen een Log Analytics-werk ruimte en een Automation-account controleren aan de hand van de volgende procedure.

1. Selecteer het Automation-account in de Azure Portal.
1. Ga naar de sectie **Verwante resources** van het menu.
1. Als de **werk ruimte** instelling is ingeschakeld, wordt dit account gekoppeld aan een log Analytics-werk ruimte. U kunt klikken op de **werk ruimte** om de details van de werk ruimte weer te geven.

## <a name="remove-a-monitoring-solution"></a>Een bewakings oplossing verwijderen
Als u een geïnstalleerde oplossing wilt verwijderen, zoekt u deze in de [lijst met geïnstalleerde oplossingen](#list-installed-monitoring-solutions). Klik op de naam van de oplossing om de bijbehorende overzichts pagina te openen en klik vervolgens op **verwijderen**.


## <a name="next-steps"></a>Volgende stappen
* Een [lijst met bewakings oplossingen van micro soft](solutions-inventory.md)ophalen.
* Meer informatie over het [maken van query's](../log-query/log-query-overview.md) voor het analyseren van gegevens die zijn verzameld door bewakings oplossingen.

