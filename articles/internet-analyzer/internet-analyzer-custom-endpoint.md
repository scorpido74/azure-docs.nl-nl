---
title: Een aangepast eindpunt maken | Microsoft Documenten
description: In dit artikel leert u hoe u een aangepast eindpunt configureert om te meten met uw Internet Analyzer-bron.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76713095"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Aangepaste eindpunten meten om te evalueren in uw Internet Analyzer-tests 

In dit artikel wordt uitgelegd hoe u een aangepast eindpunt instelt om te meten als onderdeel van uw Internet Analyzer-tests. Aangepaste eindpunten helpen bij het evalueren van on-premises workloads, workloads die worden uitgevoerd op andere cloudproviders en aangepaste Azure-configuraties.  Het is mogelijk om twee aangepaste eindpunten in één test te vergelijken als één eindpunt een Azure-bron is. Zie het [overzicht](internet-analyzer-overview.md)voor meer informatie over Internet Analyzer. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Internet Analyzer-bron instelt en selecteer de optie Aangepast eindpunt. Internet Analyzer gaat ervan uit dat uw aangepaste eindpunt internettoegankelijk is. Zie [Een internetanalyzerbron maken](internet-analyzer-create-test-portal.md)voor meer informatie.


## <a name="create-custom-endpoint"></a>Aangepast eindpunt maken

1. Download [hier](https://fpc.msedge.net/apc/trans.gif)een transparante testafbeelding van één pixel. Deze afbeelding met één pixel is de asset die de client JavaScript ophaalt om de prestaties te meten.
2. Implementeer de testafbeelding in uw aangepaste webtoepassing in een openbaar toegankelijk pad. Het pad moet via HTTPS werken. 
3. Kopieer de volledige aangepaste URL van https://contoso.com/test/trans.gif) het eindpunt (bijvoorbeeld naar het aangepaste eindpuntveld tijdens het maken van de test.

## <a name="next-steps"></a>Volgende stappen

Lees de [veelgestelde vragen over internetanalyse](internet-analyzer-faq.md)

