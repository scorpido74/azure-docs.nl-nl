---
title: Codeer fout codes Azure Media Services | Microsoft Docs
description: Dit onderwerp bevat een lijst met fout codes die kunnen worden geretourneerd als er een fout is opgetreden tijdens het uitvoeren van de coderings taak.
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
ms.openlocfilehash: 6e56dbe1d1236a567ed6f59acfcca325a6c9ee7e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269026"
---
# <a name="encoding-error-codes"></a>Foutcodes voor codering

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

De volgende tabel bevat de fout codes die kunnen worden geretourneerd als er een fout is opgetreden tijdens het uitvoeren van de coderings taak.  Als u fout gegevens in uw .NET-code wilt ophalen, gebruikt u de [Error Details](/previous-versions/azure/jj126075(v=azure.100)) -klasse. Als u details van de fout in uw REST code wilt weer geven, gebruikt u de [ErrorDetail](/rest/api/media/operations/errordetail) -rest API.

| ErrorDetail. code | Mogelijke oorzaken voor fout |
| --- | --- |
| Onbekend |Onbekende fout tijdens het uitvoeren van de taak |
| ErrorDownloadingInputAssetMalformedContent |Categorie fouten die fouten bevatten bij het downloaden van invoer-assets, zoals onjuiste bestands namen, bestanden met een lengte van nul, onjuiste indelingen, enzovoort. |
| ErrorDownloadingInputAssetServiceFailure |Categorie fouten die betrekking hebben op problemen aan de kant van de service, zoals netwerk-of opslag fouten tijdens het downloaden. |
| ErrorParsingConfiguration |Fout categorie waarbij taak \<see cref="MediaTask.PrivateData"/> (configuratie) ongeldig is, bijvoorbeeld omdat de configuratie geen geldige systeem instelling is of ongeldige XML bevat. |
| ErrorExecutingTaskMalformedContent |Categorie van fouten tijdens de uitvoering van de taak waarbij problemen in de invoer media bestanden een fout veroorzaken. |
| ErrorExecutingTaskUnsupportedFormat |Categorie fouten waarbij de media processor de opgegeven bestanden niet kan verwerken. de media-indeling wordt niet ondersteund of komt niet overeen met de configuratie. U kunt bijvoorbeeld proberen een alleen audio-uitvoer te maken van een Asset met alleen video |
| ErrorProcessingTask |Categorie van andere fouten die de media processor tegen komt tijdens de verwerking van de taak die niet gerelateerd is aan inhoud. |
| ErrorUploadingOutputAsset |Categorie van fouten bij het uploaden van het uitvoer activum |
| ErrorCancelingTask |Fout met betrekking tot fouten bij het annuleren van de taak |
| TransientError |Categorie van fouten om tijdelijke problemen te bedekken (bijvoorbeeld tijdelijke netwerk problemen met Azure Storage) |

Open een [ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om hulp te krijgen van het **Media Services** -team.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwante artikelen:
* [Geavanceerde coderings taken uitvoeren door Media Encoder Standard voor instellingen aan te passen](media-services-custom-mes-presets-with-dotnet.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
