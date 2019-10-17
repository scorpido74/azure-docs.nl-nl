---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391609"
---
| Bron | Kiezen | Harde limiet |
|----------|--------------|------------|
| Opslag synchronisatie services per regio | 20 opslag synchronisatie Services | Ja |
| Synchronisatie groepen per opslag synchronisatie service | 100 synchronisatie groepen | Ja |
| Geregistreerde servers per opslag synchronisatie service | 99-servers | Ja |
| Cloud eindpunten per synchronisatie groep | 1 Cloud eindpunt | Ja |
| Server eindpunten per synchronisatie groep | 50-server eindpunten | Nee |
| Server eindpunten per server | 30 server eindpunten | Ja |
| Bestandssysteem objecten (directory's en bestanden) per synchronisatie groep | 50.000.000-objecten | Nee |
| Maximum aantal bestandssysteem objecten (directory's en bestanden) in een map | 5\.000.000-objecten | Ja |
| Maxi maal object (directory's en bestanden) security descriptor grootte | 64 KiB | Ja |
| Bestandsgrootte | 100 GiB | Nee |
| Minimale bestands grootte voor een trapsgewijs gelaagd bestand | 64 KiB | Ja |
| Gelijktijdige synchronisatie sessies | V4-agent en hoger: de limiet varieert op basis van de beschik bare systeem resources. <BR> V3-agent: twee actieve synchronisatie sessies per processor of Maxi maal acht actieve synchronisatie sessies per server. | Ja

> [!Note]  
> Een Azure File Sync-eind punt kan worden geschaald naar de grootte van een Azure-bestands share. Als de maximale grootte van de Azure-bestands share is bereikt, kan de synchronisatie niet worden gebruikt.
