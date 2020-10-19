---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 47eae616ffc62e42448da23fb02152dae17aa548
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116781"
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
| Minimale bestandsgrootte om een bestand gelaagd te maken | V9 en nieuwer: Op basis van de grootte van het bestandssysteemcluster (dubbele bestandssysteemclustergrootte). Als de bestandssysteemclustergrootte bijvoorbeeld 4 kb is, is de minimale bestandsgrootte 8 kb.<br> V8 en ouder: 64 KiB  | Ja |

> [!Note]  
> Een Azure File Sync-eindpunt kan tot de grootte van een Azure-bestandsshare worden opgeschaald. Als de limiet van de Azure-bestandsshare, kan er niet meer worden gesynchroniseerd.
