---
title: Een persoonlijk model aanpassen in Video Indexer-Azure
titleSuffix: Azure Media Services
description: Dit artikel geeft een overzicht van wat een persoons model is in Video Indexer en hoe het kan worden aangepast.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "73838302"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Een persoonlijk model in Video Indexer aanpassen

Video Indexer ondersteunt beroemdheden herkenning in uw Video's. De functie voor beroemdheden-herkenning gaat over ongeveer 1.000.000 gezichten op basis van een gevraagde gegevens bron, zoals IMDB, Wikipedia en belangrijkste LinkedIn-invloeds. Gezichten die niet worden herkend door Video Indexer, worden nog steeds gedetecteerd, maar blijven ongewijzigd. Klanten kunnen aangepaste persoons modellen bouwen en Video Indexer in staat stellen om gezichten te herkennen die niet standaard worden herkend. Klanten kunnen deze persoons modellen bouwen door de naam van een persoon te koppelen aan afbeeldings bestanden van het gezicht van de persoon.  

Als uw account verantwoordelijk is voor verschillende gebruiks gevallen, kunt u profiteren van het maken van meerdere persoons modellen per account. Als de inhoud van uw account bijvoorbeeld in verschillende kanalen moet worden gesorteerd, wilt u mogelijk voor elk kanaal een afzonderlijk model maken. 

> [!NOTE]
> Elk persoons model ondersteunt Maxi maal 1.000.000 personen en elk account heeft een limiet van 50 persoons modellen. 

Zodra een model is gemaakt, kunt u het gebruiken door de model-ID van een specifiek persoons model op te geven bij het uploaden/indexeren of indexeren van een video. Een nieuw gezicht voor een video trainen: Hiermee wordt het specifieke aangepaste model bijgewerkt waarmee de video is gekoppeld. 

Als u de ondersteuning voor meerdere persoons modellen niet nodig hebt, moet u geen persoons model-ID aan uw video toewijzen tijdens het uploaden/indexeren of opnieuw indexeren. In dit geval gebruikt Video Indexer het standaard persoons model in uw account. 

U kunt de Video Indexer website gebruiken om gezichten te bewerken die in een video zijn gedetecteerd en om meerdere aangepaste persoons modellen in uw account te beheren, zoals wordt beschreven in het onderwerp [een persoonlijk model aanpassen met behulp van een website](customize-person-model-with-website.md) . U kunt ook de API gebruiken, zoals beschreven in [een persoons model aanpassen met behulp van api's](customize-person-model-with-api.md).
