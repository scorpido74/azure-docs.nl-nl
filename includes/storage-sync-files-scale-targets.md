---
title: Include-bestand
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466876"
---
| Resource | Doel | Harde limiet |
|----------|--------------|------------|
| Opslag synchronisatie services per regio | 100 Storage Sync Services | Yes |
| Synchronisatie groepen per opslag synchronisatie service | 200 synchronisatie groepen | Yes |
| Geregistreerde servers per opslag synchronisatie service | 99-servers | Yes |
| Cloud eindpunten per synchronisatie groep | 1 Cloud eindpunt | Yes |
| Server eindpunten per synchronisatie groep | 50-server eindpunten | No |
| Server eindpunten per server | 30 server eindpunten | Yes |
| Bestandssysteem objecten (directory's en bestanden) per synchronisatie groep | 100.000.000-objecten | No |
| Maximum aantal bestandssysteem objecten (directory's en bestanden) in een map | 5.000.000-objecten | Yes |
| Maxi maal object (directory's en bestanden) security descriptor grootte | 64 KiB | Yes |
| Bestandsgrootte | 100 GiB | No |
| Minimale bestands grootte voor een trapsgewijs gelaagd bestand | V9: op basis van de grootte van het bestandssysteem cluster (dubbele grootte van het bestandssysteem cluster). Als de cluster grootte van het bestands systeem bijvoorbeeld 4kb is, wordt de minimale bestands grootte 8 KB.<br> V8 en ouder: 64 KiB  | Yes |

> [!Note]  
> Een Azure File Sync-eind punt kan worden geschaald naar de grootte van een Azure-bestands share. Als de maximale grootte van de Azure-bestands share is bereikt, kan de synchronisatie niet worden gebruikt.
