---
title: bestand opnemen
description: bestand opnemen
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: e7aa2b4389fe60eed80b15aff04d6f7fcbc7b013
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "68968851"
---
| Resource | Doel | Harde limiet |
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
| Gelijktijdige synchronisatie sessies | V4-agent en hoger: De limiet is afhankelijk van de beschik bare systeem resources. <BR> V3-agent: Twee actieve synchronisatie sessies per processor of Maxi maal acht actieve synchronisatie sessies per server. | Ja

> [!Note]  
> Een Azure File Sync-eind punt kan worden geschaald naar de grootte van een Azure-bestands share. Als de maximale grootte van de Azure-bestands share is bereikt, kan de synchronisatie niet worden gebruikt.
