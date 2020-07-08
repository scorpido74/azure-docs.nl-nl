---
title: Regio's die ondersteuning bieden voor Beschikbaarheidszones in azure
description: Als u Maxi maal beschik bare en flexibele toepassingen in azure wilt maken, Beschikbaarheidszones u fysiek afzonderlijke locaties bieden die u kunt gebruiken om uw resources uit te voeren.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 07/06/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: eec92672801d33290df1690d8cb17e79d4626192
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042047"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regio's die ondersteuning bieden voor Beschikbaarheidszones in azure

Beschikbaarheidszones is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Zie [regio's en Beschikbaarheidszones in azure](az-overview.md)voor meer informatie over Beschikbaarheidszones.

## <a name="services-support-by-region"></a>Services ondersteunen per regio

In deze sectie vindt u de Azure-Services en-regio's die ondersteuning bieden voor Beschikbaarheidszones.

Services die beschikbaar zijn in elke regio, samen met een gepland schema voor Beschik baarheid, kunnen worden gevonden in [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/).

|                                 |Noord- en Zuid-Amerika |              |           |           | Europa |              |          |              | Azië en Stille Oceaan |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |VS - centraal|VS - oost|VS - oost 2|VS - west 2|Frankrijk - centraal|Europa - noord|Verenigd Koninkrijk Zuid|Europa -west|Japan - oost|Azië - zuidoost|Australië - oost|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |                |
| Linux Virtual Machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Windows Virtual Machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Virtuele-machineschaalsets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Azure App Service omgevingen ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Zone-redundante opslag          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| **Netwerken**                     |            |              |           |           |                |              |          |             |            |                |                |
| Standaard-IP-adres        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| ExpressRoute-gateway   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Databases**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (preview-versie) | &#10003;       | &#10003; (preview-versie)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache voor Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    | &#10003;  |
| **Analyse**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |  &#10003;              |
| **Integratie**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (alleen Premium-laag) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |  &#10003;              |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identiteit**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

Azure biedt ook Beschikbaarheidszones ondersteuning in de volgende regio's:

- VS (overheid) - Virginia
- Australië - oost
- Zuid-Afrika - noord
- VS - zuid-centraal
- Canada - midden

Neem contact op met uw micro soft-verkoop-of klant vertegenwoordiger voor meer informatie over de ondersteuning van Beschikbaarheidszones in deze vijf regio's.

## <a name="next-steps"></a>Volgende stappen

- [Regio's en Beschikbaarheidszones in azure](az-overview.md)
