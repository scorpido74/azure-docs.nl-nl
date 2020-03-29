---
title: Azure Media Services codeerfoutcodes | Microsoft Documenten
description: In dit onderwerp worden foutcodes weergegeven die kunnen worden geretourneerd als er een fout is opgetreden tijdens de uitvoering van de coderingstaak..
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64709525"
---
# <a name="encoding-error-codes"></a>Foutcodes voor codering

In de volgende tabel worden foutcodes weergegeven die kunnen worden geretourneerd als er een fout is opgetreden tijdens de uitvoering van de coderingstaak.  Als u foutgegevens in uw .NET-code wilt opvragen, gebruikt u de klasse [ErrorDetails.](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) Als u foutgegevens in uw REST-code wilt opvragen, gebruikt u de [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail.Code | Mogelijke oorzaken voor fouten |
| --- | --- |
| Onbekend |Onbekende fout tijdens het uitvoeren van de taak |
| ErrorDownloadingInputAssetMalformedContent |Categorie fouten die fouten in het downloaden van invoeractiva omvat, zoals slechte bestandsnamen, bestanden met nullengte, onjuiste indelingen enzovoort. |
| FoutdownloadenInputAssetServiceStoring |Categorie fouten die problemen aan de servicezijde dekt - bijvoorbeeld netwerk- of opslagfouten tijdens het downloaden. |
| ErrorParsingConfiguration |Categorie fouten waarbij \<de taak cref="MediaTask.PrivateData"/> (configuratie) niet geldig is, bijvoorbeeld de configuratie geen geldige systeemvoorinstelling is of ongeldige XML bevat. |
| ErrorExecutingTaskMalformedContent |Categorie fouten tijdens de uitvoering van de taak waarbij problemen in de invoermediabestanden mislukken. |
| ErrorExecutingTaskUnsupportedFormat |Categorie fouten waarbij de mediaprocessor de geleverde bestanden niet kan verwerken - media-indeling wordt niet ondersteund of komt niet overeen met de configuratie. Bijvoorbeeld als u probeert een audio-only uitvoer te produceren van een asset die alleen video heeft |
| Foutverwerkingstaak |Categorie van andere fouten die de mediaprocessor tegenkomt tijdens de verwerking van de taak die geen verband houden met inhoud. |
| FoutUploadoutputAsset |Categorie fouten bij het uploaden van het uitvoerelement |
| FoutAnnulerentaak |Categorie fouten om fouten te dekken wanneer u de taak probeert te annuleren |
| Tijdelijke fout |Categorie fouten om tijdelijke problemen te dekken (bijv. tijdelijke netwerkproblemen met Azure Storage) |

Als u hulp wilt krijgen van het **Media Services-team,** opent u een [ondersteuningsticket.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
* [Geavanceerde coderingstaken uitvoeren door voorinstellingen van Media Encoder Standard aan te werken](media-services-custom-mes-presets-with-dotnet.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
