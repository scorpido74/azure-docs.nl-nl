---
title: Azure Blockchain-service (ABS) bewaken
description: Azure Blockchain-service bewaken via Azure-monitor
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293246"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Azure Blockchain-service bewaken via Azure Monitor  

Naarmate klanten blockchain-scenario's op Azure Blockchain Service (ABS) uitvoeren, wordt het van cruciaal belang om de resources voor beschikbaarheid, prestaties en bewerkingen te controleren. In dit artikel worden de monitoringgegevens beschreven die zijn gegenereerd door Azure Blockchain Service en hoe men de verschillende functies en integraties van Azure Monitor kan gebruiken om te analyseren en te waarschuwen, om productieniveauomgevingen te beheren.  

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Azure Blockchain Service maakt bewakingsgegevens met Azure Monitor, een volledige stackmonitoringservice in Azure die een complete set functies biedt om uw Azure-resources te bewaken. Zie [Azure-resources bewaken met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)voor meer informatie over Azure Monitor.
 

De volgende secties bouwen voort op dit artikel door de specifieke gegevens te beschrijven die zijn verzameld met Azure Blockchain Service en voorbeelden te geven voor het configureren van gegevensverzameling en het analyseren van deze gegevens met Azure-hulpprogramma's.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Gegevens controleren die zijn verzameld met Azure Blockchain Service  

Azure Blockchain Service verzamelt dezelfde soort bewakingsgegevens als andere Azure-resources, die worden beschreven in [Monitoringgegevens](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) van Azure-bronnen. Zie [Monitor Azure Blockchain Service-gegevensreferentie](#monitor-azure-blockchain-service-data-reference) voor een gedetailleerde referentie van de logboeken en statistieken die zijn gemaakt door Azure Blockchain Service.

De overzichtspagina in de Azure-portal voor elke Azure Blockchain Service-lidbron bevat een korte weergave van de transacties, inclusief de afgehandelde en verwerkte blokken. Sommige van deze gegevens worden automatisch verzameld en beschikbaar voor analyse zodra u de Azure Blockchain Service-lidbron hebt gemaakt, terwijl u extra gegevensverzameling met extra configuratie inschakelen.

## <a name="diagnostic-settings"></a>Diagnostische instellingen  

Platformstatistieken en het activiteitenlogboek worden automatisch verzameld, maar u moet een diagnostische instelling maken om bronlogboeken te verzamelen of door te sturen buiten Azure Monitor. Zie [Diagnostische instelling maken om platformlogboeken en -statistieken in Azure te verzamelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure-portal, CLI of PowerShell.

Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen. De categorieën voor Azure Blockchain Service worden hieronder weergegeven.

**Blockchain proxy logs** – Selecteer de categorie als u de NGNIX proxy logs wilt controleren. Alle transactiegegevens van de klant zijn beschikbaar voor audit en debug-doeleinden.  

**Blockchain-toepassingslogboeken** - Selecteer de categorie om logboeken van de blockchain-toepassing te ontvangen door de beheerde service. Voor een ABS-Quorum-lid zijn deze logboeken bijvoorbeeld de logboeken van Quorum zelf.  

**Metrische aanvragen:** selecteer de optie om metrische gegevens van Azure Cosmos DB te verzamelen naar de bestemmingen in de diagnostische instelling, die automatisch worden verzameld in Azure Metrics. Verzamel metrische gegevens met bronlogboeken om beide soorten gegevens samen te analyseren en metrische gegevens buiten Azure Monitor te verzenden.

## <a name="analyze-metric-data"></a>Metrische gegevens analyseren  

U statistieken voor Azure Blockchain Service analyseren met Metrics explorer, navigeer naar het tabblad Statistieken onder sectie Controle in het abs-bronblad. Zie [Aan de slag met Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) voor meer informatie over het gebruik van het hulpprogramma. De volledige statistieken voor Azure Blockchain Service staan in de standaardstatistieken van De Azure Blockchain Service voor naamruimte.

U **de knooppuntdimensie** gebruiken bij het toevoegen van een filter of het splitsen van de metrische gegevens, die in principe metrische waarden per transactieknooppunten en validatorknooppunten van het ABS-lid biedt.

## <a name="analyze-log-data"></a>Logboekgegevens analyseren

Hier volgen enkele query's die u invoeren in de zoekbalk Logboek om u te helpen uw Azure Blockchain Service-leden te controleren. Deze query's werken met de [nieuwe taal](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Als u de foutvoorwaarden in de blockchain-toepassingslogboeken wilt opvragen, gebruikt u de onderstaande query:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Als u de foutvoorwaarden in de Blockchain-proxylogboeken wilt opvragen, gebruikt u de onderstaande query  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
U de beschikbare tijdsfilters in Azure-logboeken gebruiken om de query voor een specifiek tijdsbereik te filteren.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Gegevensverwijzing azure Blockchain Service-gegevens controleren  

In dit artikel vindt u een overzicht van logboek- en metrische gegevens die zijn verzameld om de prestaties en beschikbaarheid van Azure Blockchain Service te analyseren.  

### <a name="resource-logs"></a>Resourcelogboeken

Alle resourcelogs delen een algemeen schema op het hoogste niveau met weinig unieke eigenschappen die specifiek zijn voor de blockchain-service. U verwijzen naar het artikel [Top-level resource logs schema](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), details van de Azure Blockchain Service specifieke eigenschappen worden hieronder behandeld  

In de volgende tabel worden de eigenschappen voor Azure Blockchain-proxylogboeken weergegeven wanneer ze worden verzameld in Azure Monitor-logboeken of Azure Storage.  


| Naam van eigenschap  | Beschrijving |
|:---|:---|
| tijd | De datum en tijd (UTC) waarop de bewerking heeft plaatsgevonden. |
| Resourceid  | De Azure Blockchain Service-bron waarvoor logboeken zijn ingeschakeld.  |
| category  |Voor Azure Blockchain Service zijn de waarden mogelijk **proxylogs** en **Applicationlogs.** |
| operationName  | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd.   |
| Logboekniveau  | Azure Blockchain-service maakt standaard **informatief** logboekniveau mogelijk.   |
| KnooppuntLocatie  | Azure-regio waar het blockchain-lid wordt geïmplementeerd.  |
| BlockchainNodeName  | De naam van het knooppunt van het Azure Blockchain Service-lid waarop de bewerking wordt uitgevoerd.   |
| EthMethod EthMethod  | De methode, die wordt genoemd door het onderliggende blockchain-protocol, in Quorum, kan worden eth_sendTransactions, eth_getBlockByNumber enz.  |
| Agent  | De user agent die optreedt namens een gebruiker, zoals webbrowser Mozilla, Edge etc. Voorbeelden van de waarden zijn: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| Code   | HTTP-foutcodes. Meestal zijn 4XX en 5XX foutvoorwaarden.  |
| NodeHost  | De DNS-naam van het knooppunt.   |
| RequestMethodName | HTTP-methode genoemd, de mogelijke waarden hier zijn PUT voor lid maken, KRIJGEN voor het verkrijgen van details van bestaande lid, Verwijderen voor lid verwijderen, PATCH voor het bijwerken van lid.   |
| BlockchainMemberName  | De naam van Azure Blockchain Service-leden die door de gebruiker wordt verstrekt.  |
| Consortium | Naam van het consortium zoals verstrekt door de gebruiker.   |
| Externe  | Het IP van de client waar het verzoek vandaan komt.  |
| RequestSize  | Grootte van het verzoek in bytes.  |
| Aanvraagtijd  | De duur van de aanvraag in milliseconden.|




In de volgende tabel worden de eigenschappen voor Azure Blockchain-toepassingslogboeken weergegeven.


| Naam van eigenschap  | Beschrijving |
|:---|:---|
| tijd | De datum en tijd (UTC) waarop de bewerking heeft plaatsgevonden. |
| Resourceid  | De Azure Blockchain Service-bron waarvoor logboeken zijn ingeschakeld.|
| category  |Voor Azure Blockchain Service is de waarde mogelijk **proxylogs** en **Applicationlogs.**  |
| operationName  | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd.   |
| Logboekniveau  | Azure Blockchain-service maakt standaard **informatief** logboekniveau mogelijk.   |
| KnooppuntLocatie  | Azure-regio waar het blockchain-lid wordt geïmplementeerd.  |
| BlockchainNodeName  | De naam van het knooppunt van het Azure Blockchain Service-lid waarop de bewerking wordt uitgevoerd.   |
| BlockchainMessage    | Dit veld bevat het Blockchain-toepassingslogboek dat de gegevensvlaktelogboeken is. Voor ABS-Quorum zou dit Quorum logs hebben. Het heeft informatie over wat voor soort log invoer is het dat is informatieve, fout, waarschuwing en een string die meer informatie over de uitgevoerde actie geeft.   |
| TenantID    | De regiospecifieke tenant van de Azure Blockchain-service. Het formaat van https://westlake-rp-proddit veld is . <region>.cloudapp.azure.com waar de regio het Azure-gebied van het geïmplementeerde lid opgeeft.       |
| SourceSystem   | Het systeem vult de logboeken, in dit geval is het **Azure**.    |



### <a name="metrics"></a>Metrische gegevens

In de volgende tabellen worden de platformstatistieken weergegeven die zijn verzameld voor Azure Blockchain Service. Alle statistieken worden opgeslagen in de standaardstatistieken van De **Azure Blockchain Service** in de naamruimte.

Zie [Azure Monitor-ondersteunde statistieken](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)voor een lijst met alle door Azure Monitor ondersteunde statistieken (waaronder Azure Blockchain Service.

### <a name="blockchain-metrics"></a>Blockchain-statistieken

In de volgende tabel wordt de lijst met Blockchain-statistieken opgegeven die worden verzameld voor de Azure Blockchain Service-lidbron.


| Naam van meetwaarde | Eenheid  |  Aggregatietype| Beschrijving   |
|---|---|---|---|
| Transacties in behandeling   | Count  |  Average | Het aantal transacties dat wacht om te worden gedold.   |
| Verwerkte blokken   | Count  | Sum  |  Het aantal blokken dat in elk tijdsinterval wordt verwerkt. Momenteel is het blok grootte is 5 seconden, dus in een minuut elk knooppunt zal verwerken 12 blokken en 60 blokken in 5 minuten.   |
|Verwerkte transacties    | Count  | Sum  | Het aantal transacties dat in een blok wordt verwerkt.    |
|Transacties in wachtrijen    |  Count | Average  | Het aantal transacties dat niet onmiddellijk kan worden gedold. Het kan zijn omdat ze niet in orde zijn en de toekomstige transactie wacht op eerdere transacties. Of, het kan twee transacties hebben hetzelfde nummer slechts eenmaal gebruikt (nonce) en dezelfde gaswaarde, vandaar de tweede kan niet worden gewonnen.   |

### <a name="connection-metrics"></a>Verbindingsstatistieken  

In de volgende tabel worden de verschillende verbindingsstatistieken weergegeven die worden verzameld voor de Azure Blockchain Service-lidbron. Dit zijn NGINX proxy metrics.


| Naam van meetwaarde | Eenheid  |  Aggregatietype| Beschrijving |
|---|---|---|---|
| Geaccepteerde verbindingen   | Count  |  Sum | Het totale aantal geaccepteerde clientverbindingen.   |
| Actieve verbindingen  | Count  | Average  |  Het huidige aantal actieve clientverbindingen, waaronder Wachtverbindingen.    |
|Verwerkte verbindingen    | Count  | Sum  | Het totale aantal afgehandelde verbindingen. Over het algemeen is de parameterwaarde hetzelfde als geaccepteerde verbindingen, tenzij sommige resourcelimieten zijn bereikt.     |
|Afgehandelde aanvragen     |  Count | Sum  | Het totale aantal clientaanvragen.  |


### <a name="performance-metrics"></a>Metrische gegevens voor prestaties

In de volgende tabel worden de prestatiestatistieken weergegeven die worden verzameld voor elk van de knooppunten van de Azure Blockchain-lidbron.  


| Naam van meetwaarde | Eenheid  |  Aggregatietype| Beschrijving   |
|---|---|---|---|
| CPU-gebruikspercentage   | Percentage  |  Max. | Het percentage van het CPU-gebruik.     |
| IO Lees bytes   | Kilobytes   | Sum  |  De som van IO lees bytes over alle knooppunten van de blockchain-lidbron.      |
|IO-schrijfbytes     | Kilobytes   | Sum  | De som van IO schrijft bytes over alle knooppunten van de blockchain-lidbron.     |
|Geheugenlimiet       |  Gigabytes   | Average    | Maximaal geheugen beschikbaar voor het blockchain-proces per knooppunt. |
|Geheugengebruik     | Gigabytes  |  Average | De hoeveelheid geheugen die wordt gebruikt, is gemiddeld over alle knooppunten heen.  |
| Percentage geheugengebruik     | Percentage   | Average  |  Het percentage van het gebruikte geheugen gemiddeld over alle knooppunten.       |
|Opslaggebruik      | Gigabytes   | Average  | De gebruikte GB opslagruimte gemiddeld over alle knooppunten.       |


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) om blockchain-gegevens vast te leggen en te transformeren naar Azure Event Grid.
