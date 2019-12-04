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
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795611"
---
| Bron | Kiezen | Harde limiet |
|----------|--------------|------------|
| Opslag synchronisatie services per regio | 20 opslag synchronisatie Services | Ja |
| Synchronisatie groepen per opslag synchronisatie service | 100 synchronisatie groepen | Ja |
| Geregistreerde servers per opslag synchronisatie service | 99-servers | Ja |
| Cloud eindpunten per synchronisatie groep | 1 Cloud eindpunt | Ja |
| Server eindpunten per synchronisatie groep | 50-server eindpunten | Nee |
| Server eindpunten per server | 30 server eindpunten | Ja |
| Bestandssysteem objecten (directory's en bestanden) per synchronisatie groep | 100.000.000-objecten | Nee |
| Maximum aantal bestandssysteem objecten (directory's en bestanden) in een map | 5\.000.000-objecten | Ja |
| Maxi maal object (directory's en bestanden) security descriptor grootte | 64 KiB | Ja |
| Bestandsgrootte | 100 GiB | Nee |
| Minimale bestands grootte voor een trapsgewijs gelaagd bestand | V9: op basis van de grootte van het bestandssysteem cluster (dubbele grootte van het bestandssysteem cluster). Als de cluster grootte van het bestands systeem bijvoorbeeld 4kb is, wordt de minimale bestands grootte 8 KB.<br> V8 en ouder: 64 KiB  | Ja |

> [!Note]  
> Een Azure File Sync-eind punt kan worden geschaald naar de grootte van een Azure-bestands share. Als de maximale grootte van de Azure-bestands share is bereikt, kan de synchronisatie niet worden gebruikt.
