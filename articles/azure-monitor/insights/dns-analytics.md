---
title: DNS-analyse oplossing in Azure Monitor | Microsoft Docs
description: Stel de DNS-analyse-oplossing in Azure Monitor in en gebruik deze om inzicht te krijgen in de DNS-infra structuur voor beveiliging, prestaties en bewerkingen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: ad61743751ace9ca0c7eba12ffcea5f15e1157d5
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316186"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Verzamel inzichten over uw DNS-infrastructuur met de oplossing DNS Analytics Preview

![Symbool van DNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

In dit artikel wordt beschreven hoe u de oplossing voor Azure DNS Analytics kunt instellen en gebruiken in Azure Monitor om inzicht te krijgen in de DNS-infra structuur voor beveiliging, prestaties en bewerkingen.

DNS Analytics helpt u bij:

- Clients identificeren die op te lossen schadelijke domeinnamen.
- Identificeer verlopen bronrecords.
- Identificeer vaak opgevraagde domeinnamen en dremepelwaarde DNS-clients.
- Weergave aanvraagbelasting op DNS-servers.
- Dynamische DNS-registratie van mislukte pogingen weergeven.

De oplossing verzamelt, analyseert en correleert analytische Windows DNS- en auditlogboeken en andere gerelateerde gegevens uit uw DNS-servers.

## <a name="connected-sources"></a>Verbonden bronnen

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| **Verbonden bron** | **Ondersteuning** | **Beschrijving** |
| --- | --- | --- |
| [Windows-agents](../platform/agent-windows.md) | Ja | De oplossing verzamelt DNS-gegevens van Windows-agents. |
| [Linux-agents](../learn/quick-collect-linux-computer.md) | Nee | De oplossing verzamelt geen DNS-gegevens van directe Linux-agents. |
| [System Center Operations Manager-beheergroep](../platform/om-agents.md) | Ja | De oplossing verzamelt DNS-gegevens van agents in een verbonden beheergroep van Operations Manager. Een directe verbinding van de Operations Manager agent naar Azure Monitor is niet vereist. Gegevens uit de beheergroep doorgestuurd naar de Log Analytics-werkruimte. |
| [Azure Storage-account](../platform/collect-azure-metrics-logs.md) | Nee | Azure storage wordt niet gebruikt door de oplossing. |

### <a name="data-collection-details"></a>Details van de verzameling gegevens

De oplossing verzamelt DNS-inventarisatie- en DNS-gebeurtenis met betrekking tot gegevens van de DNS-servers waarop een Log Analytics-agent is geïnstalleerd. Deze gegevens worden vervolgens geüpload naar Azure Monitor en weer gegeven in het dash board van de oplossing. Inventarisatie-gerelateerde gegevens, zoals het aantal DNS-servers, -zones en -bronrecords worden verzameld door het uitvoeren van de DNS PowerShell-cmdlets. De gegevens worden eenmaal per twee dagen bijgewerkt. De gebeurtenis-gerelateerde gegevens worden verzameld in de buurt van real-time uit de [analytische en auditlogboeken](https://technet.microsoft.com/library/dn800669.aspx#enhanc) geleverd door de verbeterde DNS-logboekregistratie en diagnostische gegevens in Windows Server 2012 R2.

## <a name="configuration"></a>Configuratie

Gebruik de volgende informatie in de oplossing te configureren:

- Hebt u een [Windows](../platform/agent-windows.md) of [Operations Manager](../platform/om-agents.md) -agent op elke DNS-server die u wilt bewaken.
- U kunt de oplossing DNS Analytics toevoegen aan uw Log Analytics-werkruimte van de [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). U kunt ook het proces dat wordt beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](solutions.md)gebruiken.

De oplossing start het verzamelen van gegevens zonder de noodzaak van verdere configuratie. Echter, kunt u de volgende configuratie voor het aanpassen van het verzamelen van gegevens.

### <a name="configure-the-solution"></a>De oplossing configureren

Klik op het dashboard van de oplossing **configuratie** om de pagina configuratie van DNS Analytics te openen. Er zijn twee soorten wijzigingen in de configuratie die u kunt maken:

- **Goedgekeurde domeinnamen**. De opzoekquery's worden niet door de oplossing worden verwerkt. Wordt een lijst met toegestane domeinnaamachtervoegsels bijgehouden. De lookup-query's die worden omgezet naar de domeinnamen die overeenkomen met de naam van domeinachtervoegsels in deze lijst met toegestane adressen worden niet verwerkt door de oplossing. Geen verwerking van white list-domein namen helpt bij het optimaliseren van de gegevens die naar Azure Monitor worden verzonden. De standaard goedgekeurde lijst bevat populaire openbare domeinnamen, zoals www.google.com en www.facebook.com. U kunt de volledige lijst met weergeven door te schuiven.

  De lijst om toe te voegen van eventuele achtervoegsel domeinnaam die u wilt opzoeken inzichten voor weergeven, kunt u wijzigen. U kunt ook een achtervoegsel domeinnaam die u niet wilt opzoeken inzichten voor verwijderen.

- **Dremepelwaarde voor zeer actieve Client**. DNS-clients die groter zijn dan de drempelwaarde voor het aantal opzoekaanvragen zijn gemarkeerd de **DNS-Clients** blade. De standaarddrempelwaarde is 1000. U kunt de drempel voor bewerken.

    ![Goedgekeurde domeinnamen](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Management packs

Als u van de Microsoft Monitoring Agent gebruikmaakt verbinding maken met uw Log Analytics-werkruimte, worden de volgende managementpack is geïnstalleerd:

- Micro soft DNS data collector Intelligence Pack (micro soft. intelligence packs. DNS)

Als uw Operations Manager-beheergroep is verbonden met uw Log Analytics-werkruimte, worden de volgende management packs geïnstalleerd in Operations Manager wanneer u deze oplossing toevoegt. Er is geen vereiste configuratie of onderhoud van deze management packs:

- Micro soft DNS data collector Intelligence Pack (micro soft. intelligence packs. DNS)
- Microsoft System Center Advisor DNS Analytics-configuratie (Microsoft.IntelligencePack.Dns.Configuration)

Zie [Operations Manager koppelen aan Log Analytics](../platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="use-the-dns-analytics-solution"></a>De oplossing DNS Analytics gebruiken

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


De DNS-tegel bevat het aantal DNS-servers waarop de gegevens worden verzameld. Het bevat ook het aantal aanvragen van clients op te lossen schadelijke domeinen in de afgelopen 24 uur. Wanneer u op de tegel klikt, wordt het oplossingsdashboard geopend.

![Tegel DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard van de oplossing

Dashboard van de oplossing bevat een samenvatting van de gegevens voor de verschillende functies van de oplossing. Het bevat ook koppelingen naar de gedetailleerde weergave voor forensische analyse en diagnose. Standaard worden de gegevens weergegeven voor de afgelopen zeven dagen. U kunt het bereik van de datum en tijd wijzigen met behulp van de **-upbesturingselement voor selectie van datum / tijd-** , zoals wordt weergegeven in de volgende afbeelding:

![Time-upbesturingselement voor selectie](./media/dns-analytics/dns-time.png)

Dashboard van de oplossing ziet u de volgende blades:

**DNS-beveiliging**. De DNS-clients die probeert te communiceren met schadelijke domeinen-rapporten. DNS Analytics kunnen met behulp van Microsoft threat feeds met informatie over client-IP-adressen die toegang probeert te krijgen van schadelijke domeinen worden gedetecteerd. In veel gevallen bellen malware geïnfecteerde apparaten' ' in het midden 'command and control' van het schadelijke domein door het omzetten van de domeinnaam van schadelijke software.

![De blade DNS-beveiliging](./media/dns-analytics/dns-security-blade.png)

Wanneer u een client-IP-adres in de lijst klikt, wordt zoeken in Logboeken wordt geopend en worden de details van het opzoeken van de respectieve query. In het volgende voorbeeld DNS Analytics gedetecteerd dat de communicatie is uitgevoerd met een [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot):

![Resultaten van de logboekzoekopdracht ircbot weergeven](./media/dns-analytics/ircbot.png)

De informatie helpt u bij het identificeren van de:

- Client IP-adres dat de communicatie wordt gestart.
- De domeinnaam die wordt omgezet naar het schadelijke IP-adres.
- IP-adressen die de domeinnaam wordt omgezet in.
- Schadelijke IP-adres.
- Ernst van het probleem.
- De reden voor witte het schadelijke IP-adres.
- Tijd van de detectie.

**Domeinen die zijn opgevraagd**. Biedt de meest voorkomende domeinnamen worden opgevraagd met de DNS-clients in uw omgeving. Hier vindt u de lijst van alle domeinnamen die zijn opgevraagd. U kunt ook inzoomen op de details van de lookup-aanvraag van een specifieke domeinnaam in zoeken in Logboeken.

![Domeinen opgevraagde blade](./media/dns-analytics/domains-queried-blade.png)

**DNS-Clients**. Rapporten van de clients *inbreuk op de drempelwaarde* voor het aantal query's in de geselecteerde tijdsperiode. U kunt de lijst van alle DNS-clients en de details van de query's die door deze in zoeken in Logboeken weergeven.

![Blade DNS-Clients](./media/dns-analytics/dns-clients-blade.png)

**Dynamische DNS-registraties**. Rapporten naam registratiefouten. Alle fouten voor het adres van [bronrecords](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (Type A en AAAA) zijn gemarkeerd, samen met de client-IP-adressen die de van registratieaanvragen. U kunt deze informatie vervolgens gebruiken om te vinden van de hoofdoorzaak van de registratie is mislukt door de volgende stappen:

1. Zoek de zone die gemachtigd is voor de naam die de client wilt bijwerken.

1. De oplossing gebruiken om te controleren of de informatie over de inventaris van die zone.

1. Controleer of de dynamische update voor de zone is ingeschakeld.

1. Controleer of de zone is geconfigureerd voor beveiligde dynamische update of niet.

    ![Dynamische DNS-registraties-blade](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Naam van aanvragen voor naamregistraties**. De bovenste tegel ziet u een trendlijn van geslaagde en mislukte DNS-aanvragen voor dynamische updates. De lagere tegel geeft een lijst van de top 10-clients die mislukte DNS-update aanvragen om de DNS-servers, gesorteerd op basis van het aantal fouten te verzenden.

![Naam registratie aanvragen blade](./media/dns-analytics/name-reg-req-blade.png)

**Voorbeeld van een Analytics-query's DDI**. Bevat een lijst van de meest voorkomende zoekquery's die rechtstreeks ophalen van onbewerkte analytics-gegevens.


![Voorbeeldquery's](./media/dns-analytics/queries.png)

U kunt deze query's gebruiken als uitgangspunt voor het maken van uw eigen query's voor aangepaste rapportage. De query's koppelen aan de pagina DNS Analytics zoeken in Logboeken is waar de resultaten worden weergegeven:

- **Lijst met DNS-Servers**. Geeft een lijst weer van alle DNS-servers met hun bijbehorende FQDN-naam, domeinnaam, naam van het forest en server IP-adressen.
- **Lijst met DNS-Zones**. Geeft een lijst weer van alle DNS-zones met de naam van de bijbehorende zone, de status van de dynamische update, naamservers en DNSSEC-ondertekeningsstatus.
- **Ongebruikte resourcerecords**. Geeft een lijst van alle niet-gebruikte/verouderd resourcerecords. Deze lijst bevat de naam van de resource record, bronrecordtype, de bijbehorende DNS-server, tijd voor het genereren van record en de naam van zone. U kunt deze lijst gebruiken om te identificeren van de DNS-bronrecords die niet langer in gebruik zijn. Op basis van deze informatie, kunt u vervolgens verwijderen die items uit de DNS-servers.
- **Querybelasting van DNS-Servers**. Toont informatie zodat u een perspectief van de DNS-belasting op uw DNS-servers krijgt. Deze informatie kunt u de capaciteit voor de servers plannen. Gaat u naar de **metrische gegevens** tab om de weergave wijzigen in een grafische visualisatie. In deze weergave helpt u begrijpen hoe de DNS-belasting is verdeeld over de DNS-servers. Deze DNS-query tarief laat trends zien voor elke server.

    ![Zoekresultaten voor DNS-servers query-logboek](./media/dns-analytics/dns-servers-query-load.png)

- **Querybelasting van DNS-Zones**. Toont de statistieken van de DNS-zone-query-per-seconde van alle zones op de DNS-servers die worden beheerd door de oplossing. Klik op de **metrische gegevens** tabblad om te wijzigen van de weergave van gedetailleerde records in een grafische visualisatie van de resultaten.
- **Configuratiegebeurtenissen**. Bevat alle DNS-configuratie wijzigen-gebeurtenissen en bijbehorende berichten. Vervolgens kunt u deze gebeurtenissen op basis van tijd van de gebeurtenis, gebeurtenis-ID, DNS-server, filteren of taakcategorie. Aan de hand van de gegevens kunt u wijzigingen in specifieke DNS-servers op specifieke tijdstippen met controleren.
- **Logboek met analytische DNS**. Geeft alle analytische gebeurtenissen op alle DNS-servers die worden beheerd door de oplossing. U kunt vervolgens filteren deze gebeurtenissen op basis van tijd van de gebeurtenis, gebeurtenis-ID, DNS-server, client-IP-die de opzoekquery query type taakcategorie en. DNS-server-analysegebeurtenissen inschakelen activiteiten bijhouden op de DNS-server. Een analytische gebeurtenis wordt geregistreerd telkens wanneer de server verzendt of ontvangt DNS-gegevens.

### <a name="search-by-using-dns-analytics-log-search"></a>Zoeken met behulp van DNS Analytics zoeken in Logboeken

U kunt een query maken op de pagina zoeken in Logboeken. U kunt uw zoekresultaten filteren met behulp van facet besturingselementen. U kunt ook geavanceerde query's te transformeren, te filteren en rapport maken op uw resultaten. Start met behulp van de volgende query's:

1. In de **query zoekvak**, type `DnsEvents` om alle DNS-gebeurtenissen die worden gegenereerd door de DNS-servers worden beheerd door de oplossing weer te geven. De lijst met resultaten de logboekgegevens voor alle gebeurtenissen met betrekking tot de opzoekquery's, dynamische registraties en wijzigingen in de configuratie.

    ![Zoeken in Logboeken DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Als u wilt de logboekgegevens voor een lookup-query's weergeven, selecteert u **LookUpQuery** als de **Subtype** filter vanuit de besturingselement facet aan de linkerkant. Er wordt een tabel met een lijst met alle gebeurtenissen van de lookup-query voor de geselecteerde periode weergegeven.

    b. Als u wilt de logboekgegevens voor dynamische registraties weergeven, selecteert u **DynamicRegistration** als de **Subtype** filter vanuit de besturingselement facet aan de linkerkant. Er wordt een tabel met een lijst met alle gebeurtenissen van de dynamische registratie voor de geselecteerde periode weergegeven.

    c. Als u wilt weergeven van de logboekgegevens voor wijzigingen in de configuratie, selecteer **ConfigurationChange** als de **Subtype** filter vanuit de besturingselement facet aan de linkerkant. Er wordt een tabel met een lijst met alle wijzigingsgebeurtenissen voor de configuratie voor de geselecteerde periode weergegeven.

1. In de **query zoekvak**, type `DnsInventory` om alle DNS-inventarisatie-gerelateerde gegevens voor de DNS-servers worden beheerd door de oplossing weer te geven. De lijst met resultaten de logboekgegevens voor DNS-servers, DNS-zones en resourcerecords.

    ![Zoeken in Logboeken DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Problemen oplossen

Veelvoorkomende stappen voor probleem oplossing:

1. Ontbrekende DNS-Zoek gegevens: als u dit probleem wilt oplossen, probeert u het configuratie bestand opnieuw in te stellen of zojuist de configuratie pagina in de portal te laden. Als u de instelling opnieuw wilt instellen, wijzigt u een instelling in een andere waarde, wijzigt u deze in de oorspronkelijke waarde en slaat u de configuratie op.

## <a name="feedback"></a>Feedback

Als u feedback wilt geven, gaat u naar de [pagina log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) om ideeën te plaatsen voor DNS-analyse functies waarmee u kunt werken. 

## <a name="next-steps"></a>Volgende stappen

[Query logboeken](../log-query/log-query-overview.md) om gedetailleerde DNS-logboek records weer te geven.
