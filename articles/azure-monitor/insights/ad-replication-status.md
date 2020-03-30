---
title: De replicatiestatus van Active Directory controleren
description: Het oplossingspakket active directory-replicatiestatus controleert regelmatig uw Active Directory-omgeving op replicatiefouten.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 30b0c7c87f6d55586b931be1445b175ce58565d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055906"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Status van Active Directory-replicatie bewaken met Azure Monitor

![AD-replicatiestatussymbool](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory is een belangrijk onderdeel van een IT-omgeving voor bedrijven. Om een hoge beschikbaarheid en hoge prestaties te garanderen, heeft elke domeincontroller zijn eigen kopie van de Active Directory-database. Domeincontrollers repliceren met elkaar om wijzigingen in de hele onderneming te verspreiden. Fouten in dit replicatieproces kunnen verschillende problemen veroorzaken in de hele onderneming.

De oplossing AD-replicatiestatus controleert regelmatig uw Active Directory-omgeving op replicatiefouten.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende informatie om de oplossing te installeren en configureren.

### <a name="prerequisites"></a>Vereisten

* De oplossing AD-replicatiestatus vereist een ondersteunde versie van .NET Framework 4.6.2 of hoger die is geïnstalleerd op elke computer waarop de logboekanalyse-agent voor Windows is geïnstalleerd (ook wel de Microsoft Monitoring Agent (MMA)) is geïnstalleerd.  De agent wordt gebruikt door System Center 2016 - Operations Manager, Operations Manager 2012 R2 en Azure Monitor.
* De oplossing ondersteunt domeincontrollers met Windows Server 2008 en 2008 R2, Windows Server 2012 en 2012 R2 en Windows Server 2016.
* Een werkruimte log-analyse om de Active Directory Health Check-oplossing toe te voegen vanuit de Azure-marktplaats in de Azure-portal. Er is geen extra configuratie vereist.


### <a name="install-agents-on-domain-controllers"></a>Agents installeren op domeincontrollers
U moet agents installeren op domeincontrollers die lid zijn van het domein dat moet worden geëvalueerd. U moet ook agents installeren op ledenservers en de agents configureren om AD-replicatiegegevens naar Azure Monitor te verzenden. Zie [Windows-computers verbinden](../../azure-monitor/platform/agent-windows.md)met Azure Monitor als u wilt weten hoe u Windows-computers met Azure Monitor verbinden. Zie [Operations Manager verbinden met Azure Monitor](../../azure-monitor/platform/om-agents.md)als uw domeincontroller al deel uitmaakt van een bestaande System Center Operations Manager-omgeving die u met Azure Monitor wilt verbinden.

### <a name="enable-non-domain-controller"></a>Niet-domeincontroller inschakelen
Als u geen van uw domeincontrollers rechtstreeks wilt verbinden met Azure Monitor, u elke andere computer in uw domein gebruiken die is verbonden met Azure Monitor om gegevens te verzamelen voor het oplossingspakket ad-replicatiestatus en deze de gegevens te laten verzenden.

1. Controleer of de computer lid is van het domein dat u wilt controleren met de oplossing AD-replicatiestatus.
2. [Sluit de Windows-computer aan op Azure Monitor](../../azure-monitor/platform/om-agents.md) of verbind deze via uw bestaande Operations [Manager-omgeving met Azure Monitor](../../azure-monitor/platform/om-agents.md), als deze nog niet is verbonden.
3. Stel op die computer de volgende registersleutel in:<br>Sleutel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management\<Groups ManagementGroupName>\Solutions\ADReplication**<br>Waarde: **IsTarget**<br>Waardegegevens: **waar**

   > [!NOTE]
   > Deze wijzigingen worden pas van kracht als u de Microsoft Monitoring Agent-service (HealthService.exe) opnieuw start.
   > ### <a name="install-solution"></a>Installatieoplossing
   > Volg het proces dat is beschreven in [Een bewakingsoplossing installeren](solutions.md#install-a-monitoring-solution) om de **oplossing voor active directory-replicatiestatus** toe te voegen aan uw logboekanalysewerkruimte. Er is geen verdere configuratie nodig.


## <a name="ad-replication-status-data-collection-details"></a>Gegevensverzameling van AD-replicatiestatus
In de volgende tabel worden methoden voor het verzamelen van gegevens en andere details weergegeven over de manier waarop gegevens worden verzameld voor de AD-replicatiestatus.

| 
    platform
   | Direct Agent | SCOM-agent | Azure Storage | SCOM vereist? | SCOM-agentgegevens verzonden via managementgroep | verzamelfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |elke vijf dagen |



## <a name="understanding-replication-errors"></a>Replicatiefouten begrijpen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

In de tegel AD-replicatiestatus wordt weergegeven hoeveel replicatiefouten u momenteel hebt. **Kritieke replicatiefouten** zijn fouten die zich op of boven 75% van de [tombstone-levensduur](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) voor uw Active Directory-forest bevinden.

![Tegel AD-replicatiestatus](./media/ad-replication-status/oms-ad-replication-tile.png)

Wanneer u op de tegel klikt, u meer informatie over de fouten weergeven.
![Dashboard AD-replicatiestatus](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status van doelserver en status van bronserver
In deze kolommen wordt de status weergegeven van doelservers en bronservers die replicatiefouten ondervinden. Het getal na elke domeinnaam van de domeincontroller geeft het aantal replicatiefouten op die domeincontroller aan.

De fouten voor zowel doelservers als bronservers worden weergegeven omdat sommige problemen gemakkelijker kunnen worden opgelost vanuit het perspectief van de bronserver en andere vanuit het perspectief van de doelserver.

In dit voorbeeld u zien dat veel doelservers ongeveer hetzelfde aantal fouten hebben, maar er is één bronserver (ADDC35) die veel meer fouten heeft dan alle andere. Het is waarschijnlijk dat er een probleem is op ADDC35 waardoor het niet om gegevens te verzenden naar de replicatiepartners. Als u de problemen op ADDC35 oplost, kunnen veel van de fouten die in het doelservergebied worden weergegeven, worden opgelost.

### <a name="replication-error-types"></a>Replicatiefouttypen
Dit gebied geeft u informatie over de soorten fouten die in uw hele onderneming zijn gedetecteerd. Elke fout heeft een unieke numerieke code en een bericht waarmee u de hoofdoorzaak van de fout bepalen.

De donut aan de bovenkant geeft u een idee van welke fouten verschijnen steeds minder vaak in uw omgeving.

Het toont u wanneer meerdere domeincontrollers dezelfde replicatiefout ervaren. In dit geval u mogelijk een oplossing op één domeincontroller ontdekken of identificeren en deze vervolgens herhalen op andere domeincontrollers die door dezelfde fout zijn getroffen.

### <a name="tombstone-lifetime"></a>Tombstone Levensduur
De levensduur van de grafsteen bepaalt hoe lang een verwijderd object, een grafsteen genoemd, wordt bewaard in de Active Directory-database. Wanneer een verwijderd object de levensduur van de grafsteen passeert, wordt het automatisch verwijderd uit de Active Directory-database.

De standaard levensduur van de grafsteen is 180 dagen voor de meest recente versies van Windows, maar het was 60 dagen op oudere versies, en het kan expliciet worden gewijzigd door een Active Directory-beheerder.

Het is belangrijk om te weten of u replicatiefouten hebt die naderen of voorbij de tombstone levensduur zijn. Als twee domeincontrollers een replicatiefout ervaren die gedurende de levensduur van de grafsteen blijft bestaan, wordt replicatie uitgeschakeld tussen deze twee domeincontrollers, zelfs als de onderliggende replicatiefout is opgelost.

Het Tombstone Lifetime-gebied helpt u plaatsen te identificeren waar uitgeschakelde replicatie dreigt te gebeuren. Elke fout in de categorie **Meer dan 100% TSL** vertegenwoordigt een partitie die niet is gerepliceerd tussen de bron- en doelserver gedurende ten minste de tombstone-levensduur voor het forest.

In deze situatie is het eenvoudig herstellen van de replicatiefout niet voldoende. U moet ten minste handmatig onderzoeken of u achtergebleven objecten identificeren en opruimen voordat u de replicatie herstarten. Mogelijk moet u zelfs een domeincontroller buiten gebruik stellen.

Naast het identificeren van replicatiefouten die zijn blijven bestaan na de levensduur van de grafsteen, wilt u ook aandacht besteden aan eventuele fouten die in de **50-75% TSL-** of **75-100% TSL-categorieën** vallen.

Dit zijn fouten die duidelijk blijven hangen, niet van voorbijgaande aard, dus ze waarschijnlijk nodig uw interventie op te lossen. Het goede nieuws is dat ze nog niet de grafsteen levensduur bereikt. Als u deze problemen snel oplost en *voordat* ze de levensduur van de grafsteen bereiken, kan replicatie opnieuw worden opgestart met minimale handmatige interventie.

Zoals eerder vermeld, toont de dashboardtegel voor de AD-replicatiestatusoplossing het aantal *kritieke* replicatiefouten in uw omgeving, dat wordt gedefinieerd als fouten die meer dan 75% van de levensduur van de grafsteen bedragen (inclusief fouten die meer dan 100% van TSL bedragen). Streef ernaar om dit nummer op 0 te houden.

> [!NOTE]
> Alle berekeningen van het tombstone-levensduurpercentage zijn gebaseerd op de werkelijke levensduur van de grafsteen voor uw Active Directory-forest, zodat u erop vertrouwen dat deze percentages nauwkeurig zijn, zelfs als u een aangepaste tombstone-levensduurwaarde hebt ingesteld.
>
>

### <a name="ad-replication-status-details"></a>DETAILS AD-replicatiestatus
Wanneer u op een item in een van de lijsten klikt, ziet u aanvullende details hierover met behulp van een logboekquery. De resultaten worden gefilterd om alleen de fouten met betrekking tot dat item weer te geven. Als u bijvoorbeeld klikt op de eerste domeincontroller die wordt vermeld onder **De status van de doelserver (ADDC02),** ziet u queryresultaten die zijn gefilterd om fouten weer te geven met die domeincontroller die als doelserver wordt weergegeven:

![Fouten in AD-replicatiestatus in queryresultaten](./media/ad-replication-status/oms-ad-replication-search-details.png)

Vanaf hier u verder filteren, de logboekquery wijzigen, enzovoort. Zie [Logboekgegevens analyseren in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)voor meer informatie over het gebruik van de logboekquery's in Azure Monitor.

In het veld **HelpLink** wordt de URL van een TechNet-pagina weergegeven met aanvullende details over die specifieke fout. U deze koppeling kopiëren en plakken in uw browservenster om informatie te zien over het oplossen van problemen en het oplossen van de fout.

U ook op **Exporteren** klikken om de resultaten naar Excel te exporteren. Als u de gegevens exporteert, u replicatiefoutgegevens visualiseren op elke gewenste manier.

![geëxporteerde AD-replicatiestatusfouten in Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Veelgestelde vragen over AD-replicatiestatus
**V: Hoe vaak worden ad-replicatiestatusgegevens bijgewerkt?**
A: De informatie wordt elke vijf dagen bijgewerkt.

**V: Is er een manier om te configureren hoe vaak deze gegevens worden bijgewerkt?**
A: Niet op dit moment.

**V: Moet ik al mijn domeincontrollers toevoegen aan mijn Log Analytics-werkruimte om de replicatiestatus te kunnen zien?**
A: Nee, er mag slechts één domeincontroller worden toegevoegd. Als u meerdere domeincontrollers in uw Log Analytics-werkruimte hebt, worden gegevens van alle domeincontrollers naar Azure Monitor verzonden.

**V: Ik wil geen domeincontrollers toevoegen aan mijn Log Analytics-werkruimte. Kan ik de oplossing AD-replicatiestatus nog steeds gebruiken?**

A: Ja. U de waarde van een registersleutel zo instellen dat deze is ingeschakeld. Zie [Niet-domeincontroller inschakelen](#enable-non-domain-controller).

**V: Wat is de naam van het proces dat de gegevensverzameling doet?**
A: AdvisorAssessment.exe

**V: Hoe lang duurt het voordat gegevens worden verzameld?**
A: De tijd voor het verzamelen van gegevens is afhankelijk van de grootte van de Active Directory-omgeving, maar duurt meestal minder dan 15 minuten.

**V: Wat voor soort gegevens wordt verzameld?**
A: Replicatiegegevens worden verzameld via LDAP.

**V: Is er een manier om te configureren wanneer gegevens worden verzameld?**
A: Niet op dit moment.

**V: Welke machtigingen heb ik nodig om gegevens te verzamelen?**
A: Normale gebruikersmachtigingen voor Active Directory zijn voldoende.

## <a name="troubleshoot-data-collection-problems"></a>Problemen met het verzamelen van gegevens oplossen
Om gegevens te verzamelen, vereist het oplossingspakket voor ad-replicatiestatus dat ten minste één domeincontroller is verbonden met uw Log Analytics-werkruimte. Totdat u een domeincontroller aansluit, verschijnt er een bericht dat **gegevens nog steeds worden verzameld.**

Als u hulp nodig hebt bij het aansluiten van een van uw domeincontrollers, u documentatie bekijken bij [Windows-computers verbinden met Azure Monitor.](../../azure-monitor/platform/om-agents.md) Als uw domeincontroller al is verbonden met een bestaande System Center Operations Manager-omgeving, u ook documentatie bekijken bij [Connect System Center Operations Manager naar Azure Monitor.](../../azure-monitor/platform/om-agents.md)

Zie [Niet-domeincontroller inschakelen](#enable-non-domain-controller)als u geen domeincontrollers rechtstreeks wilt verbinden met Azure Monitor of System Center Operations Manager.

## <a name="next-steps"></a>Volgende stappen
* Gebruik [Logboekquery's in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde statusgegevens van Active Directory-replicatie weer te geven.
