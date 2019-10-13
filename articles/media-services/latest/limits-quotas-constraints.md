---
title: Quota en beperkingen in Azure Media Services v3 | Microsoft Docs
description: In dit onderwerp worden de quota's en beperkingen in Azure Media Services v3 beschreven
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.openlocfilehash: 819548d784e5cba9fcec6b2110137d91bf28e03d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296911"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quota en beperkingen in Azure Media Services v3

In dit artikel worden de quota's en beperkingen in Azure Media Services v3 beschreven.

| Bron | Standaardlimiet | 
| --- | --- | 
| Assets per Azure Media Services-account | 1\.000.000|
| Dynamisch-manifestfilters|100|
| JobInputs per taak | 50 (vast)|
| JobOutputs per taak | 20 (vast) |
| TransformOutputs in een trans formatie | 20 (vast) |
| Bestanden per JobInput|10 (vast)|
| Bestandsgrootte| In sommige scenario's geldt een limiet voor de maximale bestands grootte die wordt ondersteund voor verwerking in Media Services. <sup>i</sup> |
| Taken per Media Services-account | 500.000 <sup>(2)</sup> (vast)|
| Live gebeurtenissen per Azure Media Services-account |5|
| Media Services accounts in één abonnement | 25 (vast) |
| Live outputs per live gebeurtenis |3 <sup>(3)</sup> |
| Maximale duur van live uitvoer | 25 uur |
| Opslagaccounts | 100<sup>(4)</sup> (vast) |
| Streaming-eind punten (gestopt of actief) per Media Services-account|2 (vast)|
| Beleid voor streaming | 100 <sup>(5)</sup> |
| Trans formaties per Media Services account | 100 (vast)|
| Unieke streaming-Locators die zijn gekoppeld aan een asset in één keer | 100<sup>(6)</sup> (vast) |
| Opties per inhouds sleutel beleid |30 | 
| Licenties per maand voor elk van de DRM-typen op Media Services key delivery service per account|1\.000.000|

<sup>1</sup> de maximale grootte die voor één BLOB wordt ondersteund, is momenteel Maxi maal 5 TB in Azure Blob Storage. Er zijn extra limieten van toepassing op Media Services op basis van de VM-grootten die worden gebruikt door de service. De limiet voor de grootte is van toepassing op de bestanden die u uploadt en ook de bestanden die worden gegenereerd als gevolg van de verwerking van Media Services (code ring of analyse). Als uw bron bestand groter is dan 260-GB, zal uw taak waarschijnlijk mislukken. 

De volgende tabel bevat de limieten voor de gereserveerde media-eenheden S1, S2 en S3. Als het bron bestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt de coderings taak. Als u resources van 4 KB met een lange duur wilt coderen, moet u gereserveerde S3-media-eenheden gebruiken om de benodigde prestaties te verzorgen. Als u onbewerkte inhoud hebt die groter is dan de limiet van 260 GB op de gereserveerde S3-media-eenheden, neemt u contact met ons op amshelp@microsoft.com voor mogelijke oplossingen om uw scenario te ondersteunen.

|Type gereserveerde media-eenheid   |Maximale invoer grootte (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> dit aantal bevat taken in de wachtrij, voltooid, actief en geannuleerd. Het bevat geen verwijderde taken. 

Alle taak records in uw account die ouder zijn dan 90 dagen worden automatisch verwijderd, zelfs als het totale aantal records lager is dan het maximum quotum. 

<sup>drie</sup> live uitvoer bewerkingen beginnen bij het maken en stoppen wanneer deze worden verwijderd.

<sup>4</sup> de opslag accounts moeten afkomstig zijn uit hetzelfde Azure-abonnement.

<sup>5</sup> als u een aangepast [streaming-beleid](https://docs.microsoft.com/rest/api/media/streamingpolicies)gebruikt, moet u een beperkt aantal beleids regels voor uw media service-account ontwerpen en ze opnieuw gebruiken voor uw StreamingLocators wanneer dezelfde versleutelings opties en protocollen nodig zijn. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

<sup>6</sup> streaming-locators zijn niet ontworpen voor het beheren van toegangs beheer per gebruiker. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven.

## <a name="support-ticket"></a>Ondersteunings ticket

Voor bronnen die niet zijn hersteld, kunt u vragen of de quota's moeten worden gegenereerd door een [ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te openen. Neem gedetailleerde informatie op in de aanvraag voor de gewenste quotum wijzigingen, use-case scenario's en regio's vereist. <br/>Maak **geen** extra Azure Media Services-accounts in een poging om de limieten te verhogen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](media-services-overview.md)
