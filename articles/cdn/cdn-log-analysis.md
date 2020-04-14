---
title: Azure CDN-gebruikspatronen analyseren | Microsoft Documenten
description: In dit artikel worden de verschillende typen analyserapporten beschreven die beschikbaar zijn voor Azure CDN-producten.
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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253608"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Gebruikspatronen van Azure CDN analyseren

Nadat u CDN hebt ingeschakeld voor uw toepassing, u het CDN-gebruik controleren, de status van uw levering controleren en mogelijke problemen oplossen. Azure CDN biedt deze mogelijkheden op de volgende manieren: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Kernanalyses via diagnostische azure-logboeken

Core analytics is beschikbaar voor CDN-eindpunten voor alle prijsniveaus. Met Azure-diagnostische logboeken kunnen kernanalyses worden geëxporteerd naar Azure-opslag-, gebeurtenishubs- of Azure-monitorlogboeken. Azure Monitor logs biedt een oplossing met grafieken die gebruikersconfigureerbaar en aanpasbaar zijn. Zie [Diagnostische azure-logboeken voor](cdn-azure-diagnostic-logs.md)meer informatie over diagnostische logboeken voor Azure.

## <a name="verizon-core-reports"></a>Verizon kernrapporten

Als Azure CDN-gebruiker met een **Azure CDN-standaard van Verizon** of Azure **CDN Premium vanuit** verizon-profiel, u de kernrapporten van Verizon bekijken in de aanvullende portal van Verizon. Verizon-kernrapporten zijn toegankelijk via de optie **Beheren** vanuit de Azure-portal en bieden een verscheidenheid aan grafieken en weergaven. Zie [Core Reports van Verizon](cdn-analyze-usage-patterns.md)voor meer informatie.

## <a name="verizon-custom-reports"></a>Aangepaste rapporten van Verizon

Als Azure CDN-gebruiker met een **Azure CDN-standaard van Verizon** of Azure **CDN Premium vanuit** verizon-profiel, u aangepaste verizon-rapporten bekijken in de aanvullende portal van Verizon. Aangepaste rapporten van Verizon zijn toegankelijk via de optie **Beheren** vanuit de Azure-portal. Op de pagina Aangepaste rapporten van Verizon wordt het aantal treffers of gegevens weergegeven dat wordt overgedragen voor elke cname van de rand die deel uitmaakt van een Azure CDN-profiel. De gegevens kunnen worden gegroepeerd op HTTP-antwoordcode of cachestatus gedurende een bepaalde periode. Zie [Aangepaste rapporten van Verizon](cdn-verizon-custom-reports.md)voor meer informatie.

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium van Verizon-rapporten

Met **Azure CDN Premium van Verizon**hebt u ook toegang tot de volgende rapporten:
   * [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
   * [Realtime statistieken](cdn-real-time-stats.md)
   * [Prestaties van edge-knooppunt](cdn-edge-performance.md)

