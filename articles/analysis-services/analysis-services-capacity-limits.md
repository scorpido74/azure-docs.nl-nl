---
title: Limieten voor resources en objecten Azure Analysis Services | Microsoft Docs
description: Beschrijft Azure Analysis Services limieten voor resources en objecten.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fe43602c66af72357e16822ee9d4b5a741d3f86
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298704"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limieten voor resources en objecten Analysis Services

In dit artikel worden de limieten voor resource-en model objecten beschreven.

## <a name="tier-limits"></a>Laag limieten

Zie de [pagina met Azure Analysis Services prijzen](https://azure.microsoft.com/pricing/details/analysis-services/)voor QPU-en geheugen limieten voor de lagen ontwikkelaar, basis en standaard.

## <a name="object-limits"></a>Object limieten

Deze limieten zijn theoretisch. Prestaties worden verminderd bij lagere getallen.

|Object|Maximale grootte/aantal|  
|------------|----------------------------|  
|Data bases in een exemplaar|16.000|  
|Gecombineerd aantal tabellen en kolommen in een Data Base|16.000|  
|Rijen in een tabel|Onbeperkt<br /><br /> **Waarschuwing:** Met de beperking dat geen enkele kolom in de tabel meer dan 1.999.999.997 afzonderlijke waarden kan hebben.|  
|Hiërarchieën in een tabel|15.999|  
|Niveaus in een hiërarchie|15.999|  
|Relaties|8,000|  
|Sleutel kolommen in alle tabellen|15.999|  
|Metingen in tabellen|2 ^ 31-1 = 2.147.483.647|  
|Cellen die door een query zijn geretourneerd|2 ^ 31-1 = 2.147.483.647|  
|Record grootte van de bron query|64 K|  
|Lengte van object namen|512 tekens|  


