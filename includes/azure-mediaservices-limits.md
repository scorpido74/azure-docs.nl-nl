---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334861"
---
>[!NOTE]
>Voor bronnen die niet zijn opgelost, opent u een ondersteunings ticket om te vragen om een toename van de quota's. Maak geen aanvullende Azure Media Services accounts in een poging om hogere limieten te verkrijgen.

| Resource | Limiet | 
| --- | --- | 
| Azure Media Services accounts in één abonnement | 25 (vast) |
| Gereserveerde media-eenheden per Media Services account |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Taken per Media Services-account | 50.000<sup>2</sup> |
| Gekoppelde taken per taak | 30 (vast) |
| Activa per Media Services account | 1.000.000|
| Activa per taak | 50 |
| Activa per taak | 100 |
| Unieke locators die aan één activum tegelijk zijn gekoppeld | 5<sup>4</sup> |
| Live channels per Media Services-account |5|
| Programma's met de status Gestopt per kanaal |50|
| Programma's met de status Wordt uitgevoerd per kanaal |3|
| Streaming-eind punten die worden gestopt of uitgevoerd per Media Services-account|2|
| Streaming-eenheden per streaming-eindpunt |10 |
| Opslagaccounts | 1.000<sup>5</sup> (vast) |
| Beleidsregels | 1.000.000<sup>6</sup> |
| Bestandsgrootte| In sommige scenario's geldt een limiet voor de maximale bestands grootte die wordt ondersteund voor verwerking in Media Services. <sup>7</sup> |

<sup>1</sup> Als u het type wijzigt, bijvoorbeeld van S2 naar S1, worden de maximum limieten voor gereserveerde eenheden opnieuw ingesteld.

<sup>2</sup> Dit aantal omvat taken in de wachtrij, voltooid, actief en geannuleerd. Het bevat geen verwijderde taken. U kunt oude taken verwijderen met behulp van **IJob. Delete** of het **verwijderen** van een HTTP-aanvraag.

Met ingang van 1 april 2017 worden alle taak records in uw account die ouder zijn dan 90 dagen automatisch verwijderd, samen met de bijbehorende taak registraties. Automatisch verwijderen vindt plaats, zelfs als het totale aantal records lager is dan het maximum quotum. Als u de taak-en taak gegevens wilt archiveren, gebruikt u de code die wordt beschreven in [assets beheren met de Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Wanneer u een aanvraag voor het maken van een lijst met taak entiteiten maakt, worden er Maxi maal 1.000 taken per aanvraag geretourneerd. Als u alle ingediende taken wilt bijhouden, gebruikt u de boven-of sla query's zoals beschreven in [query opties](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))van het OData-systeem.

<sup>4</sup> Locators zijn niet ontworpen voor het beheren van toegangs beheer per gebruiker. Gebruik Digital Rights Management (DRM)-oplossingen om verschillende toegangs rechten voor afzonderlijke gebruikers te bieden. Zie [uw inhoud beveiligen met Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md)voor meer informatie.

<sup>5</sup> De opslag accounts moeten afkomstig zijn uit hetzelfde Azure-abonnement.

<sup>6</sup> Er is een limiet van 1.000.000 beleids regels voor verschillende Media Services-beleids regels. Een voor beeld is voor het beleid van de Locator of het ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Gebruik dezelfde beleids-ID als u altijd dezelfde dagen en toegangs machtigingen gebruikt. Zie [assets beheren met de Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)voor meer informatie en een voor beeld.

<sup>7</sup> De maximale grootte die wordt ondersteund voor één blob is momenteel Maxi maal 5 TB in Azure Blob Storage. Er zijn extra limieten van toepassing op Media Services op basis van de VM-grootten die worden gebruikt door de service. De limiet voor de grootte is van toepassing op de bestanden die u uploadt en ook de bestanden die worden gegenereerd als gevolg van de verwerking van Media Services (code ring of analyse). Als uw bron bestand groter is dan 260-GB, zal uw taak waarschijnlijk mislukken. 

De volgende tabel bevat de limieten voor de gereserveerde media-eenheden S1, S2 en S3. Als het bron bestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt de coderings taak. Als u resources van 4 KB met een lange duur wilt coderen, moet u gereserveerde S3-media-eenheden gebruiken om de benodigde prestaties te verzorgen. Als u onbewerkte inhoud hebt die groter is dan de limiet van 260 GB op de gereserveerde S3-media-eenheden, opent u een ondersteunings ticket.

|Type gereserveerde media-eenheid    |Maximale invoer grootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
