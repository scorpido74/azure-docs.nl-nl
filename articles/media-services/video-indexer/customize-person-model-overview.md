---
title: Een persoonsmodel aanpassen in Video-indexer - Azure
titleSuffix: Azure Media Services
description: In dit artikel vindt u een overzicht van wat een persoonsmodel is in Video-indexer en hoe u dit aanpassen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838302"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Een persoonsmodel aanpassen in videoindexer

Video Indexer ondersteunt herkenning van beroemdheden in je video's. De functie voor herkenning van beroemdheden omvat ongeveer een miljoen gezichten op basis van veelgevraagde gegevensbronnen zoals IMDB, Wikipedia en top LinkedIn-beïnvloeders. Gezichten die niet worden herkend door Video Indexer worden nog steeds gedetecteerd, maar worden niet bij naam genoemd. Klanten kunnen aangepaste persoonsmodellen maken en Video-indexer inschakelen om gezichten te herkennen die standaard niet worden herkend. Klanten kunnen deze persoonsmodellen bouwen door de naam van een persoon te koppelen aan afbeeldingsbestanden van het gezicht van de persoon.  

Als uw account zich richt op verschillende use-cases, u profiteren van de mogelijkheid om meerdere persoonsmodellen per account te maken. Als de inhoud in je account bijvoorbeeld in verschillende kanalen moet worden gesorteerd, kun je voor elk kanaal een apart persoonsmodel maken. 

> [!NOTE]
> Elk persoonsmodel ondersteunt maximaal 1 miljoen mensen en elk account heeft een limiet van 50 persoonsmodellen. 

Zodra een model is gemaakt, u het gebruiken door de model-ID van een specifiek persoonsmodel op te geven bij het uploaden/indexeren of opnieuw indexeren van een video. Als u een nieuw gezicht voor een video traint, wordt het specifieke aangepaste model bijgewerkt waarmee de video is gekoppeld. 

Als u de ondersteuning voor het model met meerdere personen niet nodig hebt, wijst u geen persoonsmodel-id toe aan uw video bij het uploaden/indexeren of opnieuw indexeren. In dit geval gebruikt Video Indexer het standaardpersoonsmodel in uw account. 

U de website Video-indexer gebruiken om gezichten te bewerken die in een video zijn gedetecteerd en om meerdere aangepaste persoonsmodellen in uw account te beheren, zoals beschreven in het [model Een persoon aanpassen met behulp van een websiteonderwerp.](customize-person-model-with-website.md) U de API ook gebruiken, zoals beschreven in [Een persoonsmodel aanpassen met API's](customize-person-model-with-api.md).
