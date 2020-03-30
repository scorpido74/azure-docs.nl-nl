---
title: DNS Analytics-oplossing in Azure Monitor | Microsoft Documenten
description: Stel de DNS Analytics-oplossing in Azure Monitor in en gebruik deze om inzichten te verzamelen in dns-infrastructuur op het gebied van beveiliging, prestaties en bewerkingen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657330"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Inzichten verzamelen over uw DNS-infrastructuur met de DNS Analytics Preview-oplossing

![DNS Analytics-symbool](./media/dns-analytics/dns-analytics-symbol.png)

In dit artikel wordt beschreven hoe u de Azure DNS Analytics-oplossing in Azure Monitor instellen en gebruiken om inzichten te verzamelen in DNS-infrastructuur over beveiliging, prestaties en bewerkingen.

DNS Analytics helpt u bij:

- Identificeer clients die schadelijke domeinnamen proberen op te lossen.
- Verouderde bronrecords identificeren.
- Identificeer veelgevraagde domeinnamen en spraakzame DNS-clients.
- Weergave van aanvraagbelasting op DNS-servers.
- Dynamische DNS-registratiefouten weergeven.

De oplossing verzamelt, analyseert en correleert Windows DNS analytische en auditlogs en andere gerelateerde gegevens van uw DNS-servers.

## <a name="connected-sources"></a>Verbonden bronnen

In de volgende tabel worden de verbonden bronnen beschreven die door deze oplossing worden ondersteund:

| **Verbonden bron** | **Ondersteuning** | **Beschrijving** |
| --- | --- | --- |
| [Windows-agents](../platform/agent-windows.md) | Ja | De oplossing verzamelt DNS-informatie van Windows-agents. |
| [Linux-agents](../learn/quick-collect-linux-computer.md) | Nee | De oplossing verzamelt geen DNS-informatie van directe Linux-agents. |
| [Beheergroep System Center Operations Manager](../platform/om-agents.md) | Ja | De oplossing verzamelt DNS-informatie van agents in een verbonden Operations Manager-beheergroep. Een directe verbinding van de operations manager-agent met Azure Monitor is niet vereist. Gegevens worden doorgestuurd van de beheergroep naar de werkruimte Log Analytics. |
| [Azure-opslagaccount](../platform/collect-azure-metrics-logs.md) | Nee | Azure-opslag wordt niet gebruikt door de oplossing. |

### <a name="data-collection-details"></a>Gegevensverzamelingsgegevens

De oplossing verzamelt DNS-voorraad- en DNS-gebeurtenisgerelateerde gegevens van de DNS-servers waar een Log Analytics-agent is geïnstalleerd. Deze gegevens worden vervolgens geüpload naar Azure Monitor en weergegeven in het oplossingsdashboard. Voorraadgerelateerde gegevens, zoals het aantal DNS-servers, -zones en resourcerecords, worden verzameld door de DNS PowerShell-cmdlets uit te voeren. De gegevens worden eens in de twee dagen bijgewerkt. De gebeurtenisgerelateerde gegevens worden in de buurt van realtime verzameld uit de [analytische en auditlogboeken](https://technet.microsoft.com/library/dn800669.aspx#enhanc) die worden geleverd door verbeterde DNS-logboekregistratie en -diagnose in Windows Server 2012 R2.

## <a name="configuration"></a>Configuratie

Gebruik de volgende informatie om de oplossing te configureren:

- U moet op elke DNS-server een [Windows-](../platform/agent-windows.md) of [Operations Manager-agent](../platform/om-agents.md) hebben die u wilt controleren.
- U de DNS Analytics-oplossing toevoegen aan uw Log Analytics-werkruimte vanuit azure [marketplace.](https://aka.ms/dnsanalyticsazuremarketplace) U het proces dat wordt beschreven in [Azure Monitor-oplossingen toevoegen](solutions.md)ook gebruiken vanuit de galerie Oplossingen.

De oplossing begint met het verzamelen van gegevens zonder verdere configuratie. U echter de volgende configuratie gebruiken om het verzamelen van gegevens aan te passen.

### <a name="configure-the-solution"></a>De oplossing configureren

Klik op het oplossingsdashboard op **Configuratie** om de pagina DNS Analytics-configuratie te openen. Er zijn twee soorten configuratiewijzigingen die u aanbrengen:

- **Domeinnamen op de witte lijst**. De oplossing verwerkt niet alle opzoekquery's. Het onderhoudt een whitelist van domeinnaam achtervoegsels. De opzoekquery's die worden opgelost voor de domeinnamen die overeenkomen met domeinnaamachtervoegsels in deze whitelist, worden niet verwerkt door de oplossing. Het niet verwerken van op de witte lijst staande domeinnamen helpt bij het optimaliseren van de gegevens die naar Azure Monitor worden verzonden. De standaardwhitelist bevat populaire namen van openbare domeinnamen, zoals www.google.com en www.facebook.com. U de volledige standaardlijst bekijken door te schuiven.

  U de lijst wijzigen om elk achtervoegsel van domeinnamen toe te voegen waarvoor u opzoekinzichten wilt weergeven. U ook het achtervoegsel van domeinnamen verwijderen waarvoor u geen opzoekinzichten wilt weergeven.

- **Spraakzame klantdrempel**. DNS-clients die de drempelwaarde voor het aantal opzoekaanvragen overschrijden, worden gemarkeerd in het **dns-clientsblad.** De standaarddrempel is 1.000. U de drempelwaarde bewerken.

    ![Domeinnamen op de witte lijst](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Management packs

Als u de Microsoft Monitoring Agent gebruikt om verbinding te maken met uw Log Analytics-werkruimte, wordt het volgende beheerpakket geïnstalleerd:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.dns)

Als uw beheergroep Operations Manager is verbonden met uw Log Analytics-werkruimte, worden de volgende beheerpakketten geïnstalleerd in Operations Manager wanneer u deze oplossing toevoegt. Er is geen vereiste configuratie of onderhoud van deze beheerpakketten:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.dns.configuration)

Zie [Operations Manager koppelen aan Log Analytics](../platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="use-the-dns-analytics-solution"></a>De DNS Analytics-oplossing gebruiken

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


De DNS-tegel bevat het aantal DNS-servers waar de gegevens worden verzameld. Het omvat ook het aantal verzoeken van clients om kwaadaardige domeinen op te lossen in de afgelopen 24 uur. Wanneer u op de tegel klikt, wordt het oplossingsdashboard geopend.

![TEGEL DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard van de oplossing

Het oplossingsdashboard toont samengevatte informatie voor de verschillende functies van de oplossing. Het bevat ook links naar de gedetailleerde weergave voor forensische analyse en diagnose. Standaard worden de gegevens van de afgelopen zeven dagen weergegeven. U de datum en het tijdsbereik wijzigen met behulp van het **tijdsselectiebesturingselement**, zoals in de volgende afbeelding wordt weergegeven:

![Tijdselectiebesturingselement](./media/dns-analytics/dns-time.png)

Het oplossingsdashboard toont de volgende bladen:

**DNS-beveiliging**. Rapporteert de DNS-clients die proberen te communiceren met schadelijke domeinen. Door gebruik te maken van Microsoft threat intelligence-feeds kan DNS Analytics client-IP's detecteren die toegang proberen te krijgen tot schadelijke domeinen. In veel gevallen, malware-geïnfecteerde apparaten "dial out" naar de "command and control" centrum van het kwaadaardige domein door het oplossen van de malware domeinnaam.

![DNS-beveiligingsblad](./media/dns-analytics/dns-security-blade.png)

Wanneer u op een client-IP in de lijst klikt, wordt Logboekzoeken geopend en worden de opzoekgegevens van de desbetreffende query weergegeven. In het volgende voorbeeld heeft DNS Analytics gedetecteerd dat de communicatie is uitgevoerd met een [IRCbot:](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)

![Logboekzoekresultaten met ircbot](./media/dns-analytics/ircbot.png)

De informatie helpt u bij het identificeren van de:

- Client-IP die de communicatie initieerde.
- Domeinnaam die wordt opgelost naar het kwaadaardige IP.
- IP-adressen waarop de domeinnaam wordt opgelost.
- Kwaadaardig IP-adres.
- Ernst van het probleem.
- Reden voor het op de zwarte lijst zetten van het kwaadaardige IP.
- Detectietijd.

**Opgevraagde domeinen**. Biedt de meest voorkomende domeinnamen die worden opgevraagd door de DNS-clients in uw omgeving. U de lijst met alle opgevraagde domeinnamen bekijken. U ook inzoomen op de details van het opzoekverzoek van een specifieke domeinnaam in Log Search.

![Blad met opgevraagde domeinen](./media/dns-analytics/domains-queried-blade.png)

**DNS-clients**. Meldt dat de clients de drempel voor het aantal query's in de gekozen periode *overschrijden.* U de lijst met alle DNS-clients en de details van de query's bekijken die door hen zijn gemaakt in Log Search.

![Blade DNS-clients](./media/dns-analytics/dns-clients-blade.png)

**Dynamische DNS-registraties**. Meldt naamregistratiefouten. Alle registratiefouten voor [adresbronrecords](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (type A en AAAA) worden gemarkeerd samen met de client-IP's die de registratieaanvragen hebben ingediend. U deze informatie vervolgens gebruiken om de hoofdoorzaak van de registratiefout te achterhalen door de volgende stappen te volgen:

1. Zoek de zone die gezaghebbend is voor de naam die de client probeert bij te werken.

1. Gebruik de oplossing om de voorraadgegevens van die zone te controleren.

1. Controleer of de dynamische update voor de zone is ingeschakeld.

1. Controleer of de zone is geconfigureerd voor veilige dynamische update of niet.

    ![Blade dynamische DNS-registraties](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Aanvragen voor naamregistratie**. De bovenste tegel toont een trendlijn van geslaagde en mislukte DNS dynamische updateverzoeken. De onderste tegel bevat de top 10 clients die mislukte DNS-updateverzoeken verzenden naar de DNS-servers, gesorteerd op het aantal fouten.

![Naam registratie aanvragen blade](./media/dns-analytics/name-reg-req-blade.png)

**Voorbeeld van DDI Analytics-query's**. Bevat een lijst met de meest voorkomende zoekopdrachten die ruwe analysegegevens rechtstreeks ophalen.


![Voorbeeldquery's](./media/dns-analytics/queries.png)

U deze query's gebruiken als uitgangspunt voor het maken van uw eigen query's voor aangepaste rapportage. De query's worden gekoppeld aan de pagina DNS Analytics Log Search waar de resultaten worden weergegeven:

- **Lijst met DNS-servers**. Toont een lijst met alle DNS-servers met bijbehorende FQDN, domeinnaam, forestnaam en server-IP's.
- **Lijst met DNS-zones**. Toont een lijst met alle DNS-zones met de bijbehorende zonenaam, dynamische updatestatus, naamservers en DNSSEC-ondertekeningsstatus.
- **Ongebruikte resourcerecords**. Toont een lijst met alle ongebruikte/verouderde bronrecords. Deze lijst bevat de naam van de bronrecord, het type resourcerecord, de bijbehorende DNS-server, recordgeneratietijd en zonenaam. U deze lijst gebruiken om de DNS-bronrecords te identificeren die niet meer worden gebruikt. Op basis van deze informatie u deze vermeldingen vervolgens verwijderen van de DNS-servers.
- **DNS-servers queryladen**. Toont informatie zodat u een perspectief krijgen op de DNS-belasting op uw DNS-servers. Met deze informatie u de capaciteit voor de servers plannen. U naar het tabblad **Statistieken** gaan om de weergave te wijzigen in een grafische visualisatie. Deze weergave helpt u te begrijpen hoe de DNS-belasting over uw DNS-servers wordt verdeeld. Het toont DNS-querysnelheidtrends voor elke server.

    ![DNS-servers query log zoekresultaten](./media/dns-analytics/dns-servers-query-load.png)

- **DNS-zones queryladen**. Toont de statistieken VAN DNS-zonequery per seconde van alle zones op de DNS-servers die door de oplossing worden beheerd. Klik op het tabblad **Statistieken** om de weergave te wijzigen van gedetailleerde records naar een grafische visualisatie van de resultaten.
- **Configuratiegebeurtenissen**. Toont alle DNS-configuratiewijzigingsgebeurtenissen en bijbehorende berichten. U deze gebeurtenissen vervolgens filteren op basis van het tijdstip van de gebeurtenis-id, DNS-server of taakcategorie. De gegevens kunnen u helpen bij het controleren van wijzigingen die op specifieke tijdstippen zijn aangebracht in specifieke DNS-servers.
- **DNS Analytical Log**. Toont alle analytische gebeurtenissen op alle DNS-servers die door de oplossing worden beheerd. U deze gebeurtenissen vervolgens filteren op basis van het tijdstip van de gebeurtenis-id, DNS-server, client-IP die de opzoekquery heeft gemaakt en de taakcategorie querytypen. Analytische DNS-servergebeurtenissen maken het mogelijk om activiteiten te volgen op de DNS-server. Elke keer dat de server DNS-informatie verzendt of ontvangt, wordt een analytische gebeurtenis geregistreerd.

### <a name="search-by-using-dns-analytics-log-search"></a>Zoeken met DNS Analytics Log Search

Op de pagina Zoeken in logboek u een query maken. U uw zoekresultaten filteren met facetbesturingselementen. U ook geavanceerde query's maken om uw resultaten te transformeren, te filteren en te rapporteren. Begin met de volgende query's:

1. Typ in het **queryvak**alle `DnsEvents` DNS-gebeurtenissen weer te geven die worden gegenereerd door de DNS-servers die door de oplossing worden beheerd. De resultaten bevatten de logboekgegevens voor alle gebeurtenissen die betrekking hebben op opzoekquery's, dynamische registraties en configuratiewijzigingen.

    ![Zoeken naar dnsevents-logboek](./media/dns-analytics/log-search-dnsevents.png)  

    a. Als u de logboekgegevens voor opzoekquery's wilt weergeven, selecteert u **LookUpQuery** als het filter **Subtype** vanuit het facetbesturingselement aan de linkerkant. Er wordt een tabel weergegeven met alle opzoekquerygebeurtenissen voor de geselecteerde periode.

    b. Als u de logboekgegevens voor dynamische registraties wilt weergeven, selecteert u **DynamicRegistration** als het filter **Subtype** vanuit het facetbesturingselement aan de linkerkant. Er wordt een tabel weergegeven met alle dynamische registratiegebeurtenissen voor de geselecteerde periode.

    c. Als u de logboekgegevens voor configuratiewijzigingen wilt weergeven, selecteert u **ConfigurationChange** als het filter **Subtype** vanuit het facetbesturingselement aan de linkerkant. Er wordt een tabel weergegeven met alle configuratiewijzigingsgebeurtenissen voor de geselecteerde periode.

1. Typ in het **queryvak**alle `DnsInventory` DNS-voorraadgerelateerde gegevens weer te geven voor de DNS-servers die door de oplossing worden beheerd. De resultaten bevatten de logboekgegevens voor DNS-servers, DNS-zones en bronrecords.

    ![Zoeken naar dns-logboeklogboeken](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Problemen oplossen

Veelvoorkomende stappen voor het oplossen van problemen:

1. Ontbrekende DNS-zoekgegevens - Als u dit probleem wilt oplossen, probeert u de config opnieuw in te stellen of de configuratiepagina eenmaal in de portal te laden. Als u het opnieuw instellen wilt, wijzigt u een instelling in een andere waarde en wijzigt u deze terug naar de oorspronkelijke waarde en slaat u de config op.

## <a name="feedback"></a>Feedback

Ga naar de [pagina Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) om ideeën te plaatsen voor DNS Analytics-functies waaraan u wilt werken. 

## <a name="next-steps"></a>Volgende stappen

[Querylogboeken](../log-query/log-query-overview.md) om gedetailleerde DNS-logboekrecords weer te geven.
