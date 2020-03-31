---
title: Een beheeroplossing bouwen in Azure | Microsoft Documenten
description: Beheeroplossingen omvatten pakketbeheerscenario's in Azure die klanten kunnen toevoegen aan hun Log Analytics-werkruimte.  In dit artikel vindt u informatie over hoe u beheeroplossingen maken die in uw eigen omgeving kunnen worden gebruikt of beschikbaar worden gesteld aan uw klanten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663212"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Een beheeroplossing ontwerpen en bouwen in Azure (Voorbeeld)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheeroplossingen in Azure die momenteel in preview zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.

[Beheeroplossingen]( solutions.md) bieden verpakte beheerscenario's die klanten kunnen toevoegen aan hun Log Analytics-werkruimte.  Dit artikel presenteert een basisproces voor het ontwerpen en bouwen van een managementoplossing die geschikt is voor de meest voorkomende vereisten.  Als u nieuw bent in gebouw beheer oplossingen dan u dit proces te gebruiken als uitgangspunt en vervolgens gebruik maken van de concepten voor meer complexe oplossingen als uw eisen evolueren.

## <a name="what-is-a-management-solution"></a>Wat is een managementoplossing?

Beheeroplossingen bevatten Azure-resources die samenwerken om een bepaald beheerscenario te realiseren.  Ze worden geïmplementeerd als [Resource Management-sjablonen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) die details bevatten over het installeren en configureren van hun opgenomen bronnen wanneer de oplossing wordt geïnstalleerd.

De basisstrategie is om uw beheeroplossing te starten door de afzonderlijke componenten in uw Azure-omgeving te bouwen.  Zodra u de functionaliteit goed werkt, u beginnen met het verpakken van hen in een [beheeroplossingbestand.]( solutions-solution-file.md) 


## <a name="design-your-solution"></a>Ontwerp uw oplossing
Het meest voorkomende patroon voor een beheeroplossing wordt weergegeven in het volgende diagram.  De verschillende componenten in dit patroon worden besproken in de hieronder.

![Overzicht van beheeroplossingen](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Gegevensbronnen
De eerste stap bij het ontwerpen van een oplossing is het bepalen van de gegevens die u nodig hebt vanuit de Log Analytics-repository.  Deze gegevens kunnen worden verzameld door een [gegevensbron](../../azure-monitor/platform/agent-data-sources.md) of [een andere oplossing,]( solutions.md)of uw oplossing moet mogelijk het proces bieden om deze te verzamelen.

Er zijn een aantal manieren waarop gegevensbronnen kunnen worden verzameld in de Log Analytics-opslagplaats, zoals beschreven in [gegevensbronnen in Log Analytics.](../../azure-monitor/platform/agent-data-sources.md)  Dit omvat gebeurtenissen in het Windows-gebeurtenislogboek of gegenereerd door Syslog naast prestatiemeteritems voor zowel Windows- als Linux-clients.  U ook gegevens verzamelen uit Azure-bronnen die zijn verzameld door Azure Monitor.  

Als u gegevens nodig hebt die niet toegankelijk zijn via een van de beschikbare gegevensbronnen, u de [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) gebruiken waarmee u gegevens schrijven naar de Log Analytics-opslagplaats van elke client die een REST-API kan aanroepen.  De meest voorkomende manier voor het verzamelen van aangepaste gegevens in een beheeroplossing is het maken van een [runbook in Azure Automation](../../automation/automation-runbook-types.md) dat de vereiste gegevens uit Azure of externe bronnen verzamelt en de API voor gegevensverzamelaar gebruikt om naar de opslagplaats te schrijven.  

### <a name="log-searches"></a>Logboekzoekopdrachten
[Logboekzoekopdrachten](../../azure-monitor/log-query/log-query-overview.md) worden gebruikt om gegevens te extraheren en te analyseren in de Log Analytics-opslagplaats.  Ze worden gebruikt door weergaven en waarschuwingen in aanvulling op het toestaan van de gebruiker om ad hoc analyse van gegevens uit te voeren in de repository.  

U moet alle query's definiëren waarvan u denkt dat ze nuttig zijn voor de gebruiker, zelfs als ze niet worden gebruikt door weergaven of waarschuwingen.  Deze zijn voor hen beschikbaar als opgeslagen zoekopdrachten in de portal en u ze ook opnemen in een deel van de [visualisatielijst van query's](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) in uw aangepaste weergave.

### <a name="alerts"></a>Waarschuwingen
[Waarschuwingen in Log Analytics](../../azure-monitor/platform/alerts-overview.md) identificeren problemen door [middel van log zoekopdrachten](#log-searches) tegen de gegevens in de repository.  Ze stellen de gebruiker op de hoogte of voeren automatisch een actie uit als reactie. U moet verschillende waarschuwingsvoorwaarden voor uw toepassing identificeren en overeenkomstige waarschuwingsregels opnemen in uw oplossingsbestand.

Als het probleem mogelijk kan worden verholpen met een geautomatiseerd proces, maakt u meestal een runbook in Azure Automation om deze herstelprocedure uit te voeren.  De meeste Azure-services kunnen worden beheerd met [cmdlets](/powershell/azure/overview) die de runbook zou gebruiken om dergelijke functionaliteit uit te voeren.

Als uw oplossing externe functionaliteit vereist in reactie op een waarschuwing, u een [webhook-reactie](../../azure-monitor/platform/alerts-metric.md)gebruiken.  Hiermee u een externe webservice bellen die informatie van de waarschuwing verzendt.

### <a name="views"></a>Weergaven
Weergaven in Log Analytics worden gebruikt om gegevens uit de Log Analytics-repository te visualiseren.  Elke oplossing bevat doorgaans één weergave met een [tegel](../../azure-monitor/platform/view-designer-tiles.md) die wordt weergegeven op het hoofddashboard van de gebruiker.  De weergave kan een willekeurig aantal [visualisatieonderdelen](../../azure-monitor/platform/view-designer-parts.md) bevatten om verschillende visualisaties van de verzamelde gegevens aan de gebruiker te bieden.

U [maakt aangepaste weergaven met de View Designer](../../azure-monitor/platform/view-designer.md) die u later exporteren om in uw oplossingsbestand te worden opgenomen.  


## <a name="create-solution-file"></a>Oplossingsbestand maken
Zodra u de onderdelen hebt geconfigureerd en getest die deel uitmaken van uw oplossing, u [uw oplossingsbestand maken.]( solutions-solution-file.md)  U implementeert de oplossingscomponenten in een [resourcemanagersjabloon](../../azure-resource-manager/templates/template-syntax.md) met een [oplossingsbron]( solutions-solution-file.md#solution-resource) met relaties met de andere bronnen in het bestand.  


## <a name="test-your-solution"></a>Test uw oplossing
Terwijl u uw oplossing ontwikkelt, moet u deze installeren en testen in uw werkruimte.  U dit doen met behulp van een van de beschikbare methoden om [Resource Manager-sjablonen](../../azure-resource-manager/templates/deploy-powershell.md)te testen en te installeren.

## <a name="publish-your-solution"></a>Uw oplossing publiceren
Zodra u uw oplossing hebt voltooid en getest, u deze beschikbaar maken voor klanten via de volgende bronnen.

- **Azure Quickstart-sjablonen**.  [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) zijn een set Resource Manager-sjablonen die door de community zijn bijgedragen via GitHub.  U uw oplossing beschikbaar stellen door informatie te volgen in de [bijdragegids.](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)
- **Azure Marketplace**.  Met [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) u uw oplossing distribueren en verkopen aan andere ontwikkelaars, ISV's en IT-professionals.  U meer informatie vinden over het publiceren van uw oplossing op Azure Marketplace bij [Het publiceren en beheren van een aanbieding in de Azure Marketplace.](../../marketplace/marketplace-publishers-guide.md)



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van een oplossingsbestand]( solutions-solution-file.md) voor uw beheeroplossing.
* Meer informatie over [azure resource manager-sjablonen voor ontwerpen](../../azure-resource-manager/templates/template-syntax.md).
* Zoeken in [Azure Quickstart-sjablonen](https://azure.microsoft.com/documentation/templates) voor voorbeelden van verschillende Resource Manager-sjablonen.
