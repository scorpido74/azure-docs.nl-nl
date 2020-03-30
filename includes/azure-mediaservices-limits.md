---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334861"
---
>[!NOTE]
>Voor resources die niet zijn vastgesteld, opent u een ondersteuningsticket om een verhoging van de quota te vragen. Maak geen extra Azure Media Services-accounts in een poging hogere limieten te verkrijgen.

| Resource | Limiet | 
| --- | --- | 
| Azure Media Services-accounts in één abonnement | 25 (vast) |
| Gereserveerde mediaeenheden per Media Services-account |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Vacatures per Media Services-account | 50.000<sup>2</sup> |
| Gekoppelde taken per taak | 30 (vast) |
| Assets per Media Services-account | 1.000.000|
| Activa per taak | 50 |
| Activa per taak | 100 |
| Unieke locators die aan één activum tegelijk zijn gekoppeld | 5<sup>4.</sup> |
| Live kanalen per Media Services-account |5|
| Programma's met de status Gestopt per kanaal |50|
| Programma's met de status Wordt uitgevoerd per kanaal |3|
| Streaming eindpunten die worden gestopt of uitgevoerd per Media Services-account|2|
| Streaming-eenheden per streaming-eindpunt |10 |
| Opslagaccounts | 1.000<sup>5</sup> (vast) |
| Beleidsregels | 1.000.000<sup>6</sup> |
| Bestandsgrootte| In sommige scenario's is er een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. <sup>7.</sup> |

<sup>1.</sup> Als u bijvoorbeeld het type wijzigt van S2 naar S1, worden de maximaal gereserveerde eenheidslimieten opnieuw ingesteld.

<sup>2.</sup> Dit nummer omvat in de wachtrij staande, voltooide, actieve en geannuleerde taken. Het bevat geen verwijderde taken. U oude taken verwijderen met **IJob.Delete** of het **HTTP-verzoek verwijderen.**

Vanaf 1 april 2017 wordt elke taakrecord in uw account ouder dan 90 dagen automatisch verwijderd, samen met de bijbehorende taakrecords. Automatische verwijdering vindt plaats, zelfs als het totale aantal records lager is dan het maximumquotum. Als u de taak- en taakgegevens wilt archiveren, gebruikt u de code die is beschreven in [Assets beheren met de Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3.</sup> Wanneer u een verzoek indient om taakentiteiten op te nemen, worden maximaal 1.000 vacatures per aanvraag geretourneerd. Als u alle ingediende taken wilt bijhouden, gebruikt u de opties boven- of overslaan die zijn beschreven in [de queryopties van het OData-systeem.](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))

<sup>4.</sup> Locators zijn niet ontworpen voor het beheren van toegangsbeheer per gebruiker. Gebruik drm-oplossingen (Digital Rights Management) om verschillende toegangsrechten aan individuele gebruikers te geven. Zie [Uw inhoud beveiligen met Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md)voor meer informatie.

<sup>5.</sup> De opslagaccounts moeten van hetzelfde Azure-abonnement zijn.

<sup>6.</sup> Er is een limiet van 1.000.000 beleidsregels voor verschillende Media Services-beleidsregels. Een voorbeeld is voor het Locator-beleid of ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Als u altijd dezelfde dagen en toegangsmachtigingen gebruikt, gebruikt u dezelfde beleids-ID. Zie [Assets beheren met de Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)voor informatie en een voorbeeld.

<sup>7.</sup> De maximale grootte die wordt ondersteund voor één blob is momenteel maximaal 5 TB in Azure Blob Storage. Er gelden extra limieten in Media Services op basis van de VM-formaten die door de service worden gebruikt. De groottelimiet is van toepassing op de bestanden die u uploadt en ook op de bestanden die worden gegenereerd als gevolg van verwerking van Media Services (coderen of analyseren). Als uw bronbestand groter is dan 260 GB, mislukt uw taak waarschijnlijk. 

In de volgende tabel worden de limieten weergegeven voor de mediagereserveerde eenheden S1, S2 en S3. Als uw bronbestand groter is dan de limieten die in de tabel zijn gedefinieerd, mislukt uw coderingstaak. Als u bronnen met een 4K-resolutie van lange duur codeert, moet u gereserveerde S3-media-eenheden gebruiken om de vereiste prestaties te bereiken. Als u 4K-inhoud hebt die groter is dan de limiet van 260 GB voor de gereserveerde S3-media, opent u een ondersteuningsticket.

|Media gereserveerd eenheidstype    |Maximale invoergrootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
