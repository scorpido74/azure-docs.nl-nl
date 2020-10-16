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
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84466876"
---
| Resource | Doel | Harde limiet |
|----------|--------------|------------|
| Opslagsynchronisatieservices per regio | 100 opslagsynchronisatieservices | Ja |
| Synchronisatiegroepen per opslagsynchronisatieservice | 200 synchronisatiegroepen | Ja |
| Geregistreerde servers per opslagsynchronisatieservice | 99 servers | Ja |
| Cloudeindpunten per synchronisatiegroep | 1 cloudeindpunt | Ja |
| Servereindpunten per synchronisatiegroep | 50 servereindpunten | Nee |
| Servereindpunten per server | 30 servereindpunten | Ja |
| Bestandssysteemobjecten (mappen en bestanden) per synchronisatiegroep | 100 miljoen objecten | Nee |
| Maximumaantal bestandssysteemobjecten (mappen en bestanden) in een map | 5 miljoen objecten | Ja |
| Maximumgrootte beveiligingsbeschrijving (mappen en bestanden) van objecten | 64 KiB | Ja |
| Bestandsgrootte | 100 GiB | Nee |
| Minimale bestandsgrootte om een bestand gelaagd te maken | V9: Op basis van de grootte van het bestandssysteemcluster (dubbele bestandssysteemclustergrootte). Als de bestandssysteemclustergrootte bijvoorbeeld 4 kb is, is de minimale bestandsgrootte 8 kb.<br> V8 en ouder: 64 KiB  | Ja |

> [!Note]  
> Een Azure File Sync-eindpunt kan tot de grootte van een Azure-bestandsshare worden opgeschaald. Als de limiet van de Azure-bestandsshare, kan er niet meer worden gesynchroniseerd.
