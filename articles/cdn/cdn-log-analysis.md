---
title: Azure CDN gebruiks patronen analyseren | Microsoft Docs
description: In dit artikel worden de verschillende typen analyse rapporten beschreven die beschikbaar zijn voor Azure CDN producten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253608"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Gebruikspatronen van Azure CDN analyseren

Nadat u CDN hebt ingeschakeld voor uw toepassing, kunt u het CDN-gebruik controleren, de status van uw levering controleren en mogelijke problemen oplossen. Azure CDN biedt deze mogelijkheden op de volgende manieren: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Kern analyse via Diagnostische logboeken van Azure

Kern analyse is beschikbaar voor CDN-eind punten voor alle prijs categorieën. Met Azure Diagnostics-logboeken kan kern analyse worden geëxporteerd naar Azure Storage, Event hubs of Azure Monitor Logboeken. Azure Monitor-logboeken bieden een oplossing met grafieken die door de gebruiker kunnen worden geconfigureerd en aangepast. Zie [Azure Diagnostic logs](cdn-azure-diagnostic-logs.md)(Engelstalig) voor meer informatie over Azure Diagnostische logboeken.

## <a name="verizon-core-reports"></a>Verizon-kern rapporten

Als Azure CDN gebruiker met een **Azure CDN standaard van Verizon** of **Azure CDN Premium van Verizon** -profiel, kunt u Verizon-kern rapporten weer geven in de aanvullende portal van Verizon. Verizon-kern rapporten zijn toegankelijk via de optie **beheren** van de Azure Portal en bieden diverse grafieken en weer gaven. Zie voor meer informatie [kern rapporten van Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Aangepaste Verizon-rapporten

Als Azure CDN gebruiker met een **Azure CDN standaard van Verizon** of **Azure CDN Premium van Verizon** -profiel, kunt u aangepaste Verizon-rapporten weer geven in de aanvullende portal van Verizon. Aangepaste Verizon-rapporten zijn toegankelijk via de optie **beheren** van de Azure Portal. Op de pagina met aangepaste Verizon-rapporten vindt u het aantal treffers of gegevens die zijn overgedragen voor elke rand CName die deel uitmaakt van een Azure CDN profiel. De gegevens kunnen worden gegroepeerd op HTTP-respons code of de cache status voor een wille keurige periode. Zie [aangepaste rapporten van Verizon](cdn-verizon-custom-reports.md)voor meer informatie.

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium van Verizon-rapporten

Met **Azure CDN Premium van Verizon**kunt u ook toegang krijgen tot de volgende rapporten:
   * [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
   * [Realtime statistieken](cdn-real-time-stats.md)
   * [Prestaties van edge-knooppunt](cdn-edge-performance.md)

