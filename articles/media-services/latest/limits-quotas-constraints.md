---
title: Quota en limieten in Azure Media Services
description: In dit onderwerp worden de quota en limieten in Microsoft Azure Media Services beschreven.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b1836b1d0dc69a2a0b186a54974895eb0d8cd91a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265487"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Quota en limieten Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel vindt u een aantal van de meest voorkomende Microsoft Azure Media Services limieten, ook wel quota's genoemd.

> [!NOTE]
> Voor bronnen die niet zijn opgelost, opent u een ondersteunings ticket om te vragen om een toename van de quota's. Maak geen aanvullende Azure Media Services accounts in een poging om hogere limieten te verkrijgen.

## <a name="account-limits"></a>Account limieten

| Resource | Standaardlimiet |
| --- | --- |
| [Media Services accounts](media-services-account-concept.md) in één abonnement | 25 (vast) |

## <a name="asset-limits"></a>Activa limieten

| Resource | Standaardlimiet |
| --- | --- |
| [Activa](assets-concept.md) per Media Services account | 1.000.000|

## <a name="storage-limits"></a>Opslag limieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Bestandsgrootte| In sommige scenario's geldt een limiet voor de maximale bestands grootte die wordt ondersteund voor verwerking in Media Services. <sup>i</sup> |
| [Opslagaccounts](storage-account-concept.md) | 100<sup>(2)</sup> (vast) |

<sup>1</sup> de maximale grootte die voor één BLOB wordt ondersteund, is momenteel Maxi maal 5 TB in Azure Blob Storage. Er zijn extra limieten van toepassing op Media Services op basis van de VM-grootten die worden gebruikt door de service. De limiet voor de grootte is van toepassing op de bestanden die u uploadt en ook de bestanden die worden gegenereerd als gevolg van de verwerking van Media Services (code ring of analyse). Als uw bron bestand groter is dan 260-GB, zal uw taak waarschijnlijk mislukken. 

De volgende tabel bevat de limieten voor de gereserveerde media-eenheden S1, S2 en S3. Als het bron bestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt de coderings taak. Als u resources van 4 KB met een lange duur wilt coderen, moet u gereserveerde S3-media-eenheden gebruiken om de benodigde prestaties te verzorgen. Als u onbewerkte inhoud hebt die groter is dan de limiet van 260 GB op de gereserveerde S3-media-eenheden, opent u een ondersteunings ticket.

|Type gereserveerde media-eenheid|Maximale invoer grootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> de opslag accounts moeten afkomstig zijn uit hetzelfde Azure-abonnement.

## <a name="jobs-encoding--analyzing-limits"></a>Limieten voor taken (coderings &-analyse)

| Resource | Standaardlimiet | 
| --- | --- | 
| [Taken](transforms-jobs-concept.md) per Media Services-account | 500.000 <sup>(3)</sup> (vast)|
| Taak invoer per taak | 50 (vast)|
| Taak uitvoer per taak | 20 (vast) |
| [Trans formaties](transforms-jobs-concept.md) per Media Services account | 100 (vast)|
| Uitvoer van een trans formatie transformeren | 20 (vast) |
| Bestanden per taak invoer|10 (vast)|

<sup>3</sup> dit aantal bevat taken in de wachtrij, voltooid, actief en geannuleerd. Het bevat geen verwijderde taken. 

Alle taak records in uw account die ouder zijn dan 90 dagen worden automatisch verwijderd, zelfs als het totale aantal records lager is dan het maximum quotum. 

## <a name="live-streaming-limits"></a>Limieten voor live streamen

| Resource | Standaardlimiet | 
| --- | --- | 
| [Live-gebeurtenissen](live-events-outputs-concept.md) <sup>(4)</sup> per Media Services-account |5|
| Live outputs per live gebeurtenis |3 <sup>(5)</sup> |
| Maximale duur van live uitvoer | [Grootte van het DVR-venster](live-event-cloud-dvr.md) |

<sup>4</sup> Zie [vergelijking van live-gebeurtenis typen en limieten](live-event-types-comparison.md)voor meer informatie over limieten voor Live-gebeurtenissen.

<sup>5</sup> actieve uitvoer bewerkingen beginnen bij het maken en stoppen wanneer deze worden verwijderd.

## <a name="packaging--delivery-limits"></a>Leverings limieten voor verpakking &

| Resource | Standaardlimiet |
| --- | --- |
| [Streaming-eind punten](streaming-endpoint-concept.md) (gestopt of actief) per Media Services-account|2 |
| Premium-streaming-eenheden | 10 |
| [Dynamisch-manifestfilters](filters-dynamic-manifest-overview.md)|100|
| [Streaming-beleid](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Unieke [streaming-Locators](streaming-locators-concept.md) die zijn gekoppeld aan een asset in één keer | 100<sup>(7)</sup> (vast) |

<sup>6</sup> wanneer u een aangepast [streaming-beleid](/rest/api/media/streamingpolicies)gebruikt, moet u een beperkt aantal beleids regels voor uw media service-account ontwerpen en ze opnieuw gebruiken voor uw StreamingLocators wanneer dezelfde versleutelings opties en protocollen nodig zijn. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

<sup>7</sup> streaming-locators zijn niet ontworpen voor het beheren van toegangs beheer per gebruiker. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven.

## <a name="protection-limits"></a>Beveiligings limieten

| Resource | Standaardlimiet | 
| --- | --- | 
| Opties per [inhouds sleutel beleid](content-key-policy-concept.md) |30 | 
| Licenties per maand voor elk van de DRM-typen op Media Services key delivery service per account|1.000.000|

## <a name="support-ticket"></a>Ondersteunings ticket

Voor bronnen die niet zijn hersteld, kunt u vragen of de quota's moeten worden gegenereerd door een [ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te openen. Neem gedetailleerde informatie op in de aanvraag voor de gewenste quotum wijzigingen, use-case scenario's en regio's vereist. <br/>Maak **geen** extra Azure Media Services-accounts in een poging om de limieten te verhogen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](media-services-overview.md)
