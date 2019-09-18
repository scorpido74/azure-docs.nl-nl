---
title: Servicemetertoewijzing voor gratis Azure-account
description: Informatie over servicemetertoewijzing voor services die zijn opgenomen in een gratis account.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8022c065d73aafc53d3dcb77e79c3e6320e0ce39
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490408"
---
# <a name="understand-free-service-to-meter-mapping"></a>Gratis servicemetertoewijzing begrijpen

Elke Azure-service maakt gebruik van meters, waarvan het Azure-factuursysteem gebruikmaakt om gebruikers services in rekening te brengen. Voor een beter begrip van gratis servicegebruik gaan we eens kijken naar de servicemetertoewijzing voor de services. Zie [Gratis services maken met een gratis Azure-account](billing-create-free-services-included-free-account.md) voor informatie over het maken van gratis services.

## <a name="service-to-meter-mapping-for-eligible-services"></a>Servicemetertoewijzing voor in aanmerking komende services

|    Service   | Meternaam op Azure-portal | Meternaam in het gebruiks-bestand/API | Id van de meter |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux VM | Rekenuren - Standard_B1 VM | Rekenuren - Gratis | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | Rekenuren - Standard_B1 VM (Windows) | Rekenuren - Gratis | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - openbare IP-adressen  | IP-adresuren - openbare IP-adressen | IP-adresuren - Gratis | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Opslag (GB) - Cosmos DB | Opslag (GB) - Gratis | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 aanvraageenheden (uren) - Cosmos DB | 100 aanvraageenheden (uren) - Gratis | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| File Storage | Standaard-IO - bestanden (GB) - Lokaal redundant | Standaard-IO - bestanden (GB) - Gratis | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standaard-IO - eenheden voor bestanden lezen (per 10.000) | Standaard-IO - eenheden voor bestanden lezen (per 10.000) - Gratis | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standaard-IO - eenheden voor bestanden wegschrijven (per 10.000) | Standaard-IO - eenheden voor bestanden wegschrijven (per 10.000) - Gratis | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standaard-IO - eenheden voor bestandsprotocolbewerkingen (per 10.000) | Standaard-IO - eenheden voor bestandsprotocolbewerkingen (per 10.000) - Gratis | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standaard-IO - eenheden voor opvragen bestanden (per 10.000) | Standaard-IO - eenheden voor opvragen bestanden (per 10.000) - Gratis | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| 'Hot' blok-blob-opslag | Standaard-IO - leesbewerkingen 'hot' blok-blob (per 10.000) | Standaard-IO - leesbewerkingen 'hot' blok-blob (per 10.000) - Gratis |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| 'Hot' blok-blob-opslag | Standaard-IO -'hot' blok-blob (GB) - Lokaal redundant | Standaard-IO -'hot' blok-blob (GB) - Lokaal redundant - Gratis | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| 'Hot' blok-blob-opslag | Standaard-IO - schrijfbewerkingen 'hot' blok-blob (per 10.000) | Standaard-IO - schrijfbewerkingen 'hot' blok-blob (per 10.000) - Gratis | b34bbb76-edce-4c2d-a288-81a2db1fea53
| 'Hot' blok-blob-opslag  | Standaard-IO - schrijf-/leesbewerkingen 'hot' blok-blob (per 10.000) | Standaard-IO - schrijf-/leesbewerkingen 'hot' blok-blob (per 10.000) - Gratis | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Beheerde schijf <sup>1</sup>  | Standaard - beheerde schijven/momentopnamen (GB) - Lokaal redundant | Standaard - beheerde schijven/momentopnamen (GB) - Gratis | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Beheerde schijf <sup>1</sup>  | Standaard - beheerdeschijfbewerkingen (per 10.000) | Standaard - beheerdeschijfbewerkingen (per 10.000) - Gratis | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Beheerde schijf <sup>1</sup>  | Premium Storage - pagina-blob/P6 (eenheden) - Lokaal redundant | Premium Storage - pagina-blob/P6 (eenheden) - Gratis | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standaard - S0-databasedagen - SQL Database | Standaard S0-databasedagen - Gratis | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Gedeelde bandbreedte <sup>2</sup> | Uitgaande gegevensoverdracht (GB) | Uitgaande gegevensoverdracht (GB) - Gratis | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> Als u een virtuele Windows-machine maakt en beheerde schijf kiest, gebruikt u de meter voor beheerde schijven als onderdeel van de virtuele machine.

<sup>2</sup> Gedeelde meters kunnen worden gebruikt via meerdere services. Bijvoorbeeld: zowel virtuele machines als opslag genereert gebruik op basis van de meter Uitgaande gegevensoverdracht (GB).

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Voor vragen of hulp [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Uw abonnement bijwerken](billing-upgrade-azure-subscription.md)
