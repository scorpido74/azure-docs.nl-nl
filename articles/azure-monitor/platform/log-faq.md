---
title: Log Analytics-Veelgestelde vragen over | Microsoft Docs
description: Antwoorden op veelgestelde vragen over de logboek analyse service van Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394787"
---
# <a name="log-analytics-faq"></a>Veelgestelde vragen over Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Deze veelgestelde vragen over micro soft is een lijst met veelgestelde vragen over de Azure Monitor Log Analytics werk ruimte. Als u aanvullende vragen over Log Analytics hebt, gaat u naar de [discussieforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) en plaats uw vraag. Wanneer u een vraag is vaak wordt gevraagd, toevoegen we deze aan dit artikel zodat snel en eenvoudig kunnen worden gevonden.


## <a name="new-logs-experience"></a>Nieuwe ervaring voor logboeken

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>V: Wat is het verschil tussen de nieuwe ervaring voor logboeken en Log Analytics?

A: ze zijn hetzelfde. [Log Analytics wordt als een functie in Azure Monitor geïntegreerd](../../azure-monitor/azure-monitor-rebrand.md) voor een meer geïntegreerde bewaking. De nieuwe logboeken-ervaring in Azure Monitor is precies hetzelfde als de Log Analytics-query's die veel klanten hebben al gebruikt.

### <a name="q-can-i-still-use-log-search"></a>V: kan ik nog steeds zoeken in Logboeken gebruiken? 

A: log Search is momenteel nog steeds beschikbaar in de OMS-portal en in de Azure-portal onder de naam van de **Logboeken (klassiek)** . De OMS-portal wordt op 15 januari 2019 officieel stopgezet. De klassieke logboeken-ervaring in Azure portal worden geleidelijk stopgezet en vervangen door de nieuwe ervaring voor Logboeken. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>V. Kan ik nog steeds Advanced Analytics-Portal gebruiken? 
De nieuwe ervaring van de logboeken in Azure portal is gebaseerd op de Portal Advanced Analytics, maar deze nog steeds toegankelijk buiten de Azure-portal. Het overzicht voor het buiten gebruik stellen van deze externe portal wordt binnenkort aangekondigd.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>V. Waarom kan niet ik Zie Query Explorer en knoppen in de nieuwe ervaring voor Logboeken opslaan?

**Query Explorer**, **opslaan** en **waarschuwing instellen** knoppen zijn niet beschikbaar wanneer logboeken verkennen in de context van een specifieke resource. Als u waarschuwingen maken, opslaan of een query laden, logboeken moeten binnen het bereik aan een werkruimte. Als u Logboeken in de context van de werkruimte, selecteert u **alle services** > **Monitor** > **logboeken**. De laatst gebruikte werkruimte is geselecteerd, maar u kunt een andere werkruimte selecteren. Zie [bekijken en analyseren van gegevens in Log Analytics](../log-query/portals.md) voor meer informatie.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>V. Hoe haal ik aangepaste velden in de nieuwe ervaring voor logboeken? 

A: aangepaste velden extractie worden momenteel ondersteund in de klassieke logboeken optreden. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>V. Waar vind ik lijstweergave in de nieuwe logboeken? 

A: lijstweergave is niet beschikbaar in de nieuwe logboeken. Er is een pijl naar links van elke record in de tabel met resultaten. Klik op deze pijl om de details voor een bepaalde record openen. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>V. Na het uitvoeren van een query is er een lijst met voorgestelde filters beschikbaar. Hoe kan ik filters zien? 

A: Klik in het linkerdeel venster op filters om een voor beeld van de implementatie van nieuwe filters weer te geven. Dit is nu gebaseerd op uw volledige resultatenset in plaats van wordt beperkt door de recordlimiet van 10.000 van de gebruikersinterface. Dit is momenteel een lijst van de meest populaire filters en de 10 meest voorkomende waarden voor elk filter. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>V. Waarom krijg ik de fout: '' Microsoft.Insights' voor dit abonnement waarmee deze query-resourceprovider registreren"in Logboeken na analyseren in VM? 

A: standaard veel providers van resources automatisch worden geregistreerd, u moet echter handmatig registreren door enkele resourceproviders. Hiermee configureert u uw abonnement om te werken met de resourceprovider. Het bereik voor de registratie is altijd het abonnement. Zie [Resourceproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) voor meer informatie.

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>V. Waarom kan ik krijg geen toegang tot foutbericht bij het openen van Logboeken op de pagina van een virtuele machine? 

A: VM als logboeken wilt raadplegen, moet u ook worden toegekend aan een leesmachtiging voor de werkruimten waarin de VM-Logboeken. In dergelijke gevallen moet de beheerder u verlenen machtigingen in Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>V. Waarom ik hebben toegang mijn werkruimte in de OMS-portal tot kan, maar ik krijg de fout 'u hebt geen toegang' in de Azure-portal?  

A: voor toegang tot een werkruimte in Azure, moet u toegewezen Azure-machtigingen hebben. Er zijn enkele gevallen waarbij u bent mogelijk niet gemachtigd juiste toegang. In dergelijke gevallen de beheerder moet verlenen u met de machtigingen in Azure.See [OMS-portal naar Azure verplaatst,](oms-portal-transition.md) voor meer informatie.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>V. Waarom zie ik niet kan geen Weergaveontwerper vermelding in Logboeken?

A: Weergaveontwerper is alleen beschikbaar in Logboeken voor gebruikers die zijn toegewezen met machtigingen van de inzender of hoger.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>V. Kan ik nog steeds de Analytics-portal buiten Azure gebruiken?

A. Ja, de pagina Logboeken in Azure en de portal Advanced Analytics zijn gebaseerd op de dezelfde code. Log Analytics wordt als een functie in Azure Monitor een meer uniform bewakingservaring geïntegreerd. U kunt nog steeds toegang krijgen tot de analyse Portal met behulp van de URL: https:\/\/Portal. loganalytics. io/abonnementen/{subscriptionId}/ResourceGroups/{resourceGroupName}/werk ruimten/{workspacenaam}.



## <a name="general"></a>Algemeen

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>V. Hoe kan ik mijn weergaven en oplossingen in Azure portal bekijken? 

A: de lijst met geïnstalleerde oplossingen en weergaven zijn beschikbaar in Azure portal. Klik op **Alle services**. Selecteer in de lijst met resources **Monitor**, klikt u vervolgens op **... Meer**. De laatst gebruikte werkruimte is geselecteerd, maar u kunt een andere werkruimte selecteren. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>V. Log Analytics maakt gebruik dezelfde agent als Azure Security Center?

A: in begin juni 2017, is Azure Security Center begonnen met behulp van de Microsoft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie voor meer informatie, [Azure Security Center-Platform migratie Veelgestelde vragen over](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>V. Welke controles zijn uitgevoerd door de AD en SQL-evaluatie-oplossingen?

A: de volgende query ziet u een beschrijving van alle controles die momenteel worden uitgevoerd:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

De resultaten kunnen vervolgens worden geëxporteerd naar Excel voor verder onderzoek.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>V. Waarom zie ik iets anders dan OMS in de System Center Operations Manager-console?

A: afhankelijk van welke Update Rollup van Operations Manager op, ziet u mogelijk een knooppunt voor *System Center Advisor*, *Operational Insights*, of *Log Analytics*.

Het bijwerken van de verbindingsreeks tekst naar *OMS* is opgenomen in een managementpack, dat moet handmatig worden geïmporteerd. Als u de huidige tekst en de functionaliteit, volg de instructies op het meest recente System Center Operations Manager Update Rollup KB-artikel en vernieuw de console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Vraag: is er een on-premises versie van Log Analytics?

A: Nee. Log Analytics is een schaalbare cloudservice die worden verwerkt en kunnen grote hoeveelheden gegevens worden opgeslagen. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>V. Hoe kan ik de hoogte gesteld wanneer het verzamelen van gegevens wordt gestopt?

A: Gebruik de stappen [maken van een nieuwe waarschuwing](../../azure-monitor/platform/alerts-metric.md) om te worden geïnformeerd wanneer het verzamelen van gegevens wordt gestopt.

Bij het maken van de waarschuwing voor wanneer het verzamelen van gegevens stopt, stel de:

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaalnaam** Selecteer **zoeken in Logboeken aangepaste**.
   - **Zoekquery** op `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Waarschuwingslogica** is **Gebaseerd op** het *aantal resultaten*, en **Voorwaarde** is *Groter dan* een **Drempelwaarde** van *0*
   - **Periode** van *30* minuten en **Waarschuwingsfrequentie** voor elke *10* minuten
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - **Naam** naar *gegevensverzameling gestopt*
   - **Ernst** op *Waarschuwing*

Geef een bestaande of maak een nieuwe [actiegroep](../../azure-monitor/platform/action-groups.md) zodat wanneer de waarschuwing voldoet aan criteria, u wordt gewaarschuwd als er een heartbeat ontbreekt voor meer dan 15 minuten.

## <a name="configuration"></a>Configuratie

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>V. Kan ik de naam van de tabel/blob-container gebruikt om te lezen van Azure Diagnostics (WAD) wijzigen?

A. Nee, is het niet mogelijk om te lezen uit verschillende tabellen of containers in Azure storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>V. Welke IP-adressen wordt het gebruik van de service Log Analytics? Hoe kan ik ervoor zorgen dat mijn firewall alleen toestaat dat verkeer naar de Log Analytics-service?

A. De service Log Analytics is gebaseerd op Azure. Log Analytics-IP-adressen zijn de [Microsoft Azure Datacenter IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653).

Als de service-implementaties worden gemaakt, wordt de werkelijke IP-adressen van de service Log Analytics wijzigen. De DNS-namen om toe te staan via de firewall zijn gedocumenteerd in [vereisten voor de](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>V. Ik ExpressRoute gebruiken voor verbinding met Azure. Mijn verkeer Log Analytics maakt gebruik van mijn ExpressRoute-verbinding?

A. De verschillende soorten verkeer van ExpressRoute worden beschreven in de [documentatie voor ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Verkeer naar Log Analytics maakt gebruik van het ExpressRoute-circuit voor openbaar peering.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>V. Is er een eenvoudige en eenvoudige manier om een bestaande Log Analytics-werkruimte verplaatsen naar een andere Log Analytics-werkruimte/Azure-abonnement?

A. De `Move-AzResource` cmdlet kunt u een Log Analytics-werkruimte en een Automation-account van één Azure-abonnement verplaatsen naar een andere. Zie [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx)voor meer informatie.

Deze wijziging kan ook worden gemaakt in Azure portal.

U kunt gegevens naar de andere verplaatsen van een Log Analytics-werkruimte of wijzigen van de regio die Log Analytics-gegevens zijn opgeslagen in.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>V: hoe kan ik Log Analytics naar System Center Operations Manager toevoegen

A: bijwerken naar de meest recente updatepakket installeren en importeren van management packs kunt u Operations Manager verbinden met Log Analytics.

>[!NOTE]
>De Operations Manager-verbinding met Log Analytics is alleen beschikbaar voor System Center Operations Manager 2012 SP1 en hoger.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>V: hoe kan ik Bevestig dat een agent kan communiceren met Log Analytics?

A: om ervoor te zorgen dat de agent kan communiceren met de Log Analytics-werk ruimte, gaat u naar: configuratie scherm, beveiligings & instellingen, **micro soft Monitoring Agent**.

Onder de **Azure Log Analytics (OMS)** tabblad, gaat u naar een groen vinkje. Een groen vinkje wordt bevestigd dat de agent kan communiceren met de Azure-service is.

Een geel waarschuwingspictogram betekent dat de agent heeft problemen communicatie met Log Analytics. Een veelvoorkomende reden waarom is dat de Microsoft Monitoring Agent-service is gestopt. Servicebesturingsbeheer gebruiken de service opnieuw te starten.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>V: hoe stop ik een agent niet communiceren met Log Analytics?

A: Verwijder in System Center Operations Manager de computer uit de lijst Log Analytics beheerde computers. Operations Manager werkt u de configuratie van de agent niet langer een rapport naar Log Analytics. Voor agents die rechtstreeks verbonden met Log Analytics, kunt u ze stopt vanaf communiceren via: het Configuratiescherm, beveiliging en -instellingen, **Microsoft Monitoring Agent**.
Onder **Azure Log Analytics (OMS)** , verwijder alle werkruimten die worden vermeld.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>V: Waarom krijg ik een foutbericht wanneer ik wil mijn werkruimte van één Azure-abonnement verplaatsen naar een andere?

A: om een werkruimte naar een ander abonnement of resourcegroep verplaatsen, moet u eerst het Automation-account in de werkruimte ontkoppelen. Bezig met ontkoppelen van een Automation-account vereist dat het verwijderen van deze oplossingen als ze zijn geïnstalleerd in de werkruimte: updatebeheer, wijzigingen bijhouden of VM's starten/stoppen buiten kantooruren worden verwijderd. Nadat deze oplossingen zijn verwijderd, het Automation-account loskoppelen door te selecteren **gekoppeld werkruimten** resource in het linkerdeelvenster in de Automation-account in en klikt u op **werkruimte ontkoppelen** op het lint.
 > Oplossingen hoeft te worden geïnstalleerd in de werkruimte verwijderd en het Automation-koppeling naar de werkruimte moet na de verplaatsing worden aangepast.

Zorg ervoor dat u gemachtigd in zowel Azure-abonnementen.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>V: Waarom krijg ik een foutbericht wanneer ik wil een SavedSearch bijwerken?

A: u nodig hebt om toe te voegen 'etag' in de hoofdtekst van de API of de sjablooneigenschappen van de Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Gegevens van de gebruikersagent
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>V. Hoeveel gegevens kan ik verzenden via de agent naar Log Analytics? Is er een maximale hoeveelheid gegevens per klant?
A. Er is geen limiet voor de hoeveelheid gegevens die wordt geüpload. deze is gebaseerd op de prijs optie die u hebt geselecteerd: capaciteits reservering of betalen naar gebruik. Een Log Analytics-werk ruimte is ontworpen om automatisch te schalen om het volume van een klant te verwerken, zelfs als het per dag terabytes is. Zie [prijs informatie](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie.

De Log Analytics-agent is ontworpen om te controleren of er een kleine footprint. Het gegevensvolume is afhankelijk van de oplossingen die u inschakelt. U kunt gedetailleerde informatie over het gegevensvolume en zien de uitsplitsing van de oplossing in de [gebruik](../../azure-monitor/platform/data-usage.md) pagina.

Voor meer informatie kunt u een blog van een [klant](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) lezen waarin de resultaten worden weer gegeven nadat u het resource gebruik (footprint) van de log Analytics-agent hebt geëvalueerd.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>V. Hoeveel bandbreedte van het netwerk wordt door de Microsoft Management Agent (MMA) gebruikt bij het verzenden van gegevens naar Log Analytics?

A. Bandbreedte is een functie voor de hoeveelheid gegevens die worden verzonden. Gegevens worden gecomprimeerd wanneer deze worden verzonden via het netwerk.

### <a name="q-how-much-data-is-sent-per-agent"></a>V. Hoeveel gegevens worden verzonden per agent?

A. Afhankelijk van de hoeveelheid gegevens die per agent worden verzonden:

* De oplossingen die u hebt ingeschakeld
* Het aantal logboeken en prestatiemeteritems worden verzameld
* De hoeveelheid gegevens in de logboeken

Algemeen gebruik wordt weergegeven op de [gebruik](../../azure-monitor/platform/data-usage.md) pagina.

Voor computers die kunnen worden uitgevoerd van de agent WireData, gebruikt u de volgende query uit om te zien hoeveel gegevens er worden verzonden:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Volgende stappen

Aan de [slag met Azure monitor](../../azure-monitor/overview.md) om meer te weten te komen over log Analytics en binnen enkele minuten aan de slag te gaan.
