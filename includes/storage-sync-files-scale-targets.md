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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795611"
---
| Resource | Doel | Harde limiet |
|----------|--------------|------------|
| Opslagsynchronisatieservices per regio | 20 Opslagsynchronisatieservices | Ja |
| Synchronisatiegroepen per opslagsynchronisatieservice | 100 synchronisatiegroepen | Ja |
| Geregistreerde servers per Storage Sync Service | 99 servers | Ja |
| Cloudeindpunten per synchronisatiegroep | 1 eindpunt in de cloud | Ja |
| Servereindpunten per synchronisatiegroep | 50 servereindpunten | Nee |
| Servereindpunten per server | 30 servereindpunten | Ja |
| Bestandssysteemobjecten (mappen en bestanden) per synchronisatiegroep | 100 miljoen objecten | Nee |
| Maximaal aantal bestandssysteemobjecten (mappen en bestanden) in een map | 5 miljoen objecten | Ja |
| Maximale object (mappen en bestanden) beveiligingsdescriptor grootte | 64 KiB | Ja |
| Bestandsgrootte | 100 GiB | Nee |
| Minimale bestandsgrootte voor een bestand dat moet worden gelaagd | V9: Gebaseerd op clustergrootte van het bestandssysteem (dubbele clustergrootte van bestandssystemen). Als de clustergrootte van het bestandssysteem bijvoorbeeld 4 kb is, is de minimale bestandsgrootte 8kb.<br> V8 en ouder: 64 KiB  | Ja |

> [!Note]  
> Een Azure File Sync-eindpunt kan worden opgeschaald naar de grootte van een Azure-bestandsshare. Als de limiet voor de grootte van azure-bestanden wordt bereikt, kan de synchronisatie niet werken.
