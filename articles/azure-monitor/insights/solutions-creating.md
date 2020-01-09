---
title: Een beheer oplossing bouwen in azure | Microsoft Docs
description: Beheer oplossingen bevatten ingebouwde beheer scenario's in azure die klanten aan hun Log Analytics-werk ruimte kunnen toevoegen.  In dit artikel vindt u informatie over het maken van beheer oplossingen voor gebruik in uw eigen omgeving of voor uw klanten beschikbaar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c08dc5091e7ab0eec5d4e6dd455e6adb3caa35f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75402051"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Een beheer oplossing ontwerpen en bouwen in azure (preview)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheer oplossingen in azure die momenteel als preview-versie beschikbaar zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.

[Beheer oplossingen]( solutions.md) bieden verpakte beheer scenario's die klanten aan hun log Analytics-werk ruimte kunnen toevoegen.  Dit artikel bevat een basis proces voor het ontwerpen en bouwen van een beheer oplossing die geschikt is voor de meest voorkomende vereisten.  Als u geen ervaring hebt met het bouwen van beheer oplossingen, kunt u dit proces gebruiken als uitgangs punt en vervolgens de concepten voor complexere oplossingen benutten wanneer uw vereisten worden ontwikkeld.

## <a name="what-is-a-management-solution"></a>Wat is een beheer oplossing?

Beheer oplossingen bevatten Azure-resources die samen werken om een bepaald beheer scenario te krijgen.  Ze worden geïmplementeerd als [resource beheer sjablonen](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) die details bevatten over het installeren en configureren van de opgenomen resources wanneer de oplossing is geïnstalleerd.

De basis strategie bestaat uit het starten van de beheer oplossing door de afzonderlijke onderdelen in uw Azure-omgeving te bouwen.  Zodra u de functionaliteit hebt werken, kunt u deze in een bestand met een [beheer oplossing]( solutions-solution-file.md)inpakken. 


## <a name="design-your-solution"></a>Ontwerp uw oplossing
In het volgende diagram ziet u het meest voorkomende patroon voor een beheer oplossing.  De verschillende onderdelen in dit patroon worden hieronder beschreven.

![Overzicht van beheer oplossingen](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Gegevensbronnen
De eerste stap bij het ontwerpen van een oplossing is het bepalen van de gegevens die u nodig hebt uit de Log Analytics opslag plaats.  Deze gegevens kunnen worden verzameld door een [gegevens bron](../../azure-monitor/platform/agent-data-sources.md) of [een andere oplossing]( solutions.md), of uw oplossing moet mogelijk het proces leveren om het te kunnen verzamelen.

Er zijn een aantal manieren om gegevens bronnen te verzamelen die kunnen worden verzameld in de Log Analytics-opslag plaats, zoals beschreven in [gegevens bronnen in log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Dit omvat gebeurtenissen in het Windows-gebeurtenis logboek of gegenereerd door syslog naast de prestatie meter items voor Windows-en Linux-clients.  U kunt ook gegevens verzamelen van Azure-resources die worden verzameld door Azure Monitor.  

Als u gegevens nodig hebt die niet toegankelijk zijn via een van de beschik bare gegevens bronnen, kunt u de [http data collector API](../../azure-monitor/platform/data-collector-api.md) gebruiken, waarmee u gegevens kunt schrijven naar de log Analytics opslagplaats vanaf elke client die een rest API kan aanroepen.  De meest voorkomende methode voor het verzamelen van aangepaste gegevens in een beheer oplossing is het maken van een [runbook in azure Automation](../../automation/automation-runbook-types.md) waarmee de vereiste gegevens van Azure of externe resources worden verzameld en de Data Collector-API wordt gebruikt om naar de opslag plaats te schrijven.  

### <a name="log-searches"></a>Zoek opdrachten in Logboeken
[Zoek opdrachten in Logboeken](../../azure-monitor/log-query/log-query-overview.md) worden gebruikt voor het extra heren en analyseren van gegevens in de opslag plaats log Analytics.  Deze worden gebruikt door weer gaven en waarschuwingen, naast de mogelijkheid om de gebruiker ad hoc-analyse van gegevens in de opslag plaats uit te voeren.  

U moet alle query's definiëren waarvan u denkt dat ze nuttig zijn voor de gebruiker, zelfs als ze niet worden gebruikt door een weer gave of waarschuwing.  Deze zijn beschikbaar als opgeslagen Zoek opdrachten in de portal, en u kunt deze ook opnemen in een [lijst met visualisatie onderdelen voor query's](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) in uw aangepaste weer gave.

### <a name="alerts"></a>Waarschuwingen
[Waarschuwingen in log Analytics](../../azure-monitor/platform/alerts-overview.md) problemen identificeren via [Zoek opdrachten](#log-searches) in het logboek voor de gegevens in de opslag plaats.  Ze geven de gebruiker hiervan op de hoogte of voeren automatisch een actie uit als reactie. U moet verschillende waarschuwings voorwaarden voor uw toepassing identificeren en de bijbehorende waarschuwings regels in het oplossings bestand toevoegen.

Als het probleem mogelijk kan worden opgelost met een geautomatiseerd proces, maakt u meestal een runbook in Azure Automation om dit herstel uit te voeren.  De meeste Azure-Services kunnen worden beheerd met [cmdlets](/powershell/azure/overview) die het runbook zou gebruiken om dergelijke functionaliteit uit te voeren.

Als uw oplossing externe functionaliteit vereist in reactie op een waarschuwing, kunt u een [webhook-antwoord](../../azure-monitor/platform/alerts-metric.md)gebruiken.  Hierdoor kunt u een externe webservice aanroepen die informatie van de waarschuwing verzendt.

### <a name="views"></a>Weergaven
Weer gaven in Log Analytics worden gebruikt voor het visualiseren van gegevens uit de Log Analytics opslag plaats.  Elke oplossing bevat doorgaans één weer gave met een [tegel](../../azure-monitor/platform/view-designer-tiles.md) die wordt weer gegeven op het hoofd Dashboard van de gebruiker.  De weer gave kan een wille keurig aantal [visualisatie onderdelen](../../azure-monitor/platform/view-designer-parts.md) bevatten om verschillende visualisaties van de verzamelde gegevens aan de gebruiker te bieden.

U [maakt aangepaste weer gaven met behulp van de weer gave Designer](../../azure-monitor/platform/view-designer.md) , die u later kunt exporteren voor opname in het oplossings bestand.  


## <a name="create-solution-file"></a>Oplossings bestand maken
Zodra u de onderdelen die deel uitmaken van uw oplossing hebt geconfigureerd en getest, kunt u [het oplossings bestand]( solutions-solution-file.md).  U implementeert de oplossings onderdelen in een [Resource Manager-sjabloon](../../azure-resource-manager/templates/template-syntax.md) die een [oplossings resource]( solutions-solution-file.md#solution-resource) bevat met relaties met de andere resources in het bestand.  


## <a name="test-your-solution"></a>Uw oplossing testen
Tijdens het ontwikkelen van uw oplossing moet u deze installeren en testen in uw werk ruimte.  U kunt dit doen met behulp van een van de beschik bare methoden om [Resource Manager-sjablonen te testen en te installeren](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Uw oplossing publiceren
Zodra u uw oplossing hebt voltooid en getest, kunt u deze beschikbaar maken voor klanten via de volgende bronnen.

- **Sjablonen voor Azure Quick**start.  [Sjablonen voor Azure Quick](https://azure.microsoft.com/resources/templates/) start is een set Resource Manager-sjablonen die door de community zijn bijgedragen via github.  U kunt uw oplossing beschikbaar maken door de volgende informatie in de [bijdrage gids](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)te volgen.
- **Azure Marketplace**.  Met [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) kunt u uw oplossing distribueren en verkopen aan andere ontwikkel aars, ISV'S en IT-professionals.  U kunt lezen hoe u uw oplossing publiceert naar Azure Marketplace op [het publiceren en beheren van een aanbieding op de Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van een oplossings bestand]( solutions-solution-file.md) voor uw beheer oplossing.
* Meer informatie over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md).
* Zoek in [Azure Quick](https://azure.microsoft.com/documentation/templates) start-sjablonen naar voor beelden van verschillende Resource Manager-sjablonen.
