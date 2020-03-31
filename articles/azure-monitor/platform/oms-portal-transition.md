---
title: OMS-portal verhuist naar Azure | Microsoft Documenten
description: De OMS-portal wordt bijzonsondergang met alle functionaliteit die naar de Azure-portal wordt verplaatst. In dit artikel vindt u informatie over deze overgang.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 680cd9b44cc447f9bdea38cb9d04fc661fba9c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659251"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-portal verhuist naar Azure

> [!NOTE]
> Dit artikel is van toepassing op zowel de Azure public cloud als de overheidscloud, tenzij anders vermeld.

**De OMS-portal voor de Azure public cloud is officieel beëindigd. De OMS-portal voor Azure US Government cloud is officieel in gebruik genomen op 15 mei 2019.** We zijn verheugd om naar de Azure-portal te gaan en verwachten dat de overgang eenvoudig zal zijn. Maar we begrijpen dat veranderingen moeilijk zijn en storend kunnen zijn. De rest van dit artikel gaat over de belangrijkste scenario's en de routekaart voor deze overgang.

De Azure-portal is de hub voor alle Azure-services en biedt een uitgebreide beheerervaring met mogelijkheden zoals dashboards voor het vastmaken van resources, intelligent zoeken naar bronnen en het taggen voor resourcebeheer. Om de monitoring- en beheerworkflow te consolideren en te stroomlijnen, zijn we begonnen met het toevoegen van de OMS-portalmogelijkheden aan de Azure-portal. Alle functies van de OMS-portal maken nu deel uit van de Azure-portal. Sommige van de nieuwe functies, zoals Traffic Analytics, zijn namelijk alleen beschikbaar in de Azure-portal. Met de Azure-portal en meer u alles bereiken wat u in de OMS-portal deed. Als u dit nog niet hebt gedaan, moet u vandaag nog beginnen met het gebruik van de Azure-portal!

## <a name="what-is-changing"></a>Wat verandert er? 
De volgende wijzigingen worden aangekondigd met de afschaffing van het OMS-portaal. Elk van deze wijzigingen wordt in de onderstaande secties nader beschreven.

- U alleen nieuwe [werkruimten](#new-workspaces) maken in de Azure-portal.
- De nieuwe ervaring voor waarschuwingsbeheer [vervangt de oplossing voor waarschuwingsbeheer.](#changes-to-alerts)
- [Gebruikerstoegangsbeheer](#user-access-and-role-migration) wordt nu uitgevoerd in de Azure-portal met azure-toegangsbeheer op basis van azure-rollen.
- De [Application Insights Connector is niet langer nodig](#application-insights-connector-and-solution) omdat dezelfde functionaliteit is ingeschakeld via query's met meerdere werkruimtes.
- De [OMS Mobile App](#oms-mobile-app) wordt afgeschaft. 
- De [NSG-oplossing wordt vervangen](#azure-network-security-group-analytics) door verbeterde functionaliteit die beschikbaar is via traffic analytics-oplossing.
- Voor nieuwe verbindingen van System Center Operations Manager naar Log Analytics zijn [bijgewerkte beheerpakketten](#system-center-operations-manager)vereist.
- Zie [Uw OMS-updateimplementaties migreren naar Azure](../../automation/migrate-oms-update-deployments.md) voor meer informatie over wijzigingen in [Updatebeheer.](../../automation/automation-update-management.md)


## <a name="what-should-i-do-now"></a>Wat moet ik nu doen?
Hoewel de meeste functies blijven werken zonder migratie uit te voeren, moet u de volgende taken uitvoeren:

- U moet [uw gebruikersmachtigingen migreren](#user-access-and-role-migration) naar de Azure-portal.
- Zie [Uw OMS-updateimplementaties migreren naar Azure](../../automation/migrate-oms-update-deployments.md) voor meer informatie over de overgang van de oplossing Voor updatebeheer.

Raadpleeg [veelgestelde vragen voor de overgang van OMS-portal naar Azure-portal voor Log Analytics-gebruikers](oms-portal-faq.md) voor informatie over de overgang naar de Azure-portal. 

## <a name="user-access-and-role-migration"></a>Gebruikerstoegang en rolmigratie
Azure portal access management is rijker en krachtiger dan het toegangsbeheer in de OMS Portal. Zie [Het ontwerpen van uw Azure Monitor Logs-werkruimte](design-logs-deployment.md) voor meer informatie over toegangsbeheer in Log Analytics.

> [!NOTE]
> In eerdere versies van dit artikel stond dat de machtigingen automatisch zouden worden geconverteerd van de OMS-portal naar de Azure-portal. Deze automatische conversie is niet langer gepland en u moet de conversie zelf uitvoeren.

Mogelijk hebt u al de juiste toegang in de Azure-portal, in welk geval u geen wijzigingen hoeft aan te brengen. Er zijn een paar gevallen waarin u mogelijk geen geschikte toegang hebt in welk geval uw beheerder u machtigingen moet toewijzen.

- U hebt Alleen-gebruikersmachtigingen in de OMS-portal, maar geen machtigingen in de Azure-portal. 
- U hebt inzendermachtigingen in de OMS-portal, maar alleen Reader-toegang in de Azure-portal.
 
In beide gevallen moet uw beheerder u handmatig de juiste rol toewijzen uit de volgende tabel. We raden u aan deze rol toe te wijzen op resourcegroep of abonnementsniveau.  Voor beide zaken zal binnenkort meer prescriptieve richtlijnen worden gegeven.

| TOESTEMMING voor OMS-portal | Azure-rol |
|:---|:---|
| ReadOnly | Lezer van Log Analytics |
| Inzender | Inzender van Log Analytics |
| Beheerder | Eigenaar | 
 

## <a name="new-workspaces"></a>Nieuwe werkruimten
U geen nieuwe werkruimten meer maken met behulp van de OMS-portal. Volg de richtlijnen in [Een Logboekanalyse-werkruimte maken in de Azure-portal](../learn/quick-create-workspace.md) om een nieuwe werkruimte in de Azure-portal te maken.

## <a name="changes-to-alerts"></a>Wijzigingen in waarschuwingen

### <a name="alert-extension"></a>Waarschuwingsextensie  

Waarschuwingen zijn [uitgebreid naar de Azure-portal](alerts-extend.md) Bestaande waarschuwingen worden nog steeds weergegeven in de OMS-portal, maar u ze alleen beheren in Azure-portal. Als u waarschuwingen programmatisch gebruikt met behulp van de Log Analytics Alert REST API of Log Analytics Alert Resource Template, moet u actiegroepen gebruiken in plaats van acties in uw API-aanroepen, Azure Resource Manager-sjablonen en PowerShell-opdrachten.

### <a name="alert-management-solution"></a>Oplossing voor waarschuwingsbeheer
Als een wijziging ten opzichte van een eerdere aankondiging blijft de [oplossing voor waarschuwingsbeheer](alert-management-solution.md) beschikbaar en volledig ondersteund in de Azure-portal. U de oplossing blijven installeren vanuit Azure Marketplace.

Hoewel de oplossing voor waarschuwingsbeheer beschikbaar blijft, raden we u aan de [uniforme waarschuwingsinterface van Azure Monitor](alerts-overview.md) te gebruiken om alle waarschuwingen in Azure te visualiseren en te beheren. Deze nieuwe ervaring verzamelt native waarschuwingen van meerdere bronnen binnen Azure, waaronder logboekwaarschuwingen van Log Analytics. Als u de uniforme waarschuwingsinterface van Azure Monitor gebruikt, is de oplossing voor waarschuwingsbeheer alleen vereist om de integratie van waarschuwingen van System Center Operation Manager naar Azure mogelijk te maken. In de uniforme waarschuwingsinterface van Azure Monitor u distributies van uw waarschuwingen bekijken, profiteren van geautomatiseerde groepering van gerelateerde waarschuwingen via slimme groepen en waarschuwingen bekijken over meerdere abonnementen terwijl u uitgebreide filters toepast. Toekomstige ontwikkelingen op het gebied van waarschuwingsbeheer zullen voornamelijk beschikbaar zijn vanuit deze nieuwe ervaring. 

De gegevens die worden verzameld door de oplossing voor waarschuwingsbeheer (records met een type waarschuwing) blijven in Log Analytics zolang de oplossing voor de werkruimte is geïnstalleerd. 

## <a name="oms-mobile-app"></a>OMS mobiele app
De OMS mobiele app wordt samen met de OMS portal gezonsondergang. In plaats van de mobiele APP OMS, om toegang te krijgen tot informatie over uw IT-infrastructuur, dashboards en opgeslagen query's, hebt u rechtstreeks toegang tot de Azure-portal vanuit uw browser op uw mobiele apparaat. Als u waarschuwingen wilt ontvangen, moet u [Azure-actiegroepen](action-groups.md) configureren om meldingen te ontvangen in de vorm van sms of een spraakoproep

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector en oplossing
[Application Insights Connector](app-insights-connector.md) biedt een manier om Application Insights-gegevens op te nemen in een Log Analytics-werkruimte. Deze duplicatie van gegevens was vereist om zichtbaarheid tussen infrastructuur- en toepassingsgegevens mogelijk te maken. Met de uitgebreide ondersteuning voor het bewaren van gegevens van Application Insights in maart 2019 en de mogelijkheid om query's met meerdere resources uit te voeren, hoeft u niet alleen meerdere Azure Monitor Application [Insights-bronnen](../log-query/cross-workspace-query.md) te [bekijken,](../log-query/unify-app-resource-data.md)maar hoeven u geen gegevens uit uw Toepassingsinzichten-bronnen te dupliceren en deze naar Log Analytics te verzenden. Bovendien stuurt de connector een subset van de eigenschappen van de toepassingen naar Log Analytics, terwijl de query's met meerdere bronnen u meer flexibiliteit bieden.  

Als zodanig is Application Insights Connector op 30 maart 2019 afgeschaft en verwijderd uit Azure Marketplace, samen met OMS-portaldeprecation. Bestaande verbindingen blijven werken tot 30 juni 2019. Met OMS-portalafschaffing is er geen manier om bestaande verbindingen van de portal te configureren en te verwijderen. Dit wordt ondersteund met behulp van de REST API die beschikbaar zal worden gesteld in januari 2019 en een melding wordt geplaatst op [Azure-updates.](https://azure.microsoft.com/updates/) 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
De [Azure Network Security Group Analytics-oplossing](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) wordt vervangen door de onlangs gelanceerde Traffic [Analytics,](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) die inzicht biedt in gebruikers- en toepassingsactiviteiten op cloudnetwerken. Traffic Analytics helpt u bij het controleren van de netwerkactiviteit van uw organisatie, het beveiligen van applicaties en gegevens, het optimaliseren van de werkbelastingprestaties en het compliant blijven. 

Deze oplossing analyseert NSG Flow logs en geeft inzicht in het volgende.

- Verkeer stroomt over uw netwerken tussen Azure en internet, openbare cloudregio's, VNET's en subnetten.
- Toepassingen en protocollen op uw netwerk, zonder de noodzaak van sniffers of speciale flow collection appliances.
- Top praters, spraakzame applicaties, VM-gesprekken in de cloud, traffic hotspots.
- Bronnen en bestemmingen van verkeer tussen VNET's, onderlinge relaties tussen kritieke zakelijke diensten en toepassingen.
- Beveiliging, waaronder kwaadaardig verkeer, poorten die openstaan voor internet, applicaties of VM's die toegang tot internet proberen.
- Capaciteitsbenutting, die u helpt bij het elimineren van problemen van over provisioning of onderbenutting.

U blijven vertrouwen op diagnostische instellingen om NSG-logboeken naar Log Analytics te verzenden, zodat uw bestaande opgeslagen zoekopdrachten, waarschuwingen en dashboards blijven werken. Klanten die de oplossing al hebben geïnstalleerd, kunnen deze tot nader order blijven gebruiken. Vanaf 5 september wordt de Network Security Group Analytics-oplossing uit de marketplace verwijderd en via de community beschikbaar gesteld als [Azure QuickStart-sjabloon.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Als u [uw beheergroep Operations Manager](om-agents.md)hebt gekoppeld aan Log Analytics, blijft deze zonder wijzigingen werken. Voor nieuwe verbindingen moet u echter de richtlijnen volgen in [het Microsoft System Center Operations Manager Management Pack om Operations Management Suite te configureren.](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)

## <a name="next-steps"></a>Volgende stappen
- Zie [Algemene vragen voor de overgang van OMS-portal naar Azure-portal voor Log Analytics-gebruikers](oms-portal-faq.md) voor richtlijnen voor de overgang van de OMS-portal naar de Azure-portal.
