---
title: Quota en beperkingen in Azure Media Services
description: In dit onderwerp worden quota en beperkingen in Microsoft Azure Media Services beschreven.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: 6fb2f8f9172533a2c7f4aa03e99bd08e16a1f1dc
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545917"
---
# <a name="azure-media-services-quotas-and-limits"></a>Azure Media Services-quota en -limieten

In dit artikel worden enkele van de meest voorkomende Microsoft Azure Media Services-limieten weergegeven, die ook wel quota worden genoemd.

> [!NOTE]
> Voor resources die niet zijn vastgesteld, opent u een ondersteuningsticket om een verhoging van de quota te vragen. Maak geen extra Azure Media Services-accounts in een poging hogere limieten te verkrijgen.

## <a name="account-limits"></a>Accountlimieten

| Resource | Standaardlimiet | 
| --- | --- | 
| [Media Services-accounts](media-services-account-concept.md) in één abonnement | 25 (vast) |

## <a name="asset-limits"></a>Activalimieten

| Resource | Standaardlimiet | 
| --- | --- | 
| [Assets](assets-concept.md) per Media Services-account | 1.000.000|

## <a name="storage-limits"></a>Opslaglimieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Bestandsgrootte| In sommige scenario's is er een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. <sup>(1)</sup> |
| [Opslagaccounts](storage-account-concept.md) | 100<sup>(2)</sup> (vast) |

<sup>1</sup> De maximale grootte die wordt ondersteund voor één blob is momenteel maximaal 5 TB in Azure Blob Storage. Er gelden extra limieten in Media Services op basis van de VM-formaten die door de service worden gebruikt. De groottelimiet is van toepassing op de bestanden die u uploadt en ook op de bestanden die worden gegenereerd als gevolg van verwerking van Media Services (coderen of analyseren). Als uw bronbestand groter is dan 260 GB, mislukt uw taak waarschijnlijk. 

In de volgende tabel worden de limieten weergegeven voor de mediagereserveerde eenheden S1, S2 en S3. Als uw bronbestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt uw coderingstaak. Als u bronnen met een 4K-resolutie van lange duur codeert, moet u gereserveerde S3-media-eenheden gebruiken om de vereiste prestaties te bereiken. Als u 4K-inhoud hebt die groter is dan de limiet van 260 GB voor de gereserveerde S3-media, opent u een ondersteuningsticket.

|Media gereserveerd eenheidstype|Maximale invoergrootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> De opslagaccounts moeten van hetzelfde Azure-abonnement zijn.

## <a name="jobs-encoding--analyzing-limits"></a>Limieten voor taken (codering & analyseren)

| Resource | Standaardlimiet | 
| --- | --- | 
| [Vacatures](transforms-jobs-concept.md) per Media Services-account | 500.000 <sup>(3)</sup> (vast)|
| Jobinput per job per job | 50 (vast)|
| Taakuitvoer per taak | 20 (vast) |
| [Transformeert](transforms-jobs-concept.md) per Media Services-account | 100 (vast)|
| Uitvoer omzetten in een transformatie | 20 (vast) |
| Bestanden per taakinvoer|10 (vast)|

<sup>3</sup> Dit nummer omvat in de wachtrij staande, voltooide, actieve en geannuleerde taken. Het bevat geen verwijderde vacatures. 

Elke record in uw account ouder dan 90 dagen wordt automatisch verwijderd, zelfs als het totale aantal records lager is dan het maximumquotum. 

## <a name="live-streaming-limits"></a>Limieten voor live streaming

| Resource | Standaardlimiet | 
| --- | --- | 
| [Live Events](live-events-outputs-concept.md) <sup>(4)</sup> per Media Services-account |5|
| Live-uitgangen per live-evenement |3 <sup>.5.</sup> |
| Maximale duur van de live-uitvoer | 25 uur |

<sup>4</sup> Zie Vergelijking en beperkingen van [typen live-evenementen](live-event-types-comparison.md)voor gedetailleerde informatie over beperkingen van live-evenementen.

<sup>5</sup> Live-uitgangen beginnen bij het maken en stoppen wanneer ze worden verwijderd.

## <a name="packaging--delivery-limits"></a>Verpakkings- & leveringslimieten

| Resource | Standaardlimiet | 
| --- | --- | 
| [Streaming eindpunten](streaming-endpoint-concept.md) (gestopt of uitgevoerd) per Media Services-account|2 (vast)|
| [Dynamisch-manifestfilters](filters-dynamic-manifest-overview.md)|100|
| [Beleid voor streaming](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Unieke [streaming locators](streaming-locators-concept.md) in verband met een asset in één keer | 100<sup>(7)</sup> (vast) |

<sup>6</sup> Wanneer u een aangepast [streamingbeleid gebruikt,](https://docs.microsoft.com/rest/api/media/streamingpolicies)moet u een beperkt aantal van dergelijke beleidsregels voor uw Media Service-account ontwerpen en deze opnieuw gebruiken voor uw StreamingLocators wanneer dezelfde versleutelingsopties en -protocollen nodig zijn. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

<sup>7</sup> Streaming Locators zijn niet ontworpen voor het beheren van toegangsbeheer per gebruiker. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven.

## <a name="protection-limits"></a>Beschermingslimieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Opties per [inhoudssleutelbeleid](content-key-policy-concept.md) |30 | 
| Licenties per maand voor elk van de DRM-typen op de hoofdleveringsservice van Media Services per account|1.000.000|

## <a name="support-ticket"></a>Ondersteuningsticket

Voor resources die niet zijn vastgesteld, u vragen om de quota's te verhoogd, door het openen van een [support ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Neem gedetailleerde informatie op in de aanvraag over de gewenste quotawijzigingen, gebruiksscenario's en vereiste regio's. <br/>Maak **geen** extra Azure Media Services-accounts in een poging om de limieten te verhogen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](media-services-overview.md)
