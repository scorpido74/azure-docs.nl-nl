---
title: Log Analytics Veelgestelde vragen | Microsoft Docs
description: Antwoorden op veelgestelde vragen over de Azure Log Analytics-service.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/13/2018
ms.openlocfilehash: e3ebb87a7a5f6200d860c1c79591719c32313e11
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932218"
---
# <a name="log-analytics-faq"></a>Veelgestelde vragen over Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Deze veelgestelde vragen over micro soft vindt u in Microsoft Azure een lijst met veelgestelde vraag over Log Analytics. Als u nog meer vragen hebt over Log Analytics, gaat u naar het [discussie forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) en plaatst u uw vragen. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.


## <a name="new-logs-experience"></a>Nieuwe logboeken

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>V: wat is het verschil tussen de nieuwe logboeken en Log Analytics?

A: ze zijn hetzelfde. [Log Analytics wordt geïntegreerd als een functie in azure monitor](../../azure-monitor/azure-monitor-rebrand.md) om een meer Unified monitoring-ervaring te bieden. De nieuwe logboeken in Azure Monitor zijn precies hetzelfde als de Log Analytics query's die al door veel klanten zijn gebruikt.

### <a name="q-can-i-still-use-log-search"></a>V: kan ik de zoek functie voor logboeken nog steeds gebruiken? 

A: zoeken in Logboeken is momenteel nog steeds beschikbaar in de OMS-Portal en in de Azure Portal onder de naam **Logboeken (klassiek)** . De OMS-portal wordt op 15 januari 2019 officieel buiten gebruik gesteld. De klassieke Logboeken in Azure Portal worden geleidelijk buiten gebruik gesteld en de nieuwe logboek ervaring vervangen. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>V. Kan ik nog steeds een geavanceerde analyse Portal gebruiken? 
De nieuwe logboeken in de Azure Portal zijn gebaseerd op de geavanceerde analyse Portal, maar u hebt nog steeds toegang tot buiten de Azure Portal. De route kaart voor het buiten gebruik stellen van deze externe portal wordt binnenkort aangekondigd.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>V. Waarom kan ik query Explorer niet zien en knoppen Opslaan in de nieuwe logboeken?

**Query Verkenner**, waarschuwings knoppen **Opslaan** en **instellen** zijn niet beschikbaar bij het verkennen van Logboeken in de context van een specifieke resource. Als u waarschuwingen wilt maken, een query wilt opslaan of laden, moeten Logboeken een bereik hebben van een werk ruimte. Als u logboeken wilt openen in de werkruimte context, selecteert u **alle services** >  > **Logboeken**te **controleren** . De laatst gebruikte werk ruimte is geselecteerd, maar u kunt een andere werk ruimte selecteren. Zie [gegevens in log Analytics weer geven en analyseren](../log-query/portals.md) voor meer informatie.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>V. Hoe kan ik aangepaste velden in de nieuwe logboeken-ervaring extra heren? 

A: extractie van aangepaste velden wordt momenteel ondersteund in de klassieke Logboeken. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>V. Waar vind ik een lijst weergave in de nieuwe logboeken? 

A: de lijst weergave is niet beschikbaar in de nieuwe logboeken. Er bevindt zich een pijl links van elke record in de tabel met resultaten. Klik op deze pijl om de Details voor een specifieke record te openen. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>V. Na het uitvoeren van een query is er een lijst met voorgestelde filters beschikbaar. Hoe kan ik filters zien? 

A: Klik in het linkerdeel venster op filters om een voor beeld van de implementatie van nieuwe filters weer te geven. Dit is nu gebaseerd op uw volledige resultatenset in plaats van beperkt door de record limiet van 10.000 van de gebruikers interface. Dit is momenteel een lijst met de meest populaire filters en de tien meest voorkomende waarden voor elk filter. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>V. Waarom krijg ik de fout: ' micro soft. Insights van resource provider registreren voor dit abonnement om deze query in te scha kelen ' in Logboeken, na inzoomen vanuit de VM? 

A: standaard worden veel resource providers automatisch geregistreerd, maar u moet mogelijk bepaalde resource providers hand matig registreren. Hiermee configureert u uw abonnement om te werken met de resource provider. Het bereik voor registratie is altijd het abonnement. Zie [Resourceproviders en -typen](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) voor meer informatie.

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>V. Waarom krijg ik geen Access-fout bericht bij het openen van Logboeken vanaf een VM-pagina? 

A: als u de VM-logboeken wilt weer geven, moet u beschikken over de machtiging lezen voor de werk ruimten waarin de VM-logboeken worden opgeslagen. In deze gevallen moet uw beheerder u de machtigingen verlenen in Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>V. Waarom kan ik toegang krijgen tot mijn werk ruimte in de OMS-Portal, maar krijg ik de fout ' u hebt geen toegang ' in de Azure Portal?  

A: om toegang te krijgen tot een werk ruimte in azure, moet u de Azure-machtigingen hebben toegewezen. Er zijn enkele gevallen waarin u mogelijk niet de juiste toegangs machtigingen hebt. In deze gevallen moet uw beheerder u machtigingen verlenen in Azure. Zie OMS- [Portal naar Azure verplaatsen](oms-portal-transition.md) voor meer informatie.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>V. Waarom kan ik het weer geven van de ontwerp weergave niet zien in Logboeken? 
A: View Designer is alleen beschikbaar in Logboeken voor gebruikers die zijn toegewezen met Inzender machtigingen of hoger.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>V. Kan ik nog steeds de analyse Portal buiten Azure gebruiken?
A. Ja, de pagina Logboeken in Azure en de portal Advanced Analytics zijn gebaseerd op dezelfde code. Log Analytics wordt geïntegreerd als een functie in Azure Monitor om een meer Unified monitoring-ervaring te bieden. U kunt nog steeds toegang krijgen tot de analyse Portal met behulp van de URL: https:\/\/Portal. loganalytics. io/abonnementen/{subscriptionId}/ResourceGroups/{resourceGroupName}/werk ruimten/{workspacenaam}.



## <a name="general"></a>Algemeen

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>V. Hoe kan ik mijn weer gaven en oplossingen zien in Azure Portal? 

A: de lijst met weer gaven en geïnstalleerde oplossingen is beschikbaar in Azure Portal. Klik op **Alle services**. Selecteer in de lijst met resources de optie **monitor**en klik vervolgens op **... Meer**. De laatst gebruikte werk ruimte is geselecteerd, maar u kunt een andere werk ruimte selecteren. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>V. Waarom kan ik geen werk ruimten maken in de regio West-Centraal VS? 

A: deze regio heeft een tijdelijke capaciteits limiet. Deze limiet is gepland voor eind september 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>V. Gebruikt Log Analytics dezelfde agent als Azure Security Center?

A: in de eerste juni 2017 begon Azure Security Center met het gebruik van micro soft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie [Azure Security Center Veelgestelde vragen over platform migratie](../../security-center/security-center-enable-data-collection.md)voor meer informatie.

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>V. Welke controles worden uitgevoerd door de AD-en SQL-evaluatie-oplossingen?

A: met de volgende query wordt een beschrijving weer gegeven van alle controles die op dit moment worden uitgevoerd:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

De resultaten kunnen vervolgens naar Excel worden geëxporteerd voor verdere controle.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>V. Waarom wordt er iets anders dan OMS in de System Center Operations Manager-console weer geven?

A: afhankelijk van het update pakket van Operations Manager u zich bevindt, ziet u mogelijk een knoop punt voor *System Center Advisor*, *Operational Insights*of *log Analytics*.

De teken reeks die is bijgewerkt naar *OMS* is opgenomen in een Management Pack. deze moet hand matig worden geïmporteerd. Als u de huidige tekst en functionaliteit wilt bekijken, volgt u de instructies in het meest recente artikel System Center Operations Manager Update pakket KB en de console vernieuwen.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>V: is er een on-premises versie van Log Analytics?

A: Nee. Log Analytics is een schaal bare Cloud service die grote hoeveel heden gegevens verwerkt en opslaat. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>V. Hoe kan ik problemen oplossen als Log Analytics niet langer gegevens verzamelt?

A: voor een abonnement en een werk ruimte die is gemaakt vóór 2 april 2018 en die zich in de *gratis* prijs categorie bevindt, wordt het verzamelen van gegevens voor de rest van de dag gestopt als er meer dan 500 MB aan gegevens worden verzonden. Het bereiken van de dagelijkse limiet is een gemeen schappelijke reden dat Log Analytics het verzamelen van gegevens niet meer of gegevens lijkt te missen.  

Log Analytics maakt een gebeurtenis van het type *heartbeat* en kan worden gebruikt om te bepalen of het verzamelen van gegevens is gestopt. 

Voer de volgende query uit in de zoek opdracht om te controleren of u de dagelijkse limiet bereikt en ontbrekende gegevens: `Heartbeat | summarize max(TimeGenerated)`

Voer de volgende query uit om een specifieke computer te controleren: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Wanneer het verzamelen van gegevens stopt, worden er, afhankelijk van het geselecteerde tijds bereik, geen gevonden records weer gegeven.   

De volgende tabel beschrijft de redenen waarom het verzamelen van gegevens wordt gestopt en een aanbevolen actie om het verzamelen van gegevens te hervatten:

| Gegevens verzameling voor de reden stopt                       | Gegevens verzameling hervatten |
| -------------------------------------------------- | ----------------  |
| De limiet voor gratis gegevens is bereikt<sup>1</sup>       | Wacht tot de volgende maand voor het automatisch opnieuw opstarten van de verzameling is of<br> Overschakelen naar een betaalde prijs categorie |
| Het Azure-abonnement bevindt zich in een onderbroken staat vanwege: <br> De gratis proef versie is beëindigd <br> Azure Pass is verlopen <br> De maandelijkse bestedings limiet is bereikt (bijvoorbeeld op een MSDN-of Visual Studio-abonnement)                          | Converteren naar een betaald abonnement <br> Converteren naar een betaald abonnement <br> Limiet verwijderen of wachten tot de limiet is ingesteld |

<sup>1</sup> als uw werk ruimte zich in de *gratis* prijs categorie bevindt, bent u beperkt tot het verzenden van 500 MB aan gegevens per dag naar de service. Wanneer u de dagelijkse limiet bereikt, stopt het verzamelen van gegevens tot de volgende dag. Gegevens die worden verzonden terwijl het verzamelen van gegevens is gestopt, worden niet geïndexeerd en kunnen niet worden doorzocht. Wanneer het verzamelen van gegevens wordt hervat, vindt verwerking alleen plaats voor nieuwe gegevens die worden verzonden. 

Log Analytics gebruikt UTC-tijd en elke dag begint om middernacht UTC. Als de werk ruimte de dagelijkse limiet bereikt, worden er tijdens het eerste uur van de volgende UTC-dag de verwerking hervat.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>V. Hoe kan ik een melding ontvangen wanneer het verzamelen van gegevens wordt gestopt?

A: gebruik de stappen in [een nieuwe logboek waarschuwing maken om een](../../azure-monitor/platform/alerts-metric.md) melding te ontvangen wanneer het verzamelen van gegevens wordt gestopt.

Wanneer u de waarschuwing maakt wanneer het verzamelen van gegevens wordt gestopt, stelt u het volgende in:

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaal naam** Selecteer **aangepaste logboek zoekopdracht**.
   - **Zoekquery** op `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Waarschuwingslogica** is **Gebaseerd op** het *aantal resultaten*, en **Voorwaarde** is *Groter dan* een **Drempelwaarde** van *0*
   - **Tijds duur** van *30* minuten en **waarschuwings frequentie** tot elke *10* minuten
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - De **naam** voor het *verzamelen van gegevens is gestopt*
   - **Ernst** op *Waarschuwing*

Geef een bestaande of maak een nieuwe [actie groep](../../azure-monitor/platform/action-groups.md) op, zodat wanneer de logboek waarschuwing overeenkomt met de criteria, u een melding ontvangt als er al meer dan 15 minuten een heartbeat ontbreekt.

## <a name="configuration"></a>Configuratie
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>V. Kan ik de naam wijzigen van de tabel/BLOB-container die wordt gebruikt voor het lezen van Azure Diagnostics (WAD)?

A. Nee, het is momenteel niet mogelijk om te lezen uit een wille keurige tabel of container in azure Storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>V. Welke IP-adressen worden gebruikt door de Log Analytics-Service? Hoe kan ik ervoor te zorgen dat mijn firewall alleen verkeer naar de Log Analytics-service toestaat?

A. De Log Analytics-service is gebaseerd op Azure. Log Analytics IP-adressen bevinden zich in de [Microsoft Azure Data Center IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653).

Wanneer service-implementaties worden uitgevoerd, worden de daad werkelijke IP-adressen van de Log Analytics service gewijzigd. De DNS-namen die u via uw firewall wilt toestaan, worden beschreven in de [netwerk vereisten](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>V. Ik gebruik ExpressRoute om verbinding te maken met Azure. Maakt mijn Log Analytics verkeer gebruik van mijn ExpressRoute-verbinding?

A. De verschillende soorten ExpressRoute-verkeer worden beschreven in de [ExpressRoute-documentatie](../../expressroute/expressroute-faqs.md#supported-services).

Verkeer naar Log Analytics maakt gebruik van het ExpressRoute-circuit met open bare peering.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>V. Is er een eenvoudige en eenvoudige manier om een bestaande Log Analytics-werk ruimte te verplaatsen naar een ander Log Analytics werk ruimte/Azure-abonnement?

A. Met de cmdlet `Move-AzResource` kunt u een Log Analytics-werk ruimte en een Automation-account van het ene Azure-abonnement naar het andere verplaatsen. Zie [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx)voor meer informatie.

Deze wijziging kan ook worden aangebracht in de Azure Portal.

U kunt geen gegevens van de ene Log Analytics werk ruimte naar de andere verplaatsen of de regio wijzigen waarin Log Analytics gegevens zijn opgeslagen.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>V: Hoe kan ik Log Analytics toevoegen aan System Center Operations Manager?

A: als u het meest recente update pakket bijwerkt en Management Packs importeert, kunt u Operations Manager verbinding maken met Log Analytics.

>[!NOTE]
>De Operations Manager verbinding met Log Analytics is alleen beschikbaar voor System Center Operations Manager 2012 SP1 en hoger.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>V: hoe kan ik controleren of een agent kan communiceren met Log Analytics?

A: om ervoor te zorgen dat de agent kan communiceren met OMS, gaat u naar: configuratie scherm, beveiligings & instellingen, **micro soft Monitoring Agent**.

Ga naar het tabblad **Azure log Analytics (OMS)** en zoek naar een groen vinkje. Een groen vinkje geeft aan dat de agent kan communiceren met de Azure-service.

Een geel waarschuwings pictogram geeft aan dat de agent communicatie problemen ondervindt met Log Analytics. Een veelvoorkomende reden is dat de service micro soft monitoring agent is gestopt. Gebruik service besturings beheer om de service opnieuw te starten.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>V: Hoe kan ik stopt een agent met communiceren met Log Analytics?

A: Verwijder in System Center Operations Manager de computer uit de lijst met OMS beheerde computers. Operations Manager werkt de configuratie van de agent bij naar Log Analytics niet meer. Voor agents die rechtstreeks zijn verbonden met Log Analytics, kunt u voor komen dat ze communiceren via: configuratie scherm, beveiligings & instellingen, **micro soft Monitoring Agent**.
Verwijder onder **Azure log Analytics (OMS)** alle weer gegeven werk ruimten.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>V: Waarom krijg ik een fout melding wanneer ik mijn werk ruimte probeer te verplaatsen van het ene Azure-abonnement naar het andere?

A: als u een werk ruimte wilt verplaatsen naar een ander abonnement of een andere resource groep, moet u eerst de koppeling van het Automation-account in de werk ruimte opheffen. Als u een Automation-account wilt ontkoppelen, moet u deze oplossingen verwijderen als ze in de werk ruimte zijn geïnstalleerd: Updatebeheer, Wijzigingen bijhouden of VM's buiten bedrijfsuren starten/stoppen worden verwijderd. Nadat deze oplossingen zijn verwijderd, ontkoppelt u het Automation-account door **gekoppelde werk ruimten** te selecteren in het linkerdeel venster van de Automation-account resource en klikt u op **werk ruimte ontkoppelen** op het lint.
 > Verwijderde oplossingen moeten opnieuw worden geïnstalleerd in de werk ruimte en de Automation-koppeling naar de werk ruimte moet worden aangepast na de verplaatsing.

Zorg ervoor dat u over de juiste machtigingen beschikt in azure-abonnementen.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>V: Waarom krijg ik een fout melding wanneer ik een SavedSearch probeer bij te werken?

A: u moet ETAG toevoegen in de hoofd tekst van de API of de eigenschappen van de Azure Resource Manager-sjabloon:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Agent gegevens
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>V. Hoeveel gegevens kan ik via de agent verzenden naar Log Analytics? Is er een maximum hoeveelheid gegevens per klant?
A. Met het gratis abonnement wordt een dagelijks limiet van 500 MB per werk ruimte ingesteld. De Standard-en Premium-abonnementen hebben geen limiet voor de hoeveelheid gegevens die worden geüpload. Als Cloud service is Log Analytics ontworpen om automatisch te schalen om het volume van een klant te verwerken, zelfs als het per dag terabytes is.

De Log Analytics-agent is ontworpen om ervoor te zorgen dat deze een geringe footprint heeft. Het gegevens volume is afhankelijk van de oplossingen die u inschakelt. U kunt gedetailleerde informatie vinden op het gegevens volume en de uitsplitsing per oplossing bekijken op de pagina [gebruik](../../azure-monitor/platform/data-usage.md) .

Voor meer informatie kunt u een blog van een [klant](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) lezen waarin de resultaten worden weer gegeven nadat u het resource gebruik (footprint) van de OMS-agent hebt geëvalueerd.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>V. Hoeveel netwerk bandbreedte wordt gebruikt door de micro soft Management Agent (MMA) bij het verzenden van gegevens naar Log Analytics?

A. Band breedte is een functie voor de hoeveelheid gegevens die wordt verzonden. Gegevens worden gecomprimeerd wanneer ze via het netwerk worden verzonden.

### <a name="q-how-much-data-is-sent-per-agent"></a>V. Hoeveel gegevens worden per agent verzonden?

A. De hoeveelheid gegevens die per agent wordt verzonden, is afhankelijk van:

* De oplossingen die u hebt ingeschakeld
* Het aantal logboeken en prestatie meter items dat wordt verzameld
* Het gegevens volume in de logboeken

De prijs categorie gratis is een goede manier om meerdere servers uit te scha kelen en het typische gegevens volume te meten. Het algehele gebruik wordt op de [gebruiks](../../azure-monitor/platform/data-usage.md) pagina weer gegeven.

Voor computers waarop de WireData-agent kan worden uitgevoerd, gebruikt u de volgende query om te zien hoeveel gegevens er worden verzonden:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Volgende stappen
* Aan de [slag met log Analytics](../../azure-monitor/overview.md) om meer te weten te komen over log Analytics en binnen enkele minuten aan de slag te gaan.
