---
title: Een aangepast eind punt maken | Microsoft Docs
description: In dit artikel leert u hoe u een aangepast eind punt kunt configureren om te meten met uw Internet Analyzer-resource.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 44d570746a54154a2f8d3c7a200c761a59b7422b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501923"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Aangepaste eind punten meten om te evalueren in uw Internet Analyzer-tests 

In dit artikel wordt beschreven hoe u een aangepast eind punt instelt om te meten als onderdeel van uw Internet Analyzer-tests. Aangepaste eind punten helpen bij het evalueren van on-premises workloads, workloads die worden uitgevoerd op andere cloud providers en aangepaste Azure-configuraties.  Het vergelijken van twee aangepaste eind punten in een test is mogelijk als één eind punt een Azure-resource is. Zie het [overzicht](internet-analyzer-overview.md)voor meer informatie over Internet Analyzer. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Internet Analyzer-resource instelt en de optie ' aangepast eind punt ' selecteert. Internet Analyzer veronderstelt dat uw aangepaste eind punt toegankelijk is via internet. Zie [een Internet Analyzer-resource maken](internet-analyzer-create-test-portal.md)voor meer informatie.


## <a name="create-custom-endpoint"></a>Aangepast eind punt maken

1. Down load [hier](https://fpc.msedge.net/apc/trans.gif)een transparante test afbeelding met één pixel. Deze afbeelding met één pixel is het activum dat door de client-java script wordt opgehaald om de prestaties te meten.
2. Implementeer in uw aangepaste webtoepassing de test installatie kopie in een openbaar toegankelijk pad. Het pad moet werken via HTTPS. 
3. Kopieer de volledige aangepaste eind punt-URL (bijvoorbeeld https://contoso.com/test/trans.gif) in het veld voor het aangepaste eind punt tijdens het maken van de test.

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Internet analyse](internet-analyzer-faq.md)

