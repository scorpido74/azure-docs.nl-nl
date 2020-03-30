---
title: Richtlijnen voor persoonlijke gegevens die zijn opgeslagen in Azure Log Analytics| Microsoft Documenten
description: In dit artikel wordt beschreven hoe u persoonlijke gegevens beheert die zijn opgeslagen in Azure Log Analytics en de methoden om deze te identificeren en te verwijderen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: a720627e1783d2e29ef180b7855132ea59444cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248747"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Richtlijnen voor persoonsgegevens die zijn opgeslagen in Log Analytics en Application Insights

Log Analytics is een gegevensarchief waar waarschijnlijk persoonsgegevens worden gevonden. Application Insights slaat zijn gegevens op in een Log Analytics-partitie. In dit artikel wordt besproken waar dergelijke gegevens in Log Analytics en Application Insights doorgaans worden gevonden, evenals de mogelijkheden die u voor u beschikbaar heeft om dergelijke gegevens te verwerken.

> [!NOTE]
> Voor de toepassing van dit artikel _hebben loggegevens_ betrekking op gegevens die naar een Log Analytics-werkruimte worden verzonden, terwijl _toepassingsgegevens_ verwijzen naar gegevens die zijn verzameld door Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie voor de verwerking van persoonsgegevens

Terwijl het aan u en uw bedrijf is om uiteindelijk de strategie te bepalen waarmee u uw privégegevens (als helemaal niet) behandelt, zijn de volgende enkele mogelijke benaderingen. Ze worden vermeld in volgorde van voorkeur vanuit een technisch oogpunt van de meeste tot minst voorkeur:

* Stop waar mogelijk met het verzamelen, verdoezelen, anonimiseren of anderszins aanpassen van de verzamelde gegevens om uit te sluiten dat deze als "privé" worden beschouwd. Dit is _veruit_ de voorkeursbenadering, waardoor u de noodzaak bespaart om een zeer kostbare en impactvolle strategie voor gegevensverwerking te creëren.
* Probeer, indien mogelijk, de gegevens te normaliseren om de impact op het dataplatform en de prestaties te verminderen. Maak bijvoorbeeld in plaats van een expliciete gebruikersnaam te registreren een opzoekgegevens die de gebruikersnaam en hun gegevens correleren met een interne id die vervolgens elders kan worden geregistreerd. Op die manier, als een van uw gebruikers u vraagt om hun persoonlijke gegevens te verwijderen, is het mogelijk dat alleen het verwijderen van de rij in de opzoektabel die overeenkomt met de gebruiker voldoende is. 
* Ten slotte, als privégegevens moeten worden verzameld, bouwt u een proces rond het zuiverings-API-pad en het bestaande query-API-pad om te voldoen aan alle verplichtingen die u hebt met betrekking tot het exporteren en verwijderen van privégegevens die aan een gebruiker zijn gekoppeld. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Waar u zoeken naar privégegevens in Log Analytics?

Log Analytics is een flexibele winkel, waarmee u tijdens het voorschrijven van een schema voor uw gegevens elk veld met aangepaste waarden overschrijven. Bovendien kan elk aangepast schema worden ingenomen. Als zodanig is het onmogelijk om precies te zeggen waar privégegevens in uw specifieke werkruimte te vinden zijn. De volgende locaties zijn echter goede uitgangspunten in uw voorraad:

### <a name="log-data"></a>Logboekgegevens

* *IP-adressen*: Log Analytics verzamelt verschillende IP-informatie in veel verschillende tabellen. In de volgende query worden bijvoorbeeld alle tabellen weergegeven waarin de afgelopen 24 uur IPv4-adressen zijn verzameld:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Gebruikers-id's*: Gebruikers-id's zijn te vinden in een grote verscheidenheid aan oplossingen en tabellen. U een bepaalde gebruikersnaam zoeken in uw hele gegevensset met de zoekopdracht:
    ```
    search "[username goes here]"
    ```
  Vergeet niet om niet alleen te zoeken naar door de mens leesbare gebruikersnamen, maar ook GUIDs die direct kunnen worden herleid tot een bepaalde gebruiker!
* *Apparaat-id's:* Net als gebruikers-id's worden apparaat-id's soms als "privé" beschouwd. Gebruik dezelfde aanpak als hierboven vermeld voor gebruikers-id's om tabellen te identificeren waar dit een probleem kan zijn. 
* *Aangepaste gegevens:* Met Log Analytics kan de verzameling in verschillende methoden worden verzameld: aangepaste logboeken en aangepaste velden, de [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) en aangepaste gegevens die worden verzameld als onderdeel van systeemgebeurtenislogboeken. Al deze gegevens zijn vatbaar voor het bevatten van privégegevens en moeten worden onderzocht om na te gaan of dergelijke gegevens bestaan.
* *Door oplossingen vastgelegde gegevens:* Omdat het oplossingsmechanisme een open-end is, raden we u aan alle tabellen die door oplossingen worden gegenereerd, te bekijken om naleving te garanderen.

### <a name="application-data"></a>Toepassingsgegevens

* *IP-adressen:* Hoewel Application Insights standaard alle IP-adresvelden versluiert tot "0.0.0.0", is het een vrij algemeen patroon om deze waarde te overschrijven met het werkelijke IP-adres van de gebruiker om sessie-informatie te behouden. De onderstaande Analytics-query kan worden gebruikt om een tabel te vinden die waarden bevat in de kolom IP-adres, andere dan "0.0.0.0" in de afgelopen 24 uur:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Gebruikersnamen:* Standaard gebruikt Application Insights willekeurig gegenereerde id's voor gebruikers- en sessietracking. Het is echter gebruikelijk om te zien dat deze velden worden overschreven om een id op te slaan die relevanter is voor de toepassing. Bijvoorbeeld: gebruikersnamen, AAD GUIDs, enz. Deze ID's worden vaak beschouwd als binnenbereik als persoonsgegevens en moeten daarom op de juiste wijze worden behandeld. Onze aanbeveling is altijd om te proberen deze id's te verdoezelen of te anonimiseren. Velden waar deze waarden vaak worden gevonden, zijn session_Id, user_Id, user_AuthenticatedId, user_AccountId en aangepaste Afmetingen.
* *Aangepaste gegevens:* met Application Insights u een set aangepaste dimensies toevoegen aan elk gegevenstype. Deze afmetingen kunnen *alle* gegevens zijn. Gebruik de volgende query om eventuele aangepaste dimensies te identificeren die in de afgelopen 24 uur zijn verzameld:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Gegevens in het geheugen en in transit:* Application Insights houdt uitzonderingen, aanvragen, afhankelijkheidsoproepen en traceringen bij. Privégegevens kunnen vaak worden verzameld op code- en HTTP-oproepniveau. Bekijk de tabellen voor uitzonderingen, aanvragen, afhankelijkheden en traces om dergelijke gegevens te identificeren. Gebruik [telemetrieinitialisators](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) waar mogelijk om deze gegevens te verdoezelen.
* *Snapshot Debugger vangt:* Met de functie [Momentopnamefoutopsporing](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) in Application Insights u foutopsporingsmomentopnamen verzamelen wanneer een uitzondering wordt gemaakt op het productieexemplaar van uw toepassing. Momentopnamen worden de volledige stacktracering weergegeven die leidt tot de uitzonderingen en de waarden voor lokale variabelen bij elke stap in de stack. Helaas maakt deze functie geen selectieve verwijdering van snappunten of programmatische toegang tot gegevens in de momentopname mogelijk. Als de standaardmomentopnamepercentage niet voldoet aan uw nalevingsvereisten, is de aanbeveling daarom om de functie uit te schakelen.

## <a name="how-to-export-and-delete-private-data"></a>Privégegevens exporteren en verwijderen

Zoals eerder vermeld in de strategie voor de [verwerking van persoonsgegevens,](#strategy-for-personal-data-handling) wordt het __ten zeerste__ aanbevolen om uw beleid voor het verzamelen van gegevens te herstructureren om het verzamelen van privégegevens uit te schakelen, te verdoezelen of te anonymiseren, of het anderszins te wijzigen om het te verwijderen van "privé". Het verwerken van de gegevens zal vooral leiden tot kosten voor u en uw team om een strategie te definiëren en te automatiseren, een interface te bouwen voor uw klanten om te communiceren met hun gegevens door middel van, en de lopende onderhoudskosten. Verder is het rekenkundig duur voor Log Analytics en Application Insights, en een groot volume van gelijktijdige query- of zuiverings-API-aanroepen heeft het potentieel om een negatieve invloed te hebben op alle andere interactie met de Log Analytics-functionaliteit. Dat gezegd hebbende, zijn er inderdaad enkele geldige scenario's waarin privégegevens moeten worden verzameld. Voor deze gevallen moeten gegevens worden behandeld zoals beschreven in deze sectie.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Bekijken en exporteren

Voor zowel het bekijken als exporteren van gegevensaanvragen moet de [Log Analytics-query-API](https://dev.loganalytics.io/) of de [Application Insights-query-API](https://dev.applicationinsights.io/quickstart) worden gebruikt. Logica om de vorm van de gegevens om te zetten naar een geschikte om aan uw gebruikers te leveren, is aan u om te implementeren. [Azure Functions](https://azure.microsoft.com/services/functions/) is een geweldige plek om dergelijke logica te hosten.

> [!IMPORTANT]
>  Hoewel de overgrote meerderheid van de zuiveringsbewerkingen veel sneller kan worden voltooid dan de SLA, **is de formele SLA voor de voltooiing van zuiveringsbewerkingen ingesteld op 30 dagen** vanwege hun grote impact op het gebruikte gegevensplatform. Dit is een geautomatiseerd proces; er is geen manier om te vragen dat een bewerking sneller wordt afgehandeld.

### <a name="delete"></a>Verwijderen

> [!WARNING]
> Deletes in Log Analytics zijn destructief en niet omkeerbaar! Wees uiterst voorzichtig bij de uitvoering ervan.

We hebben beschikbaar gesteld als onderdeel van een privacy afhandeling van een *zuivering* API-pad. Dit pad moet spaarzaam worden gebruikt vanwege het risico dat hiermee gepaard gaat, de potentiële impact op de prestaties en het potentieel om alle aggregaties, metingen en andere aspecten van uw Log Analytics-gegevens scheef te trekken. Zie de [sectie Strategie voor de verwerking van persoonsgegevens](#strategy-for-personal-data-handling) voor alternatieve benaderingen voor het verwerken van privégegevens.

Zuivering is een zeer bevoorrechte bewerking die geen enkele app of gebruiker in Azure (inclusief zelfs de broneigenaar) machtigingen heeft om uit te voeren zonder expliciet een rol in Azure Resource Manager te krijgen. Deze rol is _Data Purger_ en moet voorzichtig worden gedelegeerd vanwege het potentieel voor gegevensverlies. 

> [!IMPORTANT]
> Om systeembronnen te beheren, worden zuiveringsaanvragen beperkt met 50 aanvragen per uur. U moet batch de uitvoering van zuivering aanvragen door het verzenden van een enkele opdracht waarvan het predicaat omvat alle gebruikersidentiteiten die moeten worden gewist. Gebruik de [operator in om](/azure/kusto/query/inoperator) meerdere identiteiten op te geven. U moet de query uitvoeren voordat u het zuiveringsverzoek uitvoert om te controleren of de resultaten worden verwacht. 



Zodra de rol Azure Resource Manager is toegewezen, zijn twee nieuwe API-paden beschikbaar: 

#### <a name="log-data"></a>Logboekgegevens

* [POST-zuivering](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - neemt een object dat parameters van gegevens opgeeft om een verwijzings-GUID te verwijderen en retourneert 
* GET purge status - de POST purge call retourneert een 'x-ms-status-locatie' header die een URL bevat die u aanroepen om de status van uw zuiverings-API te bepalen. Bijvoorbeeld:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Hoewel we verwachten dat de overgrote meerderheid van de zuiveringsbewerkingen veel sneller zal voltooien dan onze SLA, vanwege hun grote impact op het dataplatform dat door Log Analytics wordt gebruikt, **is de formele SLA voor de voltooiing van zuiveringsbewerkingen ingesteld op 30 dagen.** 

#### <a name="application-data"></a>Toepassingsgegevens

* [POST-zuivering](https://docs.microsoft.com/rest/api/application-insights/components/purge) - neemt een object dat parameters van gegevens opgeeft om een verwijzings-GUID te verwijderen en retourneert
* GET purge status - de POST purge call retourneert een 'x-ms-status-locatie' header die een URL bevat die u aanroepen om de status van uw zuiverings-API te bepalen. Bijvoorbeeld:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Hoewel de overgrote meerderheid van de zuiveringsbewerkingen veel sneller kan worden uitgevoerd dan de SLA, vanwege hun grote impact op het dataplatform dat door Application Insights wordt gebruikt, **is de formele SLA voor de voltooiing van zuiveringsbewerkingen ingesteld op 30 dagen.**

## <a name="next-steps"></a>Volgende stappen
- Zie [Log Analytics-gegevensbeveiliging](../../azure-monitor/platform/data-security.md)voor meer informatie over hoe Log Analytics-gegevens worden verzameld, verwerkt en beveiligd.
- Zie [Application Insights-gegevensbeveiliging](../../azure-monitor/app/data-retention-privacy.md)voor meer informatie over hoe Application Insights-gegevens worden verzameld, verwerkt en beveiligd.
