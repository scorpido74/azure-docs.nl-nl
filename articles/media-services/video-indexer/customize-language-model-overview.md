---
title: Een taalmodel aanpassen in Video-indexer - Azure
titleSuffix: Azure Media Services
description: In dit artikel vindt u een overzicht van wat een taalmodel is in Video-indexer en hoe u dit aanpassen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838324"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Een taalmodel aanpassen met videoindexer

Video Indexer ondersteunt automatische spraakherkenning door integratie met de Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). U het taalmodel aanpassen door aanpassingstekst te uploaden, namelijk tekst uit het domein waarvan u de woordenschat wilt waaraan u wilt dat de engine zich aanpast. Zodra u uw model traint, worden nieuwe woorden in de aanpassingstekst herkend, uitgaande van de standaarduitspraak, en het taalmodel leert nieuwe waarschijnlijke reeksen woorden. Custom Language modellen worden ondersteund voor Engels, Spaans, Frans, Duits, Italiaans, Chinees (Vereenvoudigd), Japans, Russisch, Braziliaans Portugees, Hindi en Koreaans. 

Laten we een woord nemen dat zeer specifiek is, zoals 'Kubernetes' (in de context van Azure Kubernetes-service), als voorbeeld. Aangezien het woord nieuw is voor Video Indexer, wordt het erkend als "communities". Je moet het model trainen om het te herkennen als "Kubernetes". In andere gevallen bestaan de woorden, maar het taalmodel verwacht niet dat ze in een bepaalde context worden weergegeven. 'Containerservice' is bijvoorbeeld geen reeks van twee woorden die een niet-gespecialiseerd taalmodel als een specifieke set woorden zou herkennen.

U hebt de optie om woorden zonder context te uploaden in een lijst in een tekstbestand. Dit wordt beschouwd als gedeeltelijke aanpassing. U ook tekstbestanden(en) documentatie of zinnen met betrekking tot uw inhoud uploaden voor een betere aanpassing.

U de API's voor video-indexeren of de website gebruiken om aangepaste taalmodellen te maken en te bewerken, zoals beschreven in onderwerpen in het gedeelte [Volgende stappen](#next-steps) van dit onderwerp.

## <a name="best-practices-for-custom-language-models"></a>Aanbevolen procedures voor aangepaste taalmodellen

Video Indexer leert op basis van waarschijnlijkheden van woordcombinaties, dus om het beste te leren:

* Geef genoeg echte voorbeelden van zinnen zoals ze zouden worden gesproken.
* Zet slechts één zin per regel, niet meer. Anders zal het systeem waarschijnlijkheden over zinnen leren.
* Het is goed om een woord te zetten als een zin om het woord te stimuleren tegen anderen, maar het systeem leert het beste van volledige zinnen.
* Bij de invoering van nieuwe woorden of afkortingen, indien mogelijk, geven zo veel voorbeelden van gebruik in een volledige zin om zo veel mogelijk context te geven aan het systeem.
* Probeer verschillende aanpassingsopties te plaatsen en te zien hoe ze voor u werken.
* Vermijd herhaling van exact dezelfde zin meerdere keren. Het kan leiden tot bias ten opzichte van de rest van de input.
* Vermijd het opnemen van ongewone symbolen (~, # @ % &) als ze zullen worden weggegooid. De zinnen waarin ze verschijnen, worden ook weggegooid.
* Vermijd het aanbrengen van te grote ingangen, zoals honderdduizenden zinnen, omdat dit het effect van het stimuleren zal verdunnen.

## <a name="next-steps"></a>Volgende stappen

[Taalmodel aanpassen met API's](customize-language-model-with-api.md)

[Taalmodel aanpassen met behulp van de website](customize-language-model-with-website.md)
