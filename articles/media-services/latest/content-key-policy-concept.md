---
title: Beleid voor inhoudssleutel in mediaservices - Azure | Microsoft Documenten
description: In dit artikel wordt uitgelegd wat beleid voor inhoudssleutel is en hoe deze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 85a9cad80156dc6ac40e78610c91805d485ff3df
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585997"
---
# <a name="content-key-policies"></a>Beleid voor inhoudssleutels

Met Media Services u uw live en on-demand content dynamisch versleuteld leveren met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde clients. 

Als u versleutelingsopties op uw stream wilt opgeven, moet u een [streamingbeleid](streaming-policy-concept.md) maken en deze koppelen aan uw [streaminglocator.](streaming-locators-concept.md) U maakt het [beleid voor inhoudssleutel](https://docs.microsoft.com/rest/api/media/contentkeypolicies) om te configureren hoe de inhoudssleutel (die veilige toegang biedt tot uw [assets)](assets-concept.md)wordt geleverd aan eindclients. U moet de vereisten (beperkingen) instellen voor het beleid voor inhoudssleutel waaraan moet worden voldaan om sleutels met de opgegeven configuratie aan clients te kunnen leveren. Het contentkey-beleid is niet nodig voor het wissen van streaming of downloaden. 

Meestal koppelt u uw contentkey-beleid aan uw [streaminglocator.](streaming-locators-concept.md) U ook het inhoudssleutelbeleid opgeven in een [streamingbeleid](streaming-policy-concept.md) (bij het maken van een aangepast streamingbeleid voor geavanceerde scenario's). 

## <a name="best-practices-and-considerations"></a>Aanbevolen procedures en overwegingen

> [!IMPORTANT]
> Bekijk de volgende aanbevelingen.

* U moet een beperkt aantal beleidsregels voor uw Media Service-account ontwerpen en deze opnieuw gebruiken voor uw streaminglocators wanneer dezelfde opties nodig zijn. Zie [Quota en limieten voor](limits-quotas-constraints.md)meer informatie .
* Contentkey-beleid is updateerbaar. Het kan tot 15 minuten duren voordat de caches voor het leveren van sleutels worden bijgewerkt en het bijgewerkte beleid worden opgehaald. 

   Door het beleid bij te werken, overschrijft u uw bestaande CDN-cache, wat problemen met het afspelen kan veroorzaken voor klanten die inhoud in de cache gebruiken.  
* We raden u aan geen nieuw contentkey-beleid voor elk item te maken. De belangrijkste voordelen van het delen van hetzelfde inhoudssleutelbeleid tussen assets die dezelfde beleidsopties nodig hebben, zijn:
   
   * Het is gemakkelijker om een klein aantal beleidsmaatregelen te beheren.
   * Als u het beleid voor inhoudssleutel wilt bijwerken, worden de wijzigingen vrijwel meteen van kracht op alle nieuwe licentieaanvragen.
* Als u een nieuw beleid moet maken, moet u een nieuwe streaminglocator voor het item maken.
* Het wordt aanbevolen om Media Services de inhoudssleutel automatisch te laten genereren. 

   Meestal zou u gebruik maken van een langlevende sleutel en controleren op het bestaan van de inhoud sleutel beleid met [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Als u de sleutel wilt krijgen, moet u een afzonderlijke actiemethode aanroepen om geheimen of referenties te krijgen, zie het voorbeeld dat volgt.

## <a name="example"></a>Voorbeeld

Gebruik , zoals in `GetPolicyPropertiesWithSecretsAsync`de [tekentoets ophalen, in het bestaande beleidsvoorbeeld](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) om bij de sleutel te komen.

## <a name="filtering-ordering-paging"></a>Filteren, bestellen, paging

Zie [Het filteren, bestellen, paging van mediaservices entiteiten](entities-overview.md).

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Eigenschappen van het beleid met `Datetime` inhoudssleutel van het type zijn altijd in UTC-indeling.
* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen

* [AES-128 dynamische versleuteling en de key delivery service gebruiken](protect-with-aes128.md)
* [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
* [CoderenHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
