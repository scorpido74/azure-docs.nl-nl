---
title: Quota en beperkingen in Azure Media Services v3 | Microsoft Documenten
description: In dit onderwerp worden quota en beperkingen beschreven in Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888424"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quota en beperkingen in Azure Media Services v3

In dit artikel worden quota en beperkingen in Azure Media Services v3 beschreven.

| Resource | Standaardlimiet | 
| --- | --- | 
| Assets per Azure Media Services-account | 1.000.000|
| Dynamisch-manifestfilters|100|
| JobInputs per taak | 50 (vast)|
| JobOutputs per taak | 20 (vast) |
| TransformOutputs in een transformatie | 20 (vast) |
| Bestanden per JobInput|10 (vast)|
| Bestandsgrootte| In sommige scenario's is er een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. <sup>(1)</sup> |
| Vacatures per Media Services-account | 500.000 <sup>(2)</sup> (vast)|
| Live gebeurtenissen per Azure Media Services-account |5|
| Media Services-accounts in één abonnement | 25 (vast) |
| Live-uitgangen per live-evenement |<sup>3.</sup> |
| Maximale duur van de live-uitvoer | 25 uur |
| Opslagaccounts | 100<sup>(4)</sup> (vast) |
| Streaming eindpunten (gestopt of uitgevoerd) per Media Services-account|2 (vast)|
| Beleid voor streaming | 100 <sup>(5)</sup> |
| Transformeert per Media Services-account | 100 (vast)|
| Unieke streaming locators in verband met een asset in één keer | 100<sup>(6)</sup> (vast) |
| Opties per inhoudssleutelbeleid |30 | 
| Licenties per maand voor elk van de DRM-typen op de hoofdleveringsservice van Media Services per account|1.000.000|

<sup>1</sup> De maximale grootte die wordt ondersteund voor één blob is momenteel maximaal 5 TB in Azure Blob Storage. Er gelden extra limieten in Media Services op basis van de VM-formaten die door de service worden gebruikt. De groottelimiet is van toepassing op de bestanden die u uploadt en ook op de bestanden die worden gegenereerd als gevolg van verwerking van Media Services (coderen of analyseren). Als uw bronbestand groter is dan 260 GB, mislukt uw taak waarschijnlijk. 

In de volgende tabel worden de limieten weergegeven voor de mediagereserveerde eenheden S1, S2 en S3. Als uw bronbestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt uw coderingstaak. Als u bronnen met een 4K-resolutie van lange duur codeert, moet u gereserveerde S3-media-eenheden gebruiken om de vereiste prestaties te bereiken. Als u 4K-inhoud hebt die groter is dan de limiet van 260 GB voor de gereserveerde S3-media, opent u een ondersteuningsticket.

|Media gereserveerd eenheidstype   |Maximale invoergrootte (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Dit nummer omvat in de wachtrij staande, voltooide, actieve en geannuleerde taken. Het bevat geen verwijderde vacatures. 

Elke record in uw account ouder dan 90 dagen wordt automatisch verwijderd, zelfs als het totale aantal records lager is dan het maximumquotum. 

<sup>3</sup> Live-uitgangen beginnen bij het maken en stoppen wanneer ze worden verwijderd.

<sup>4</sup> De opslagaccounts moeten van hetzelfde Azure-abonnement zijn.

<sup>5</sup> Wanneer u een aangepast [streamingbeleid gebruikt,](https://docs.microsoft.com/rest/api/media/streamingpolicies)moet u een beperkt aantal van dergelijke beleidsregels voor uw Media Service-account ontwerpen en deze opnieuw gebruiken voor uw StreamingLocators wanneer dezelfde versleutelingsopties en -protocollen nodig zijn. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

<sup>6</sup> Streaming Locators zijn niet ontworpen voor het beheren van toegangsbeheer per gebruiker. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven.

## <a name="support-ticket"></a>Ondersteuningsticket

Voor resources die niet zijn vastgesteld, u vragen om de quota's te verhoogd, door het openen van een [support ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Neem gedetailleerde informatie op in de aanvraag over de gewenste quotawijzigingen, gebruiksscenario's en vereiste regio's. <br/>Maak **geen** extra Azure Media Services-accounts in een poging om de limieten te verhogen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](media-services-overview.md)
