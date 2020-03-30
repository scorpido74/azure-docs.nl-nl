---
title: Azure Analysis Services-bron- en objectlimieten | Microsoft Documenten
description: In dit artikel worden resource- en objectlimieten beschreven voor een Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573201"
---
# <a name="analysis-services-resource-and-object-limits"></a>Resource- en objectlimieten voor Analysis Services

In dit artikel worden resource- en modelobjectlimieten beschreven.

## <a name="tier-limits"></a>Niveaulimieten

Zie de [prijspagina](https://azure.microsoft.com/pricing/details/analysis-services/)Azure Analysis Services voor QPU- en geheugenlimieten voor ontwikkelaars, basis- en standaardlagen.

## <a name="object-limits"></a>Objectlimieten

Deze grenzen zijn theoretisch. De prestaties zullen bij lagere aantallen afnemen.

|Object|Maximale maten/getallen|  
|------------|----------------------------|  
|Databases in een instantie|16.000|  
|Gecombineerd aantal tabellen en kolommen in een database|16.000|  
|Rijen in een tabel|Onbeperkt<br /><br /> **Waarschuwing:** Met de beperking dat geen enkele kolom in de tabel meer dan 1.999.999.997 verschillende waarden kan hebben.|  
|Hiërarchieën in een tabel|15,999|  
|Niveaus in een hiërarchie|15,999|  
|Relaties|8,000|  
|Sleutelkolommen in alle tabel|15,999|  
|Metingen in tabellen|2^31-1 = 2.147.483.647|  
|Cellen die worden geretourneerd door een query|2^31-1 = 2.147.483.647|  
|Recordgrootte van de bronquery|64 K|  
|Lengte van objectnamen|512 tekens|  


