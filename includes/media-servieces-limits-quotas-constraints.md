---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: d6ff823951e6474a35a514036f02c0aabb17bf01
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82997738"
---
> [!NOTE]
> Voor bronnen die niet zijn opgelost, opent u een ondersteunings ticket om te vragen om een toename van de quota's. Maak geen aanvullende Azure Media Services accounts in een poging om hogere limieten te verkrijgen.

### <a name="account-limits"></a>Account limieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Media Services accounts in één abonnement | 25 (vast) |

### <a name="asset-limits"></a>Activa limieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Activa per Media Services account | 1.000.000|

### <a name="storage-media-limits"></a>Opslag limieten (media)

| Resource | Standaardlimiet | 
| --- | --- | 
| Bestandsgrootte| In sommige scenario's geldt een limiet voor de maximale bestands grootte die wordt ondersteund voor verwerking in Media Services. <sup>i</sup> |
| Opslagaccounts | 100<sup>(2)</sup> (vast) |

<sup>1</sup> de maximale grootte die voor één BLOB wordt ondersteund, is momenteel Maxi maal 5 TB in Azure Blob Storage. Er zijn extra limieten van toepassing op Media Services op basis van de VM-grootten die worden gebruikt door de service. De limiet voor de grootte is van toepassing op de bestanden die u uploadt en ook de bestanden die worden gegenereerd als gevolg van de verwerking van Media Services (code ring of analyse). Als uw bron bestand groter is dan 260-GB, zal uw taak waarschijnlijk mislukken. 

De volgende tabel bevat de limieten voor de gereserveerde media-eenheden S1, S2 en S3. Als het bron bestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt de coderings taak. Als u resources van 4 KB met een lange duur wilt coderen, moet u gereserveerde S3-media-eenheden gebruiken om de benodigde prestaties te verzorgen. Als u onbewerkte inhoud hebt die groter is dan de limiet van 260 GB op de gereserveerde S3-media-eenheden, opent u een ondersteunings ticket.

|Type gereserveerde media-eenheid|Maximale invoer grootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> de opslag accounts moeten afkomstig zijn uit hetzelfde Azure-abonnement.

### <a name="jobs-encoding--analyzing-limits"></a>Limieten voor taken (coderings &-analyse)

| Resource | Standaardlimiet | 
| --- | --- | 
| Taken per Media Services-account | 500.000 <sup>(3)</sup> (vast)|
| Taak invoer per taak | 50 (vast)|
| Taak uitvoer per taak | 20 (vast) |
| Trans formaties per Media Services account | 100 (vast)|
| Uitvoer van een trans formatie transformeren | 20 (vast) |
| Bestanden per taak invoer|10 (vast)|

<sup>3</sup> dit aantal bevat taken in de wachtrij, voltooid, actief en geannuleerd. Het bevat geen verwijderde taken. 

Alle taak records in uw account die ouder zijn dan 90 dagen worden automatisch verwijderd, zelfs als het totale aantal records lager is dan het maximum quotum. 

### <a name="live-streaming-limits"></a>Limieten voor live streamen

| Resource | Standaardlimiet | 
| --- | --- | 
| Live-gebeurtenissen <sup>(4)</sup> per Media Services-account |5|
| Live outputs per live gebeurtenis |3 <sup>(5)</sup> |
| Maximale duur van live uitvoer | [Grootte van het DVR-venster](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> Zie [vergelijking van live-gebeurtenis typen en beperkingen](../articles/media-services/latest/live-event-types-comparison.md)voor meer informatie over beperkingen van Live-gebeurtenissen.

<sup>5</sup> actieve uitvoer bewerkingen beginnen bij het maken en stoppen wanneer deze worden verwijderd.

### <a name="packaging--delivery-limits"></a>Leverings limieten voor verpakking &

| Resource | Standaardlimiet | 
| --- | --- | 
| Streaming-eind punten (gestopt of actief) per Media Services-account|2 (vast)|
| Dynamisch-manifestfilters|100|
| Beleid voor streaming | 100 <sup>(6)</sup> |
| Unieke streaming-Locators die zijn gekoppeld aan een asset in één keer | 100<sup>(7)</sup> (vast) |

<sup>6</sup> wanneer u een aangepast [streaming-beleid](https://docs.microsoft.com/rest/api/media/streamingpolicies)gebruikt, moet u een beperkt aantal beleids regels voor uw media service-account ontwerpen en ze opnieuw gebruiken voor uw StreamingLocators wanneer dezelfde versleutelings opties en protocollen nodig zijn. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

<sup>7</sup> streaming-locators zijn niet ontworpen voor het beheren van toegangs beheer per gebruiker. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven.

### <a name="protection-limits"></a>Beveiligings limieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Opties per inhouds sleutel beleid |30 | 
| Licenties per maand voor elk van de DRM-typen op Media Services key delivery service per account|1.000.000|

### <a name="support-ticket"></a>Ondersteunings ticket

Voor bronnen die niet zijn hersteld, kunt u vragen of de quota's moeten worden gegenereerd door een [ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te openen. Neem gedetailleerde informatie op in de aanvraag voor de gewenste quotum wijzigingen, use-case scenario's en regio's vereist. <br/>Maak **geen** extra Azure Media Services-accounts in een poging om de limieten te verhogen.
